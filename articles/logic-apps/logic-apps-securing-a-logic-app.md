---
title: Proteggere l'accesso alle app per la logica di Azure | Documentazione Microsoft
description: Aggiungere la sicurezza per la protezione dell'accesso ai trigger, agli input e output, ai parametri di azione e ai servizi utilizzati con i flussi di lavoro nelle app per la logica di Azure.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0528d660f590e106f61729f10f8f68da3fe58cb7
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="secure-access-to-your-logic-apps"></a>Proteggere l'accesso alle app per la logica

Sono disponibili molti strumenti per proteggere un'app per la logica.

* Protezione dell'accesso per attivare un'app per la logica (trigger di una richiesta HTTP)
* Protezione dell'accesso per gestire, modificare o leggere un'app per la logica
* Protezione dell'accesso ai contenuti di input e output per un'esecuzione
* Protezione di parametri o input all'interno di azioni in un flusso di lavoro
* Protezione dell'accesso ai servizi che ricevono richieste da un flusso di lavoro

## <a name="secure-access-to-trigger"></a>Proteggere l'accesso al trigger

Quando si lavora con un'app per la logica che viene attivata con una richiesta HTTP ([richiesta](../connectors/connectors-native-reqres.md) o [webhook](../connectors/connectors-native-webhook.md)), è possibile limitare l'accesso in modo che possa essere attivata solo dai client autorizzati. Tutte le richieste in un'app per la logica vengono crittografate e protette tramite SSL.

### <a name="shared-access-signature"></a>Firma di accesso condiviso

Ogni endpoint di richiesta per un'app per la logica include una [firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) come parte dell'URL. Ogni URL contiene parametri di query `sp`, `sv` e `sig`. Le autorizzazioni vengono specificate da `sp` e corrispondono ai metodi HTTP consentiti, `sv` è la versione usata per generare e `sig` è il parametro usato per autenticare l'accesso al trigger. La firma viene generata attraverso l'algoritmo SHA256 con una chiave privata in tutti i percorsi e le proprietà dell'URL. La chiave privata non viene mai esposta né pubblicata, inoltre continua a essere crittografata e archiviata nell'ambito dell'app per la logica. L'app per la logica autorizza solo i trigger che contengono una firma valida creata con la chiave privata.

#### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

È possibile rigenerare in qualsiasi momento una nuova chiave protetta tramite il portale di Azure o API REST. Tutti gli URL correnti che sono stati generati in passato usando la chiave precedente verranno invalidati e non saranno più autorizzati ad attivare l'app per la logica.

1. Nel Portale di Azure aprire l'app per la logica per cui si desidera rigenerare una chiave.
1. Fare clic sulla voce di menu **Chiavi di accesso** in **Impostazioni**.
1. Scegliere la chiave da rigenerare e completare il processo.

Per accedere agli URL recuperati dopo la rigenerazione, è necessaria la nuova chiave di accesso.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Creazione di URL di callback con una data di scadenza

Se si condivide l'URL con altre parti, è possibile generare gli URL con chiavi e date di scadenza specifiche in base alle esigenze. Questo consente di eseguire perfettamente il roll delle chiavi o di verificare che l'accesso per attivare un'app sia limitato a un determinato intervallo di tempo. È possibile specificare una data di scadenza per un URL tramite l'[API REST delle app per la logica](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `NotAfter` come una stringa di data JSON, che restituisce un URL di callback valido solo fino a data e ora `NotAfter`.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Creazione di URL con una chiave privata primaria o secondaria

Quando si generano URL di callback per i trigger basati su richieste o si crea un elenco che li contiene, è anche possibile specificare la chiave da usare per firmare l'URL.  È possibile generare un URL firmato da una chiave specifica tramite l'[API REST delle app per la logica](https://docs.microsoft.com/rest/api/logic/workflowtriggers) come indicato di seguito:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `KeyType` come `Primary` o `Secondary`.  Viene restituito un URL firmato dalla chiave sicura specificata.

### <a name="restrict-incoming-ip-addresses"></a>Limitare gli indirizzi IP in ingresso

Oltre alla firma di accesso condiviso, si potrebbe voler limitare la chiamata a un'app per la logica solo da client specifici.  Ad esempio, se si gestisce l'endpoint tramite Gestione API di Azure, è possibile limitare l'app per la logica in modo che accetti solo le richieste provenienti dall'indirizzo IP dell'istanza di Gestione API.

Questa impostazione può essere configurata dalle impostazioni dell'app per la logica:

1. Nel Portale di Azure aprire l'app per la logica per cui si desidera aggiungere limitazioni per l'indirizzo IP.
1. Fare clic sulla voce di menu **Access control configuration** (Configurazione controllo di accesso) in **Impostazioni**.
1. Specificare l'elenco di intervalli di indirizzi IP che devono essere accettati dal trigger.

Un intervallo IP valido assume il formato `192.168.1.1/255`. Se si desidera che l'app per la logica venga attivata solo come app per la logica nidificata, selezionare l'opzione **Only other logic apps** (Solo altre app per la logica). L'opzione scrive una matrice vuota nella risorsa; ciò significa che vengono attivate correttamente solo le chiamate dal servizio stesso (app per la logica padre).

> [!NOTE]
> Un'app per la logica con un trigger di richiesta potrebbe comunque essere eseguita tramite l'API REST/Gestione API `/triggers/{triggerName}/run` indipendentemente dall'IP. In questo caso potrebbe essere richiesta l'autenticazione all'API REST di Azure e tutti gli eventi potrebbero essere visualizzati nel log di controllo di Azure. Impostare i criteri di controllo di accesso di conseguenza.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Impostazione degli intervalli IP nella definizione della risorsa

Se si usa un [modello di distribuzione](logic-apps-create-deploy-template.md) per automatizzare le distribuzioni, le impostazioni dell'intervallo IP possono essere configurate nel modello risorsa.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Aggiunta di Azure Active Directory, OAuth o altri standard di sicurezza

Per aggiungere altri protocolli di autorizzazione a un'app per la logica, [Gestione API di Azure](https://azure.microsoft.com/services/api-management/) offre monitoraggio avanzato, sicurezza, criteri e documentazione per ogni endpoint in grado di esporre un'app per la logica come un'API. Gestione API di Azure può esporre un endpoint privato o pubblico per l'app per la logica, che potrebbe sfruttare Azure Active Directory, certificati, OAuth o altri standard di sicurezza. Quando viene ricevuta una richiesta, Gestione API di Azure la inoltra all'app per la logica (eseguendo eventuali trasformazioni e restrizioni necessarie in transito). È possibile usare le impostazioni dell'intervallo IP in ingresso nell'app per la logica per consentire il trigger solo da Gestione API.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Proteggere l'accesso per gestire o modificare un'app per la logica

È possibile limitare l'accesso alle operazioni di gestione in un'app per la logica in modo che le operazioni sulla risorsa possano essere eseguite solo da utenti o gruppi specifici. Le app per la logica usano la funzionalità di [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) e possono essere personalizzate con gli stessi strumenti.  Esistono anche alcuni ruoli predefiniti a cui è possibile assegnare i membri della sottoscrizione:

* **Collaboratore per app per la logica**: offre l'accesso per visualizzare, modificare e aggiornare un'app per la logica.  Non è possibile rimuovere la risorsa o eseguire operazioni di amministrazione.
* **Operatore per app per la logica**: può visualizzare l'app per la logica e la cronologia delle esecuzioni e abilitare o disabilitare.  Non è possibile modificare o aggiornare la definizione.

È inoltre possibile usare i [blocchi per le risorse di Azure](../azure-resource-manager/resource-group-lock-resources.md) per impedire la modifica o l'eliminazione delle app per la logica. Questo risulta utile per evitare la modifica o l'eliminazione delle risorse di produzione.

## <a name="secure-access-to-contents-of-the-run-history"></a>Proteggere l'accesso ai contenuti della cronologia delle esecuzioni

È possibile limitare l'accesso ai contenuti di input o output dalle esecuzioni precedenti a intervalli di indirizzi IP specifici.  

Tutti i dati all'interno di un'esecuzione del flusso di lavoro è crittografata in transito e a riposo. Quando viene effettuata una chiamata alla cronologia delle esecuzioni, il servizio autentica la richiesta e offre collegamenti alla richiesta, oltre a input e output di risposta. Questo collegamento può essere protetto in modo che i contenuti vengano restituiti solo dalle richieste di visualizzare contenuto da un intervallo di indirizzi IP designato. È possibile usare questa funzionalità per il controllo di accesso. È anche possibile specificare un indirizzo IP come `0.0.0.0` in modo che nessuno possa accedere a input e output. Questa restrizione può essere rimossa solo da qualcuno con autorizzazioni di amministratore per offrire l'accesso "just-in-time" ai contenuti del flusso di lavoro.

Questa impostazione può essere configurata nelle impostazioni della risorsa del Portale di Azure:

1. Nel Portale di Azure aprire l'app per la logica per cui si desidera aggiungere limitazioni per l'indirizzo IP.
1. Fare clic sulla voce di menu **Access control configuration** (Configurazione controllo di accesso) in **Impostazioni**.
1. Specificare l'elenco di intervalli di indirizzi IP per l'accesso al contenuto

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Impostazione degli intervalli IP nella definizione della risorsa

Se si usa un [modello di distribuzione](logic-apps-create-deploy-template.md) per automatizzare le distribuzioni, le impostazioni dell'intervallo IP possono essere configurate nel modello risorsa.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Proteggere i parametri e gli input all'interno di un flusso di lavoro

Esistono alcuni aspetti della definizione di un flusso di lavoro per cui potrebbe essere opportuno creare parametri per la distribuzione nei diversi ambienti. Inoltre, alcuni di questi parametri potrebbero essere parametri protetti da non visualizzare durante la modifica di un flusso di lavoro, ad esempio un ID client e un segreto client per [l'autenticazione di Azure Active Directory](../connectors/connectors-native-http.md#authentication) di un'azione HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Uso di parametri e parametri protetti

Il [linguaggio di definizione del flusso di lavoro](http://aka.ms/logicappsdocs) assicura un'operazione `@parameters()` per accedere al valore di un parametro di risorsa in fase di runtime. È inoltre possibile [specificare parametri nel modello di distribuzione risorse](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Se si specifica un tipo di parametro come `securestring`, questo non verrà restituito con il resto della definizione della risorsa, ovvero non sarà accessibile visualizzando la risorsa dopo la distribuzione.

> [!NOTE]
> Se il parametro viene usato nelle intestazioni o nel corpo della richiesta, potrebbe essere visibile accedendo alla cronologia delle esecuzioni e alla richiesta HTTP in uscita. Assicurarsi di configurare di conseguenza i criteri di accesso ai contenuti.
> Le intestazioni di autorizzazione non sono mai visibili tramite input o output. Pertanto, se usato in tale contesto, il segreto non è recuperabile.

#### <a name="resource-deployment-template-with-secrets"></a>Modello di distribuzione risorse con segreti

Di seguito è riportato un esempio di distribuzione che fa riferimento a un parametro protetto di `secret` in fase di runtime. In un file di parametri separato è possibile specificare il valore dell'ambiente per `secret` oppure sfruttare l'[insieme di credenziali delle chiavi di Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md) per recuperare i segreti in fase di distribuzione.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
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
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Proteggere l'accesso ai servizi che ricevono richieste da un flusso di lavoro

Esistono diversi modi per proteggere qualsiasi endpoint a cui l'app per la logica deve accedere.

### <a name="using-authentication-on-outbound-requests"></a>Uso dell'autenticazione nelle richieste in uscita

Quando si lavora con un'azione HTTP, HTTP + Swagger (Open API) o Webhook, è possibile aggiungere l'autenticazione alla richiesta inviata. Potrebbe trattarsi di autenticazione di base, autenticazione del certificato o l'autenticazione di Azure Active Directory. Informazioni dettagliate su come configurare l'autenticazione sono disponibili [in questo articolo](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Limitazione dell'accesso agli indirizzi IP delle app per la logica

Tutte le chiamate dalle app per la logica provengono da un set specifico di indirizzi IP in base all'area. È possibile aggiungere altri filtri in modo da accettare solo richieste da tali indirizzi IP designati. Per un elenco di indirizzi IP, vedere [Limiti e configurazione delle app per la logica](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Connettività locale

Le app per la logica offrono integrazione con numerosi servizi per garantire una comunicazione locale sicura e affidabile.

#### <a name="on-premises-data-gateway"></a>Gateway dati locale

Molti dei connettori gestiti delle app per la logica offrono una connettività sicura a sistemi locali, tra cui File System, SQL, SharePoint, DB2 e altro ancora. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni sul [funzionamento del gateway dati](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Gestione API di Azure

[Gestione API di Azure](https://azure.microsoft.com/services/api-management/) dispone di numerose opzioni di connettività locale, tra cui l'integrazione ExpressRoute e VPN da sito a sito per la protezione del proxy e la comunicazione con i sistemi locali. Nella finestra di progettazione delle app per la logica è possibile selezionare rapidamente un'API esposta da Gestione API di Azure all'interno di un flusso di lavoro, in modo da offrire accesso rapido ai sistemi locali.

#### <a name="hybrid-connections-from-azure-app-service"></a>Connessioni ibride dai servizi app di Azure

È possibile usare la funzionalità di connessione ibrida locale per consentire la comunicazione tra l'API di Azure e le App Web.  Informazioni dettagliate sulle connessioni ibride e su come configurarle sono disponibili [in questo articolo](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="next-steps"></a>Passaggi successivi
[Creare un modello di distribuzione](logic-apps-create-deploy-template.md)  
[Gestione delle eccezioni](logic-apps-exception-handling.md)  
[Monitorare le app per la logica](logic-apps-monitor-your-logic-apps.md)  
[Diagnosi degli errori delle app per la logica](logic-apps-diagnosing-failures.md)  

