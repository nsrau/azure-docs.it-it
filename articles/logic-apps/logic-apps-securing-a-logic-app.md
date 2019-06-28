---
title: Proteggere l'accesso alle App per la logica di Azure
description: Aggiungere sicurezza alle App per la logica di Azure, inclusi trigger, input e output, parametri e altri servizi
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 65369e5747f17c0e420d5775f1c5704cac9ca21b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341095"
---
# <a name="secure-access-in-azure-logic-apps"></a>Proteggere l'accesso alle App per la logica di Azure

Ecco gli elementi nell'app per la logica con cui è possibile proteggere l'accesso:

* [Trigger di richiesta o Webhook](#secure-triggers)
* [Operazioni quali gestione, modifica o visualizzazione](#secure-operations) dell'app per la logica
* [Input e output](#secure-run-history) dalla cronologia di esecuzione dell'app per la logica
* [Input e parametri azione](#secure-action-parameters)
* [Servizi che ottengono richieste](#secure-requests) dall'app per la logica

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Proteggere l'accesso ai trigger di richiesta

Quando l'app per la logica usa un trigger basato sulla richiesta HTTP, ad esempio i trigger [Richiesta](../connectors/connectors-native-reqres.md) o [Webhook](../connectors/connectors-native-webhook.md), è possibile limitare l'accesso in modo che solo i client autorizzati possano avviarla. Tutte le richieste ricevute da un'app per la logica vengono crittografate e protette con il protocollo Secure Sockets Layer (SSL). Di seguito sono elencati i diversi modi in cui è possibile proteggere l'accesso a questo tipo di trigger:

* [Generare firme di accesso condiviso](#sas)
* [Limitare gli indirizzi IP in ingresso](#restrict-incoming-ip-addresses)
* [Aggiungere Azure Active Directory, OAuth o altri standard di sicurezza](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Generare firme di accesso condiviso

Ogni endpoint di richiesta a un'app per la logica include una [firma di accesso condiviso (Shared Access Signature - SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) come parte dell'URL. Ogni URL contiene parametri di query `sp`, `sv` e `sig`:

* `sp` specifica le autorizzazioni che eseguono il mapping ai metodi HTTP consentiti per l'uso.
* `sv` specifica la versione usata per generare la firma.
* `sig` viene usato per autenticare l'accesso al trigger.

L'algoritmo SHA256 genera la firma con una chiave di accesso privata in tutti i percorsi e le proprietà dell'URL. La chiave privata non viene mai esposta né pubblicata, inoltre continua a essere crittografata e archiviata nell'ambito dell'app per la logica. L'app per la logica autorizza solo i trigger che contengono una firma valida creata con la chiave privata. 

Di seguito altre informazioni, sulla protezione dell'accesso con firma di accesso condiviso:

* [Per rigenerare le chiavi di accesso](#access-keys)
* [Creare URL di callback in scadenza](#expiring-urls)
* [Creare URL con una chiave primaria o secondaria](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

È possibile rigenerare in qualsiasi momento una nuova chiave di accesso protetta tramite il portale di Azure o API REST. Tutti gli URL generati in precedenza usando la chiave precedente verranno invalidati e non saranno più autorizzati ad attivare l'app per la logica. Per accedere agli URL recuperati dopo la rigenerazione, è necessaria la nuova chiave di accesso.

1. Nel portale di Azure aprire l'app per la logica che contiene la chiave che si desidera rigenerare.

1. Nel menu di app per la logica in **Impostazioni**, selezionare **Chiave di accesso**.

1. Scegliere la chiave da rigenerare e completare il processo.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Creazione di URL di callback con una data di scadenza

Se si condivide l'URL dell'endpoint di un trigger basato su richiesta con altre parti è possibile generare gli URL di callback con chiavi e date di scadenza, in base alla necessità. È quindi possibile aggiornare le chiavi senza problemi o limitare l'accesso per attivare l'app per la logica all'interno di un determinato lasso di tempo. È possibile specificare una data di scadenza per un URL tramite l'[API REST delle app per la logica](https://docs.microsoft.com/rest/api/logic/workflowtriggers), ad esempio:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la `NotAfter`proprietà usando una stringa di data JSON. Questa proprietà restituisce un URL di callback valido solo fino a data e ora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Creare URL con una chiave privata primaria o secondaria

Quando si generano URL di callback per i trigger basati su richieste o si crea un elenco che li contiene, è anche possibile specificare la chiave da usare per firmare l'URL. È possibile generare un URL firmato da una chiave specifica tramite l'[API REST delle app per la logica](https://docs.microsoft.com/rest/api/logic/workflowtriggers), ad esempio:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `KeyType` come `Primary` o `Secondary`. Questa proprietà restituisce un URL firmato dalla chiave sicura specificata.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Limitare gli indirizzi IP in ingresso

Oltre alla firma di accesso condiviso è possibile che l'utente voglia limitare client specifici che possono chiamare l'app per la logica.  
Ad esempio, se si gestisce l'endpoint di richiesta tramite Gestione API di Azure, è possibile limitare l'app per la logica in modo che accetti solo le richieste provenienti dall'indirizzo IP dell'istanza di Gestione API. 

#### <a name="set-ip-ranges---azure-portal"></a>Impostare gli intervalli IP - portale di Azure

Per configurare questa restrizione nel portale di Azure passare alle impostazioni dell'app per la logica: 

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica. 

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso (Configurazione del controllo di accesso)**  > 
**Allowed inbound IP addresses (Indirizzi IP in ingresso consentiti)** , selezionare **Specific IP ranges (Intervalli IP specifici)** .

1. In **IP ranges for triggers (Intervalli di IP per i trigger)** , specificare gli intervalli di indirizzi IP accettati dal trigger. Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x* 

Se si vuole che l'app per la logica venga generata solo come app per la logica annidata, dall'elenco **Allowed inbound IP addresses (Indirizzi IP in ingresso consentiti)** , selezionare **Only other Logic App (Solo altre app per la logica)** . Questa opzione scrive una matrice vuota nella risorsa di app per la logica, in modo che solo le chiamate dal servizio di App per la logica (app per la logica padre) possano attivare l'app per la logica annidata.

> [!NOTE]
> Indipendentemente dall'indirizzo IP, è comunque possibile eseguire un'app per la logica che includa un trigger basato su richiesta usando `/triggers/{triggerName}/run` tramite l'API REST di Azure o tramite Gestione API. Tuttavia, in questo caso potrebbe essere richiesta l'autenticazione all'API REST di Azure e tutti gli eventi potrebbero essere visualizzati nel log di controllo di Azure. Assicurarsi di impostare i criteri di controllo di accesso di conseguenza.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Impostare gli intervalli IP - modello di distribuzione di app per la logica

Se si stanno automatizzando le distribuzioni di app per la logica usando un [modello di distribuzione Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), è possibile impostare gli intervalli IP in quel modello, ad esempio:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Aggiungere Azure Active Directory, OAuth o altri standard di sicurezza

Per aggiungere altri protocolli di autorizzazione all'app per la logica,considerare l'uso di [Gestione API di Azure](https://azure.microsoft.com/services/api-management/). Questo servizio offre monitoraggio avanzato, sicurezza, criteri e documentazione per ogni endpoint e offre la possibilità di esporre l'app per la logica come API. Gestione API di Azure può esporre un endpoint privato o pubblico per l'app per la logica, che potrebbe sfruttare Azure Active Directory, certificati, OAuth o altri standard di sicurezza. Quando Gestione API riceve una richiesta, il servizio invia la richiesta all'app per la logica, eseguendo anche eventuali trasformazioni necessarie e restrizioni. Per consentire solo a Gestione API di attivare l'app per la logica, è possibile usare le impostazioni dell'intervallo IP in ingresso dell'app per la logica. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Proteggere l'accesso alle operazioni di app per la logica

Per consentire solo a utenti o gruppi specifici di eseguire operazioni sull'app per la logica è possibile limitare l'accesso alle attività, ad esempio la gestione, la modifica e la visualizzazione. App per la logica supporta il [controllo degli accessi in base al ruolo di Azure (RBAC)](../role-based-access-control/role-assignments-portal.md), che è possibile personalizzare o assegnare ruoli predefiniti a membri nella sottoscrizione, ad esempio:

* [Collaboratore per app per la logica](../role-based-access-control/built-in-roles.md#logic-app-contributor): Consente gestire le App per la logica, ma non è possibile modificare l'accesso a tali.

* [Operatore per app per la logica](../role-based-access-control/built-in-roles.md#logic-app-operator): Consente di leggere, abilitare e disabilitare l'App per la logica, ma è possibile modificare o aggiornarli.

Per impedire la modifica o l'eliminazione delle app per la logica da parte di altri utenti è inoltre possibile usare i [blocchi per le risorse di Azure](../azure-resource-manager/resource-group-lock-resources.md). Questa funzionalità consente di impedire ad altri utenti di modificare o eliminare le risorse di produzione.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Proteggere l'accesso alla cronologia di esecuzione dell'app per la logica

Per proteggere i contenuti passati come input o output da precedenti esecuzioni di app per la logica, è possibile limitare l'accesso a specifici intervalli di indirizzi IP. Questa funzionalità offre un maggiore controllo di accesso. Tutti i dati in esecuzione in un'app per la logica vengono crittografati durante il transito e mentre sono inattivi. Quando si richiede la cronologia di esecuzione di un'app per la logica, App per la logica autentica la richiesta e fornisce collegamenti agli input e agli output delle richieste e delle risposte nel flusso di lavoro dell'app per la logica. È possibile proteggere questi collegamenti, in modo che solo le richieste da uno specifico indirizzo IP restituiscano quel contenuto. Ad esempio, si potrebbe anche specificare un indirizzo IP, ad esempio `0.0.0.0-0.0.0.0` in modo che nessuno possa accedere agli input e agli output. Questa restrizione può essere rimossa solo da una persona con autorizzazioni di amministratore per offrire l'accesso "just-in-time" ai contenuti dell'app per la logica.

### <a name="set-ip-ranges---azure-portal"></a>Impostare gli intervalli IP - portale di Azure

Per configurare questa restrizione nel portale di Azure passare alle impostazioni dell'app per la logica:

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica. 

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso** > 
   **Indirizzi IP in ingresso consentiti** selezionare **Intervalli IP specifici**.

1. In **Intervalli IP per i contenuti**, specificare gli intervalli di indirizzi IP che possono accedere al contenuto da input e output. 
   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Impostare gli intervalli IP - modello di distribuzione di app per la logica

Se si stanno automatizzando le distribuzioni di app per la logica usando un [modello di distribuzione Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), è possibile impostare gli intervalli IP in quel modello, ad esempio:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Proteggere input e parametri azione

Quando si esegue la distribuzione in diversi ambienti, è possibile impostare come parametri elementi specifici nella definizione del flusso di lavoro dell'app per la logica. In questo modo è possibile fornire input in base agli ambienti in uso e proteggere le informazioni riservate. Ad esempio, per autenticare azioni HTTP con [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), definire e proteggere i parametri che accettano l'ID client e il segreto client usati per l'autenticazione. La definizione dell'app per la logica contiene la sezione `parameters` per questi parametri.
Per accedere ai valori dei parametri durante il runtime, si può usare l'espressione `@parameters('parameterName')` fornita dal [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs). 

Per proteggere i parametri e i valori che non si vuole mostrare quando si modifica l'app per la logica o si visualizza la cronologia di esecuzione, è possibile definire i parametri con il tipo `securestring` e usare la codifica in base alle esigenze. I parametri con questo tipo non vengono restituiti con la definizione della risorsa e non sono accessibili quando si visualizza la risorsa dopo la distribuzione.

> [!NOTE]
> Se si usa un parametro nelle intestazioni o nel corpo di una richiesta, tale parametro potrebbe essere visibile quando si accede alla cronologia di esecuzione dell'app per la logica e alla richiesta HTTP in uscita. Assicurarsi inoltre di configurare di conseguenza i criteri di accesso ai contenuti.
> Le intestazioni di autorizzazione non sono mai visibili tramite input o output. Se quindi viene usato un segreto, questo non sarà recuperabile.

Per altre informazioni sulla protezione dei parametri nelle definizioni dell'app per la logica, vedere [Proteggere i parametri nelle definizioni dell'app per la logica](#secure-parameters-workflow) più avanti in questa pagina.

Se le distribuzioni sono automatizzate tramite i [modelli di distribuzione di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), in questi modelli si possono usare anche i parametri protetti. Ad esempio, è possibile usare parametri per ottenere i segreti KeyVault durante la creazione dell'app per la logica. La definizione del modello di distribuzione contiene la propria sezione `parameters`, separata dalla sezione `parameters` dell'app per la logica. Per altre informazioni sulla protezione dei parametri nei modelli di distribuzione, vedere [Proteggere i parametri nei modelli di distribuzione di Azure Resource Manager](#secure-parameters-deployment-template) più avanti in questa pagina.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Proteggere i parametri nelle definizioni dell'app per la logica

Per proteggere le informazioni riservate nella definizione del flusso di lavoro dell'app per la logica, usare i parametri protetti per fare in modo che queste informazioni non siano visibili dopo il salvataggio dell'app per la logica. Si supponga ad esempio di usare l'autenticazione `Basic` in una definizione di azione HTTP. Questo esempio include una sezione `parameters` che definisce i parametri per la definizione dell'azione e una sezione `authentication` che accetta i valori dei parametri `username` e `password`. Per fornire valori per questi parametri, è possibile usare un file di parametri distinto, ad esempio:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

Se si usano i segreti, è possibile ottenerli in fase di distribuzione usando [Azure Resource Manager Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Proteggere i parametri nei modelli di distribuzione di Azure Resource Manager

Questo esempio illustra un modello di distribuzione di Resource Manager che usa più parametri di runtime con il tipo `securestring`:

* `armTemplatePasswordParam`, che è l'input per il parametro `logicAppWfParam` di definizione dell'app per la logica

* `logicAppWfParam`, che esegue l'input per l'azione HTTP usando l'autenticazione di base

Questo esempio include una sezione interna `parameters`, che appartiene alla definizione del flusso di lavoro dell'app per la logica, e una sezione esterna `parameters`, che appartiene al modello di distribuzione. Per specificare i valori di ambiente per i parametri, è possibile usare un file di parametri distinto. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Se si usano i segreti, è possibile ottenerli in fase di distribuzione usando [Azure Resource Manager Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Proteggere l'accesso ai servizi che ricevono richieste

Ecco alcuni modi per proteggere qualsiasi endpoint in cui l'app per la logica deve accedere e inviare le richieste.

### <a name="add-authentication-on-outbound-requests"></a>Aggiunta dell'autenticazione nelle richieste in uscita

Quando si lavora con un'azione HTTP, HTTP + Swagger (Open API) o Webhook, è possibile aggiungere l'autenticazione alla richiesta inviata dall'app per la logica. Potrebbe trattarsi di autenticazione di base, autenticazione del certificato o l'autenticazione di Azure Active Directory. Per altre informazioni, vedere [Autenticare i trigger o le azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Limitazione dell'accesso agli indirizzi IP delle app per la logica

Tutte le chiamate dalle app per la logica provengono da un set specifico di indirizzi IP in base all'area. È possibile aggiungere un filtro che accetti le richieste solo da tali indirizzi IP. Per un elenco di indirizzi IP, vedere [Limits and configuration for Azure Logic Apps (Limiti e configurazione delle app per la logica)](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Proteggere la connettività locale

Le app per la logica offrono integrazione con tali servizi per garantire una comunicazione locale sicura e affidabile.

#### <a name="on-premises-data-gateway"></a>Gateway dati locale

Molti dei connettori gestiti da App per la logica di Azure offrono una connettività sicura a sistemi locali, tra cui File System, SQL, SharePoint, DB2 e altro ancora. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni su [come funziona il gateway dati locale](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Gestione API di Azure

[Gestione API di Azure](https://azure.microsoft.com/services/api-management/) dispone di numerose opzioni di connettività locale, tra cui l'integrazione ExpressRoute e per rete privata locale da sito a sito per la protezione del proxy e la comunicazione con i sistemi locali. Nella finestra di Progettazione di App per la logica è possibile selezionare rapidamente un'API esposta da Gestione API di Azure all'interno di un flusso di lavoro, in modo da offrire accesso rapido ai sistemi locali.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un modello di distribuzione](logic-apps-create-deploy-template.md)  
* [Gestione delle eccezioni](logic-apps-exception-handling.md)  
* [Monitorare le app per la logica](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosi degli errori delle app per la logica](logic-apps-diagnosing-failures.md)  
