---
title: Proteggere l'accesso ai dati
description: Accesso sicuro a input, output, trigger basati su richiesta, cronologia di esecuzione, attività di gestione e accesso ad altre risorse in App per la logica di AzureSecure access to inputs, outputs, request-based triggers, run history, management tasks, and access to other resources in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657171"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Accesso sicuro e dati nelle app per la logica di AzureSecure access and data in Azure Logic Apps

Per controllare l'accesso e proteggere i dati in App per la logica di Azure, è possibile configurare la sicurezza nelle aree seguenti:To control access and protect data in Azure Logic Apps, you can set up security in these areas:

* [Accesso ai trigger basati su richiesta](#secure-triggers)
* [Accesso alle operazioni dell'app per la logicaAccess to logic app operations](#secure-operations)
* [Accesso agli input e agli output della cronologia di esecuzione](#secure-run-history)
* [Accesso agli input dei parametri](#secure-action-parameters)
* [Accesso a servizi e sistemi chiamati da app per la logica](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Accesso ai trigger basati su richiesta

Se l'app per la logica usa un trigger basato su richiesta, che riceve chiamate o richieste in arrivo, ad esempio il trigger [Richiesta](../connectors/connectors-native-reqres.md) o [Webhook,](../connectors/connectors-native-webhook.md) è possibile limitare l'accesso in modo che solo i client autorizzati possano chiamare l'app per la logica. Tutte le richieste ricevute da un'app per la logica vengono crittografate e protette con Transport Layer Security (TLS), precedentemente noto come protocollo SSL (Secure Sockets Layer).

Di seguito sono riportate le opzioni che consentono di proteggere l'accesso a questo tipo di trigger:Here are options that can help you secure access to this trigger type:

* [Generare firme di accesso condivise](#sas)
* [Limitare gli indirizzi IP in ingresso](#restrict-inbound-ip-addresses)
* [Aggiungere OAuth di Azure Active Directory o un'altra sicurezzaAdd Azure Active Directory OAuth or other security](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generare firme di accesso condiviso

Ogni endpoint di richiesta in un'app per la logica ha una firma di accesso condiviso (SAS) nell'URL dell'endpoint, che segue questo formato:Every request endpoint on a logic app has a [Shared Access Signature (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) in the endpoint's URL, which follows this format:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Ogni URL `sp`contiene `sv`il `sig` parametro di query , e come descritto nella tabella seguente:

| Query parameter (Parametro di query) | Descrizione |
|-----------------|-------------|
| `sp` | Specifica le autorizzazioni per i metodi HTTP consentiti da utilizzare. |
| `sv` | Specifica la versione SAS da utilizzare per generare la firma. |
| `sig` | Specifica la firma da utilizzare per l'autenticazione dell'accesso al trigger. Questa firma viene generata utilizzando l'algoritmo SHA256 con una chiave di accesso segreta in tutti i percorsi URL e le proprietà. Mai esposta o pubblicata, questa chiave viene mantenuta crittografata e archiviata con l'app per la logica. L'app per la logica autorizza solo i trigger che contengono una firma valida creata con la chiave privata. |
|||

Per altre informazioni sulla protezione dell'accesso con la sAS, vedere queste sezioni in questo argomento:For more information about securing access with SAS, see these sections in this topic:

* [Per rigenerare le chiavi di accesso](#access-keys)
* [Creare URL di callback in scadenza](#expiring-urls)
* [Creare URL con una chiave primaria o secondaria](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

Per generare una nuova chiave di accesso di sicurezza in qualsiasi momento, usare l'API REST di Azure o il portale di Azure.To generate a new security access key at any time, use the Azure REST API or Azure portal. Tutti gli URL generati in precedenza che usano la chiave precedente vengono invalidati e non dispongono più dell'autorizzazione per attivare l'app per la logica. Gli URL recuperati dopo la rigenerazione vengono firmati con la nuova chiave di accesso.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica con la chiave che si vuole rigenerare.

1. Nel menu di app per la logica in **Impostazioni**, selezionare **Chiave di accesso**.

1. Selezionare la chiave che si desidera rigenerare e completare il processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Creare URL di callback in scadenza

Se si condivide l'URL dell'endpoint per un trigger basato su richiesta con altre parti, è possibile generare URL di callback che utilizzano chiavi specifiche e hanno date di scadenza. In questo modo, puoi eseguire senza problemi le chiavi o limitare l'accesso all'attivazione dell'app per la logica in base a un intervallo di tempo specifico. Per specificare una data di scadenza per un URL, usare l'API REST App per la [logica,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)ad esempio:To specify an expiration date for a URL, use the Logic Apps REST API , for example:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere `NotAfter`la proprietà usando una stringa di data JSON. Questa proprietà restituisce un URL di callback valido solo fino a data e ora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Creare URL con una chiave privata primaria o secondaria

Quando si generano o elencano URL di callback per un trigger basato su richiesta, è possibile specificare la chiave da utilizzare per firmare l'URL. Per generare un URL firmato da una chiave specifica, usare l'API REST App per la [logica,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)ad esempio:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `KeyType` come `Primary` o `Secondary`. Questa proprietà restituisce un URL firmato dalla chiave di sicurezza specificata.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Limitare gli indirizzi IP in ingresso

Insieme alla firma di accesso condiviso, è possibile limitare in modo specifico i client che possono chiamare l'app per la logica. Ad esempio, se si gestisce l'endpoint di richiesta tramite Gestione API di Azure, è possibile limitare l'app per la logica per accettare le richieste solo dall'indirizzo IP per l'istanza di Gestione API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Limitare gli intervalli IP in ingresso nel portale di AzureRestrict inbound IP ranges in Azure portal

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione** > controllo di accesso**Indirizzi IP in ingresso**, selezionare **Intervalli IP specifici**.

1. In **IP ranges for triggers (Intervalli di IP per i trigger)**, specificare gli intervalli di indirizzi IP accettati dal trigger.

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

Se si vuole che l'app per la logica venga attivata solo come app per la logica annidata, nell'elenco **Indirizzi IP in ingresso consentiti** selezionare Solo altre app per la **logica**. Questa opzione scrive una matrice vuota nella risorsa dell'app per la logica. In questo modo, solo le chiamate dal servizio App per la logica (app per la logica padre) possono attivare l'app per la logica annidata.

> [!NOTE]
> Indipendentemente dall'indirizzo IP, è comunque possibile eseguire un'app per la logica che includa un trigger basato su richiesta usando `/triggers/<trigger-name>/run` tramite l'API REST di Azure o tramite Gestione API. Tuttavia, questo scenario richiede ancora l'autenticazione con l'API REST di Azure.However, this scenario still requires authentication against the Azure REST API. Tutti gli eventi vengono visualizzati nel log di controllo di Azure.All events appear in the Azure Audit Log. Assicurarsi di impostare di conseguenza i criteri di controllo di accesso.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli IP in ingresso nel modello di Azure Resource ManagerRestrict inbound IP ranges in Azure Resource Manager template

Se si automatizza la distribuzione per le app per la logica `accessControl` usando i `triggers` modelli di [Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile specificare gli intervalli IP usando la sezione con la sezione nella definizione di risorsa dell'app per la logica, ad esempio:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Aggiungere OAuth di Azure Active Directory o un'altra sicurezzaAdd Azure Active Directory OAuth or other security

Per aggiungere altri protocolli di autorizzazione all'app per la logica, è consigliabile usare il servizio Gestione API di [Azure.To](../api-management/api-management-key-concepts.md) add more authorization protocols to your logic app, consider using the Azure API Management service. Questo servizio consente di esporre l'app per la logica come un'API e offre monitoraggio avanzato, sicurezza, criteri e documentazione per qualsiasi endpoint. Gestione API può esporre un endpoint pubblico o privato per l'app per la logica. Per autorizzare l'accesso a questo endpoint, è possibile usare [OAuth](#azure-active-directory-oauth-authentication)di Azure Active Directory , [certificato client](#client-certificate-authentication)o altri standard di sicurezza per autorizzare l'accesso a tale endpoint. Quando Gestione API riceve una richiesta, il servizio invia la richiesta all'app per la logica, eseguendo anche eventuali trasformazioni necessarie e restrizioni. Per consentire solo a Gestione API di attivare l'app per la logica, è possibile usare le impostazioni dell'intervallo IP in ingresso dell'app per la logica.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Accesso alle operazioni dell'app per la logicaAccess to logic app operations

È possibile consentire l'esecuzione di attività specifiche, ad esempio la gestione, la modifica e la visualizzazione di app per la logica, ad esempio utenti o gruppi specifici. Per controllare le autorizzazioni, usare il controllo degli accessi in base al ruolo di Azure in modo da poter assegnare ruoli personalizzati o predefiniti ai membri della sottoscrizione di Azure:To control their permissions, use [Azure Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) so that you can assign customized or built-in roles to the members in your Azure subscription:

* [Collaboratore app per](../role-based-access-control/built-in-roles.md#logic-app-contributor)la logica: consente di gestire le app per la logica, ma non è possibile modificarne l'accesso.

* [Operatore app per](../role-based-access-control/built-in-roles.md#logic-app-operator)la logica: consente di leggere, abilitare e disabilitare le app per la logica, ma non è possibile modificarle o aggiornarle.

Per impedire la modifica o l'eliminazione delle app per la logica da parte di altri utenti è inoltre possibile usare i [blocchi per le risorse di Azure](../azure-resource-manager/management/lock-resources.md). Questa funzionalità impedisce ad altri utenti di modificare o eliminare le risorse di produzione.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Accesso ai dati della cronologia di esecuzione

Durante l'esecuzione di un'app per la logica, tutti i dati [vengono crittografati durante](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) il transito usando Transport Layer Security (TLS) e [inattivi.](../security/fundamentals/encryption-atrest.md) Al termine dell'esecuzione dell'app per la logica, è possibile visualizzare la cronologia per l'esecuzione, inclusi i passaggi eseguiti insieme allo stato, alla durata, agli input e agli output per ogni azione. Questo dettaglio dettagliato fornisce informazioni dettagliate su come è stata eseguita l'app per la logica e su dove è possibile avviare la risoluzione dei problemi che si verificano.

Quando si visualizza la cronologia di esecuzione dell'app per la logica, app per la logica autentica l'accesso e quindi fornisce collegamenti agli input e agli output per le richieste e le risposte per ogni esecuzione. Tuttavia, per le azioni che gestiscono password, segreti, chiavi o altre informazioni riservate, si desidera impedire ad altri utenti di visualizzare e accedere a tali dati. Ad esempio, se l'app per la logica riceve un segreto dall'insieme di credenziali delle chiavi di [Azure](../key-vault/key-vault-overview.md) da usare durante l'autenticazione di un'azione HTTP, si vuole nascondere tale segreto dalla visualizzazione.

Per controllare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, sono disponibili le opzioni seguenti:To control access to the inputs and outputs in your logic app's run history, you have these options:

* [Limitare l'accesso in base all'intervallo di indirizzi IP.](#restrict-ip)

  Questa opzione consente di proteggere l'accesso alla cronologia di esecuzione in base alle richieste provenienti da un intervallo di indirizzi IP specifico.

* Nascondere i dati dalla cronologia di [esecuzione utilizzando l'offuscamento](#obfuscate).

  In molti trigger e azioni è possibile nascondere i relativi input, output o entrambi dalla cronologia di esecuzione di un'app per la logica.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Limitare l'accesso in base all'intervallo di indirizzi IP

Puoi limitare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica in modo che solo le richieste provenienti da intervalli di indirizzi IP specifici possano visualizzare tali dati. Ad esempio, per impedire a chiunque di accedere a input e `0.0.0.0-0.0.0.0`output, specificare un intervallo di indirizzi IP, ad esempio . Solo una persona con autorizzazioni di amministratore può rimuovere questa restrizione, che offre la possibilità di accedere "just-in-time" ai dati dell'app per la logica. È possibile specificare gli intervalli IP da limitare usando il portale di Azure o in un modello di Azure Resource Manager usato per la distribuzione di app per la logica.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Limitare gli intervalli IP nel portale di AzureRestrict IP ranges in Azure portal

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione** > controllo di accesso**Indirizzi IP in ingresso**, selezionare **Intervalli IP specifici**.

1. In **Intervalli IP per i contenuti**, specificare gli intervalli di indirizzi IP che possono accedere al contenuto da input e output. 

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli IP nel modello di Azure Resource ManagerRestrict IP ranges in Azure Resource Manager template

Se si automatizza la distribuzione per le app per la logica `accessControl` usando i `contents` modelli di [Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile specificare gli intervalli IP usando la sezione con la sezione nella definizione di risorsa dell'app per la logica, ad esempio:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Nascondere i dati dalla cronologia di esecuzione utilizzando l'offuscamento

Molti trigger e azioni dispongono di impostazioni per nascondere input, output o entrambi dalla cronologia di esecuzione di un'app per la logica. Ecco alcune [considerazioni da esaminare](#obfuscation-considerations) quando si usano queste impostazioni per proteggere questi dati.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Nascondere input e output nella finestra di progettazioneHide inputs and outputs in the designer

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

   ![Aprire l'app per la logica in Progettazione app per la logicaOpen logic app in Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Nel trigger o nell'azione in cui si desidera nascondere i dati sensibili selezionare il pulsante con i puntini di sospensione (**...**) e quindi scegliere **Impostazioni**.

   ![Aprire le impostazioni di trigger o azione](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Attivare **Secure Inputs**, **Secure Outputs**o entrambi. Al termine, fare clic su **Fine**.

   ![Attivare "Ingressi sicuri" o "Uscite sicure"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   L'azione o il trigger ora mostra un'icona a otto nella barra del titolo.

   ![La barra del titolo dell'azione o del trigger mostra l'icona del lucchetto](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   I token che rappresentano gli output protetti delle azioni precedenti mostrano anche le icone di blocco. Ad esempio, quando si seleziona un output di questo tipo dall'elenco di contenuto dinamico da usare in un'azione, tale token mostra un'icona a chiave.

   ![Selezionare il token per l'output protettoSelect token for secured output](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Dopo l'esecuzione dell'app per la logica, è possibile visualizzare la cronologia per l'esecuzione.

   1. Nel riquadro **Panoramica** dell'app per la logica selezionare l'esecuzione che si desidera visualizzare.

   1. Nel riquadro **di esecuzione dell'app** per la logica espandere le azioni che si desidera esaminare.

      Se si sceglie di nascondere sia gli input che gli output, tali valori ora appaiono nascosti.

      ![Ingressi e output nascosti nella cronologia di esecuzione](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Nascondere input e output nella vista codice

Nella definizione del trigger o dell'azione sottostante, aggiungere o aggiornare la `runtimeConfiguration.secureData.properties` matrice con uno o entrambi questi valori:

* `"inputs"`: protegge gli input nella cronologia di esecuzione.
* `"outputs"`: protegge gli output nella cronologia di esecuzione.

Ecco alcune [considerazioni da esaminare](#obfuscation-considerations) quando si usano queste impostazioni per proteggere questi dati.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Considerazioni sull'occultamento di input e output

* Quando si nascondeno gli input o gli output in un trigger o un'azione, app per la logica non invia i dati protetti ad Azure Log Analytics.When you oscurcure s or outputs on a trigger or action, Logic Apps doesn't send the secured data to Azure Log Analytics. Inoltre, non è possibile aggiungere [proprietà rilevate](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) al trigger o all'azione per il monitoraggio.

* L'API App per la [logica per la gestione della cronologia del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/) non restituisce output protetti.

* Per nascondere gli output da un'azione che nasconde gli input o nasconde in modo esplicito gli output, attivare manualmente **Secure Outputs** in tale azione.

* Assicurarsi di attivare **Secure Inputs** o **Secure Outputs** nelle azioni downstream in cui si prevede che la cronologia di esecuzione oscuri i dati.

  **Impostazione Uscite sicure**

  Quando si attiva manualmente **Secure Outputs** in un trigger o un'azione, App per la logica protegge questi output nella cronologia di esecuzione. Se un'azione downstream usa in modo esplicito questi output protetti come input, App per la logica nasconde gli input di questa azione nella cronologia di esecuzione, ma *non abilita* l'impostazione **Input sicuri** dell'azione.

  ![Uscite protette come input e impatto a valle sulla maggior parte delle azioni](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Le azioni Componi, Analizza JSON e Risposta hanno solo l'impostazione **Input sicuri.** Quando è attivata, l'impostazione nasconde anche gli output di queste azioni. Se queste azioni usano in modo esplicito gli output protetti a monte come input, App per la logica nasconde gli input e gli output di queste azioni, ma *non abilita* l'impostazione **Input sicuri** di queste azioni. Se un'azione a valle usa in modo esplicito gli output nascosti dalle azioni Componi, Analizza JSON o Risposta come input, app per la logica *non nasconde gli input o gli output di questa azione downstream.*

  ![Uscite protette come input con impatto a valle su azioni specifiche](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Impostazione Ingressi sicuri**

  Quando si attivano manualmente **gli input protetti** in un trigger o un'azione, App per la logica protegge questi input nella cronologia di esecuzione. Se un'azione a valle usa in modo esplicito gli output visibili da tale trigger o azione come input, App per la logica nasconde gli input dell'azione a valle nella cronologia di esecuzione, ma *non abilita* **gli input sicuri** in questa azione e non nasconde gli output di questa azione.

  ![Ingressi protetti e impatto a valle sulla maggior parte delle azioni](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se le azioni Componi, Analizza JSON e Risposta usano in modo esplicito gli output visibili dal trigger o dall'azione con gli input protetti, App per la logica nasconde gli input e gli output di queste azioni, ma *non abilita* l'impostazione **Input sicuri** di queste azioni. Se un'azione a valle usa in modo esplicito gli output nascosti dalle azioni Componi, Analizza JSON o Risposta come input, app per la logica *non nasconde gli input o gli output di questa azione downstream.*

  ![Ingressi protetti e impatto a valle su azioni specifiche](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Accesso agli input dei parametri

Se si esegue la distribuzione in ambienti diversi, è consigliabile parametrizzare i valori nella definizione del flusso di lavoro che variano in base a tali ambienti. In questo modo, è possibile evitare dati hardcoded usando un modello di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) per distribuire l'app per la logica, proteggere i dati sensibili definendo i parametri protetti e passare tali dati come input separati tramite i parametri del [modello](../azure-resource-manager/templates/template-parameters.md) usando un file di [parametri.](../azure-resource-manager/templates/parameter-files.md)

Ad esempio, se si autenticano azioni HTTP con [OAuth di Azure Active Directory](#azure-active-directory-oauth-authentication), è possibile definire e nascondere i parametri che accettano l'ID client e il segreto client utilizzati per l'autenticazione. Per definire questi parametri nell'app `parameters` per la logica, usare la sezione nella definizione del flusso di lavoro dell'app per la logica e nel modello di Resource Manager per la distribuzione. Per nascondere i valori dei parametri che non si desidera visualizzare quando si modifica `securestring` `secureobject` l'app per la logica o si visualizza la cronologia di esecuzione, definire i parametri usando il tipo o e usare la codifica in base alle esigenze. I parametri con questo tipo non vengono restituiti con la definizione di risorsa e non sono accessibili quando si visualizza la risorsa dopo la distribuzione. Per accedere a questi valori `@parameters('<parameter-name>')` di parametro durante il runtime, usare l'espressione all'interno della definizione del flusso di lavoro. Questa espressione viene valutata solo in fase di esecuzione ed è descritta dal linguaggio di [definizione](../logic-apps/logic-apps-workflow-definition-language.md)del flusso di lavoro .

> [!NOTE]
> Se usi un parametro in un'intestazione o nel corpo di una richiesta, tale parametro potrebbe essere visibile quando visualizzi la cronologia di esecuzione dell'app per la logica e la richiesta HTTP in uscita. Assicurarsi di impostare anche i criteri di accesso al contenuto di conseguenza. È inoltre possibile utilizzare [l'offuscamento](#obfuscate) per nascondere input e output nella cronologia di esecuzione. Le intestazioni di autorizzazione non sono mai visibili tramite input o output. Se quindi viene usato un segreto, questo non sarà recuperabile.

Per altre informazioni, vedere queste sezioni in questo argomento:For more information, see these sections in this topic:

* [Proteggere i parametri nelle definizioni del flusso di lavoroSecure parameters in workflow definitions](#secure-parameters-workflow)
* [Nascondere i dati dalla cronologia di esecuzione utilizzando l'offuscamento](#obfuscate)

Se si automatizza la distribuzione per le app per la logica utilizzando i modelli di `securestring` `secureobject` [Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile definire parametri di [modello](../azure-resource-manager/templates/template-parameters.md)protetti , che vengono valutati al momento della distribuzione, utilizzando i tipi e . Per definire i parametri del modello, `parameters` utilizzare la sezione di primo livello `parameters` del modello, che è separata e diversa dalla sezione della definizione del flusso di lavoro. Per fornire i valori per i parametri di modello, utilizzare un [file di parametri](../azure-resource-manager/templates/parameter-files.md)separato.

Ad esempio, se si usano i segreti, è possibile definire e usare parametri di modello protetti che recuperano tali segreti dall'insieme di credenziali delle chiavi di [Azure](../key-vault/key-vault-overview.md) durante la distribuzione. È quindi possibile fare riferimento all'insieme di credenziali delle chiavi e al segreto nel file dei parametri. Per altre informazioni, vedere gli argomenti seguenti:

* [Passare valori sensibili durante la distribuzione usando l'insieme di credenziali delle chiavi di AzurePass sensitive values at deployment by using Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Proteggere i parametri nei modelli](#secure-parameters-deployment-template) di Azure Resource Manager più avanti in questo argomentoSecure parameters in Azure Resource Manager templates later in this topic

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteggere i parametri nelle definizioni del flusso di lavoroSecure parameters in workflow definitions

Per proteggere le informazioni riservate nella definizione del flusso di lavoro dell'app per la logica, usare i parametri protetti in modo che queste informazioni non siano visibili dopo il salvataggio dell'app per la logica. Si supponga, ad esempio, di disporre di un'azione HTTP che richiede l'autenticazione di base, che utilizza un nome utente e una password. Nella definizione del `parameters` flusso di `basicAuthPasswordParam` `basicAuthUsernameParam` lavoro, la `securestring` sezione definisce i parametri e utilizzando il tipo . La definizione dell'azione `authentication` fa quindi riferimento a questi parametri nella sezione.

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
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
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

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Parametri protetti nei modelli di Azure Resource ManagerSecure parameters in Azure Resource Manager templates

Un modello di [Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) `parameters` per un'app per la logica include più sezioni. Per proteggere password, chiavi, segreti e altre informazioni riservate, definire i parametri `securestring` protetti `secureobject` a livello di modello e a livello di definizione del flusso di lavoro utilizzando il tipo or . È quindi possibile archiviare questi valori nell'insieme di credenziali delle chiavi di [Azure](../key-vault/key-vault-overview.md) e usare il file dei [parametri](../azure-resource-manager/templates/parameter-files.md) per fare riferimento all'insieme di credenziali delle chiavi e al segreto. Il modello recupera quindi tali informazioni al momento della distribuzione. Per altre informazioni, vedere [Passare valori sensibili durante la distribuzione tramite L'insieme di](../azure-resource-manager/templates/key-vault-parameter.md)credenziali delle chiavi di Azure.For more information, see Pass sensitive values at deployment by using Azure Key Vault .

Di seguito sono `parameters` riportate ulteriori informazioni su queste sezioni:Here is more information about these sections:

* Al livello principale del modello, una `parameters` sezione definisce i parametri per i valori utilizzati dal modello durante la *distribuzione.* Ad esempio, questi valori possono includere stringhe di connessione per un ambiente di distribuzione specifico. È quindi possibile archiviare questi valori in un [file di parametri](../azure-resource-manager/templates/parameter-files.md)separato, semplificando la modifica di questi valori.

* All'interno della definizione di risorsa dell'app `parameters` per la logica, ma all'esterno della definizione del flusso di lavoro, una sezione specifica i valori per i parametri della definizione del flusso di lavoro. In questa sezione è possibile assegnare questi valori utilizzando espressioni di modello che fanno riferimento ai parametri del modello. Queste espressioni vengono valutate al momento della distribuzione.

* All'interno della `parameters` definizione del flusso di lavoro, una sezione definisce i parametri utilizzati dall'app per la logica in fase di esecuzione. È quindi possibile fare riferimento a questi parametri all'interno del flusso di lavoro dell'app per la logica usando espressioni di definizione del flusso di lavoro, che vengono valutate in fase di esecuzione.

Questo modello di esempio con più `securestring` definizioni di parametri protetti che utilizzano il tipo:This example template that has multiple secured parameter definitions that use the type:

| Nome parametro | Descrizione |
|----------------|-------------|
| `TemplatePasswordParam` | Parametro di modello che accetta una password che viene `basicAuthPasswordParam` quindi passata al parametro della definizione del flusso di lavoro |
| `TemplateUsernameParam` | Parametro di modello che accetta un nome utente che `basicAuthUserNameParam` viene quindi passato al parametro della definizione del flusso di lavoro |
| `basicAuthPasswordParam` | Parametro di definizione del flusso di lavoro che accetta la password per l'autenticazione di base in un'azione HTTP |
| `basicAuthUserNameParam` | Parametro di definizione del flusso di lavoro che accetta il nome utente per l'autenticazione di base in un'azione HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
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
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
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
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
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
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Accesso a servizi e sistemi chiamati da app per la logica

Ecco alcuni modi in cui puoi proteggere gli endpoint che ricevono chiamate o richieste dall'app per la logica:

* Aggiungere l'autenticazione alle richieste in uscita.

  Quando si lavora con un trigger basato su HTTP o un'azione che effettua chiamate in uscita, ad esempio HTTP, HTTP e Swagger, o Webhook, è possibile aggiungere l'autenticazione alla richiesta inviata dall'app per la logica. Ad esempio, è possibile utilizzare questi tipi di autenticazione:For example, you can use these authentication types:

  * [Autenticazione di base](#basic-authentication)

  * [Autenticazione del certificato client](#client-certificate-authentication)

  * [Autenticazione OAuth di Active Directory](#azure-active-directory-oauth-authentication)

  * [Autenticazione identità gestita](#managed-identity-authentication)
  
  Per altre informazioni, vedere [Aggiungere l'autenticazione alle chiamate in uscita](#add-authentication-outbound) più avanti in questo argomento.

* Limitare l'accesso dagli indirizzi IP dell'app per la logica.

  Tutte le chiamate agli endpoint da app per la logica provengono da indirizzi IP designati specifici basati sulle aree delle app per la logica. È possibile aggiungere un filtro che accetti le richieste solo da tali indirizzi IP. Per ottenere questi indirizzi IP, vedere [Limiti e configurazione per le app per la logica](logic-apps-limits-and-config.md#configuration)di Azure.To get these IP addresses, see Limits and configuration for Azure Logic Apps .

* Migliorare la sicurezza per le connessioni ai sistemi locali.

  App per la logica di Azure offre l'integrazione con questi servizi per fornire comunicazioni locali più sicure e affidabili.

  * Gateway dati locale

    Molti connettori gestiti in App per la logica di Azure facilitano le connessioni protette a sistemi locali, ad esempio File System, SQL, SharePoint e DB2. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico in uscita protetto dall'agente gateway. Altre informazioni su [come funziona il gateway dati locale](logic-apps-gateway-install.md#gateway-cloud-service).

  * Connettersi tramite Gestione API di AzureConnect through Azure API Management

    [Gestione API di Azure](../api-management/api-management-key-concepts.md) dispone di numerose opzioni di connettività locale, tra cui l'integrazione ExpressRoute e per rete privata locale da sito a sito per la protezione del proxy e la comunicazione con i sistemi locali. Dal flusso di lavoro dell'app per la logica in Progettazione app per la logica, è possibile selezionare un'API esposta da Gestione API, che consente di accedere rapidamente ai sistemi locali.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Aggiungere l'autenticazione alle chiamate in uscitaAdd authentication to outbound calls

Gli endpoint HTTP e HTTPS supportano vari tipi di autenticazione. In base al trigger o all'azione utilizzata per effettuare chiamate in uscita o richieste che accedono a questi endpoint, è possibile scegliere tra intervalli diversi di tipi di autenticazione. Per assicurarti di proteggere tutte le informazioni sensibili gestite dall'app per la logica, usa i parametri protetti e codifica i dati in base alle esigenze. Per ulteriori informazioni sull'utilizzo e la protezione dei parametri, consultate [Accesso agli input dei parametri.](#secure-action-parameters)

> [!NOTE]
> In Progettazione app per la logica, la proprietà **Authentication** potrebbe essere nascosta in alcuni trigger e azioni in cui è possibile specificare il tipo di autenticazione. Per fare in modo che la proprietà venga visualizzata in questi casi, nel trigger o nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare **Autenticazione**. Per ulteriori informazioni, vedere [Autenticare l'accesso con identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Tipo di autenticazione | Supportata da |
|---------------------|--------------|
| [Basic](#basic-authentication) | Gestione API di Azure, Servizi app di Azure, HTTP, HTTP e Swagger, HTTP Webhook |
| [Certificato client](#client-certificate-authentication) | Gestione API di Azure, Servizi app di Azure, HTTP, HTTP e Swagger, HTTP Webhook |
| [Autenticazione OAuth Active Directory](#azure-active-directory-oauth-authentication) | Gestione API di Azure, Servizi app di Azure, Funzioni di Azure, HTTP, HTTP, Swagger, HTTP Webhook |
| [Crudo](#raw-authentication) | Gestione API di Azure, Servizi app di Azure, Funzioni di Azure, HTTP, HTTP, Swagger, HTTP Webhook |
| [Identità gestita](#managed-identity-authentication) | Gestione API di Azure, Servizi app di Azure, Funzioni di Azure, HTTP, HTTP, Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticazione di base

Se l'opzione [Base](../active-directory-b2c/secure-rest-api.md) è disponibile, specificare i seguenti valori di proprietà:

| Proprietà (progettazione) | Property (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | Basic | Il tipo di autenticazione da utilizzare |
| **Username** | `username` | Sì | <*nome utente*>| Il nome utente per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
| **Password** | `password` | Sì | <*Password*> | La password per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
||||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un modello di Azure Resource Manager [per automatizzare la distribuzione,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione `type` HTTP `Basic` di esempio specifica l'autenticazione come e utilizza la [funzione parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticazione con certificato client

Se l'opzione [Certificato client](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) è disponibile, specificare i seguenti valori di proprietà:

| Proprietà (progettazione) | Property (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | **Certificato client** <br>o <br>`ClientCertificate` | Tipo di autenticazione da utilizzare per i certificati client TLS/SSL. Sebbene i certificati autofirmati siano supportati, i certificati autofirmati per TLS/SSL non sono supportati. |
| **Pfx (in stato di** | `pfx` | Sì | <*encoded-pfx-contenuto del file*> | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) <p><p>Per convertire il file PFX in formato con codifica base64, è possibile usare PowerShell attenendosi alla procedura seguente:To convert the PFX file into base64-encoded format, you can use PowerShell by following these steps: <p>1. Salvare il contenuto del certificato in una variabile: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Convertire il contenuto `ToBase64String()` del certificato utilizzando la funzione e salvarlo in un file di testo: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Password** | `password`| No | <*file password per pfx*> | Password per accedere al file PFX. |
|||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un modello di Azure Resource Manager [per automatizzare la distribuzione,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione `type` HTTP `ClientCertificate` di esempio specifica l'autenticazione come e utilizza la [funzione parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Per altre informazioni sulla protezione dei servizi tramite l'autenticazione del certificato client, vedere gli argomenti seguenti:For more information about securing services by using client certificate authentication, see these topics:

* [Migliorare la sicurezza per le API usando l'autenticazione del certificato client in Gestione API di AzureImprove security for APIs by using client certificate authentication in Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Migliorare la sicurezza per i servizi back-end usando l'autenticazione del certificato client in Gestione API di AzureImprove security for back-end services by using client certificate authentication in Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Migliorare la sicurezza per il servizio RESTfuL utilizzando i certificati client](../active-directory-b2c/secure-rest-api.md)
* [Credenziali del certificato per l'autenticazione dell'applicazione](../active-directory/develop/active-directory-certificate-credentials.md)
* [Usare un certificato TLS/SSL nel codice nel servizio app di AzureUse a TLS/SSL certificate in your code in Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Autenticazione OAuth di Azure Active DirectoryAzure Active Directory OAuth authentication

Se l'opzione [OAuth di Active Directory](../active-directory/develop/about-microsoft-identity-platform.md) è disponibile, specificare i valori delle proprietà seguenti:

| Proprietà (progettazione) | Property (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | **Autenticazione OAuth Active Directory** <br>o <br>`ActiveDirectoryOAuth` | Tipo di autenticazione da usare. App per la logica segue attualmente il [protocollo OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Authority** | `authority` | No | <*Emittente DI URL per autorità*> | URL dell'autorità che fornisce il token di autenticazione. Per impostazione predefinita, questo valore è `https://login.windows.net`. |
| **Tenant** | `tenant` | Sì | <*ID tenant*> | L'ID tenant per il tenant di Azure AD |
| **Pubblico** | `audience` | Sì | <*risorsa da autorizzare*> | La risorsa che si vuole usare per l'autorizzazione, ad esempio `https://management.core.windows.net/` |
| **Client ID** | `clientId` | Sì | <*ID client*> | L'ID client per l'app richiedente l'autorizzazione |
| **Tipo di credenziali** | `credentialType` | Sì | Certificato <br>o <br>Segreto | Tipo di credenziale utilizzato dal client per richiedere l'autorizzazione. Questa proprietà e il valore non vengono visualizzati nella definizione sottostante dell'app per la logica, ma determinano le proprietà visualizzate per il tipo di credenziale selezionato. |
| **Segreto** | `secret` | Sì, ma solo per il tipo di credenziale "Segreto" | <*segreto client*> | Il segreto client per la richiesta dell'autorizzazione |
| **Pfx (in stato di** | `pfx` | Sì, ma solo per il tipo di credenziale "Certificato" | <*encoded-pfx-contenuto del file*> | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) |
| **Password** | `password` | Sì, ma solo per il tipo di credenziale "Certificato" | <*file password per pfx*> | Password per accedere al file PFX. |
|||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un modello di Azure Resource Manager [per automatizzare la distribuzione,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione `type` HTTP `ActiveDirectoryOAuth`di esempio `Secret`specifica l'autenticazione come , il tipo di credenziale come e utilizza la [funzione parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Autenticazione raw

Se l'opzione **Raw** è disponibile, è possibile utilizzare questo tipo di autenticazione quando è necessario utilizzare schemi di [autenticazione](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) che non seguono il [protocollo OAuth 2.0](https://oauth.net/2/). Con questo tipo, si crea manualmente il valore dell'intestazione di autorizzazione da inviare con la richiesta in uscita e si specifica il valore dell'intestazione nel trigger o nell'azione.

Ad esempio, di seguito è riportata un'intestazione di esempio per una richiesta HTTPS che segue il [protocollo OAuth 1.0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Nel trigger o nell'azione che supporta l'autenticazione non elaborata specificare i valori delle proprietà seguenti:In the trigger or action that supports raw authentication, specify these property values:

| Proprietà (progettazione) | Property (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | Raw | Il tipo di autenticazione da utilizzare |
| **valore** | `value` | Sì | <*authorization-header-value*> | Valore dell'intestazione di autorizzazione da utilizzare per l'autenticazione |
||||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un modello di Azure Resource Manager [per automatizzare la distribuzione,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione `type` HTTP `Raw`di esempio specifica l'autenticazione come e utilizza la [funzione parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Autenticazione identità gestita

Se l'opzione [Identità gestita](../active-directory/managed-identities-azure-resources/overview.md) è disponibile, l'app per la logica può usare l'identità assegnata dal sistema o una *singola* identità assegnata dall'utente creata manualmente per autenticare l'accesso alle risorse in altri tenant di Azure Active Directory (Azure AD) senza effettuare l'accesso. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Altre informazioni sui servizi di [Azure che supportano le identità gestite per l'autenticazione](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)di Azure AD.

1. Prima che l'app per la logica possa usare un'identità gestita, seguire i passaggi descritti in [Autenticare l'accesso alle risorse di Azure usando le identità gestite nelle app per la logica](../logic-apps/create-managed-service-identity.md)di Azure.Before your logic app can use a managed identity, follow the steps in Authenticate access to Azure resources by using managed identities in Azure Logic Apps . Questi passaggi abilitano l'identità gestita nell'app per la logica e configurano l'accesso di tale identità alla risorsa di Azure di destinazione.

1. Prima che una funzione di Azure possa usare un'identità gestita, abilitare innanzitutto [l'autenticazione per le funzioni](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)di Azure.Before an Azure function can use a managed identity, first enable authentication for Azure functions .

1. Nel trigger o nell'azione in cui si vuole usare l'identità gestita specificare i valori delle proprietà seguenti:In the trigger or action where you want to use the managed identity, specify these property values:

   | Proprietà (progettazione) | Property (JSON) | Obbligatoria | valore | Descrizione |
   |---------------------|-----------------|----------|-------|-------------|
   | **autenticazione** | `type` | Sì | **Identità gestita** <br>o <br>`ManagedServiceIdentity` | Il tipo di autenticazione da utilizzare |
   | **Identità gestita** | `identity` | Sì | * **Identità gestita assegnata dal sistema** <br>o <br>`SystemAssigned` <p><p>- <*nome-identità assegnato dall'utente*> | Identità gestita da utilizzare |
   | **Pubblico** | `audience` | Sì | <*ID-risorsa di destinazione*> | ID risorsa per la risorsa di destinazione a cui si desidera accedere. <p>Ad esempio, `https://storage.azure.com/` rende validi i token di accesso per l'autenticazione per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare `https://fabrikamstorageaccount.blob.core.windows.net` un URL del servizio radice, ad esempio per un account di archiviazione specifico. <p>**Nota:** la proprietà **Pubblico** potrebbe essere nascosta in alcuni trigger o azioni. Per rendere visibile questa proprietà, nel trigger o nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare **Pubblico**. <p><p>**Importante:** assicurarsi che questo ID risorsa di destinazione *corrisponda esattamente* al valore previsto da Azure AD, incluse eventuali barre finali necessarie. Pertanto, `https://storage.azure.com/` l'ID risorsa per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID risorsa per un account di archiviazione specifico non richiede una barra finale. Per trovare questi ID di risorsa, vedere [Servizi di Azure che supportano Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   |||||

   Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un modello di Azure Resource Manager [per automatizzare la distribuzione,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione `type` HTTP `ManagedServiceIdentity` di esempio specifica l'autenticazione come e utilizza la [funzione parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Automatizzare la distribuzione per le app per la logica di AzureAutomate deployment for Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorare le app per la logica](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnosi degli errori delle app per la logica](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizzare la distribuzione di app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
