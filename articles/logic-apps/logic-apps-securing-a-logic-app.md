---
title: Proteggere l'accesso e i dati-app per la logica di Azure
description: Proteggere gli input di parametri, i trigger di richiesta HTTP, la cronologia di esecuzione, le operazioni dell'app per la logica e le connessioni ad altri servizi in app per la logica
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 6c16b38cce31c45158a5871c10dbd01339da9203
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845423"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteggere l'accesso e i dati in app per la logica di Azure

Per controllare l'accesso e proteggere i dati in app per la logica di Azure, è possibile configurare la sicurezza in queste aree:

* [Accesso ai trigger di richiesta HTTP](#secure-triggers)
* [Accesso alle operazioni dell'app per la logica](#secure-operations)
* [Accesso agli input e agli output della cronologia di esecuzione](#secure-run-history)
* [Accesso agli input di parametri](#secure-action-parameters)
* [Accesso ai servizi e ai sistemi chiamati dalle app per la logica](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Accesso ai trigger di richiesta HTTP

Quando l'app per la logica usa un trigger HTTP basato su richiesta, ad esempio il trigger di [richiesta](../connectors/connectors-native-reqres.md) o [webhook](../connectors/connectors-native-webhook.md), è possibile limitare l'accesso in modo che solo i client autorizzati possano avviare l'app per la logica. Tutte le richieste ricevute da un'app per la logica vengono crittografate e protette con il protocollo Secure Sockets Layer (SSL). 

Ecco i modi in cui è possibile proteggere l'accesso a questo tipo di trigger:

* [Generare firme di accesso condiviso](#sas)
* [Limitare gli indirizzi IP in ingresso](#restrict-incoming-ip-addresses)
* [Aggiungere Azure Active Directory, OAuth o altri standard di sicurezza](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Genera firme di accesso condiviso (SAS)

Ogni endpoint di richiesta in un'app per la logica ha una [firma di accesso condiviso (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) nell'URL dell'endpoint, che segue questo formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Ogni URL contiene un `sp`parametro `sv`di query `sig` , e come descritto in questa tabella:

| Parametro di query | Descrizione |
|-----------------|-------------|
| `sp` | Specifica le autorizzazioni per i metodi HTTP consentiti da utilizzare. |
| `sv` | Specifica la versione SAS da usare per la generazione della firma. |
| `sig` | Specifica la firma da utilizzare per l'autenticazione dell'accesso al trigger. Questa firma viene generata usando l'algoritmo SHA256 con una chiave di accesso segreta su tutti i percorsi e le proprietà dell'URL. Mai esposta o pubblicata, questa chiave viene mantenuta crittografata e archiviata con l'app per la logica. L'app per la logica autorizza solo i trigger che contengono una firma valida creata con la chiave privata. |
|||

Per ulteriori informazioni sulla protezione dell'accesso con la firma di accesso condiviso, vedere le sezioni seguenti in questo argomento:

* [Per rigenerare le chiavi di accesso](#access-keys)
* [Creare URL di callback in scadenza](#expiring-urls)
* [Creare URL con una chiave primaria o secondaria](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

Per generare una nuova chiave di accesso protetta in qualsiasi momento, usare l'API REST di Azure o portale di Azure. Tutti gli URL generati in precedenza che usano la chiave precedente vengono invalidati e non sono più autorizzati ad attivare l'app per la logica. Gli URL recuperati dopo la rigenerazione vengono firmati con la nuova chiave di accesso.

1. Nel portale di Azure aprire l'app per la logica che contiene la chiave che si desidera rigenerare.

1. Nel menu di app per la logica in **Impostazioni**, selezionare **Chiave di accesso**.

1. Selezionare la chiave che si desidera rigenerare e completare il processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Creare URL di callback in scadenza

Se si condivide l'URL dell'endpoint per un trigger basato su richiesta HTTP con altre parti, è possibile generare URL di callback che usano chiavi specifiche e che abbiano date di scadenza. In questo modo, è possibile eseguire facilmente il rollup delle chiavi o limitare l'accesso per attivare l'app per la logica in base a un intervallo di tempo specifico. Per specificare una data di scadenza per un URL, usare l' [API REST di app](https://docs.microsoft.com/rest/api/logic/workflowtriggers)per la logica, ad esempio:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la `NotAfter`proprietà usando una stringa di data JSON. Questa proprietà restituisce un URL di callback valido solo fino a data e ora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Creare URL con una chiave privata primaria o secondaria

Quando si generano o elencano URL di callback per trigger HTTP basati su richiesta, è possibile specificare la chiave da usare per firmare l'URL. Per generare un URL firmato da una chiave specifica, usare l' [API REST di app](https://docs.microsoft.com/rest/api/logic/workflowtriggers)per la logica, ad esempio:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `KeyType` come `Primary` o `Secondary`. Questa proprietà restituisce un URL firmato dalla chiave sicura specificata.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Limitare gli indirizzi IP in ingresso

Insieme alla firma di accesso condiviso, potrebbe essere necessario limitare i client specifici che possono chiamare l'app per la logica. Ad esempio, se si gestisce l'endpoint di richiesta usando gestione API di Azure, è possibile limitare l'app per la logica ad accettare le richieste solo dall'indirizzo IP per l'istanza di gestione API.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Limitare gli intervalli IP in ingresso in portale di Azure

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso (Configurazione del controllo di accesso)**  > **Allowed inbound IP addresses (Indirizzi IP in ingresso consentiti)** , selezionare **Specific IP ranges (Intervalli IP specifici)** .

1. In **IP ranges for triggers (Intervalli di IP per i trigger)** , specificare gli intervalli di indirizzi IP accettati dal trigger.

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

Se si vuole che l'app per la logica venga attivata solo come app per la logica annidata, selezionare **solo altre app**per la logica dall'elenco **indirizzi IP in ingresso** consentiti. Questa opzione scrive una matrice vuota nella risorsa dell'app per la logica. In questo modo, solo le chiamate dal servizio app per la logica (app per la logica padre) possono attivare l'app per la logica nidificata.

> [!NOTE]
> Indipendentemente dall'indirizzo IP, è comunque possibile eseguire un'app per la logica con un trigger http basato su richiesta tramite `/triggers/<trigger-name>/run` l'API REST di Azure o gestione API. Tuttavia, questo scenario richiede ancora l'autenticazione per l'API REST di Azure. Tutti gli eventi vengono visualizzati nel log di controllo di Azure. Assicurarsi di impostare i criteri di controllo di accesso di conseguenza.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli IP in ingresso nel modello di Azure Resource Manager

Se si automatizzano le distribuzioni di app per la logica usando un [modello di Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), è possibile specificare gli intervalli `accessControl` IP usando la `triggers` sezione con la sezione nella definizione di risorsa dell'app per la logica, ad esempio:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Aggiungere Azure Active Directory, OAuth o altri standard di sicurezza

Per aggiungere altri protocolli di autorizzazione all'app per la logica,considerare l'uso di [Gestione API di Azure](../api-management/api-management-key-concepts.md). Questo servizio offre la possibilità di esporre l'app per la logica come API e offre funzionalità avanzate di monitoraggio, sicurezza, criteri e documentazione per qualsiasi endpoint. Gestione API di Azure può esporre un endpoint privato o pubblico per l'app per la logica, che potrebbe sfruttare Azure Active Directory, certificati, OAuth o altri standard di sicurezza. Quando Gestione API riceve una richiesta, il servizio invia la richiesta all'app per la logica, eseguendo anche eventuali trasformazioni necessarie e restrizioni. Per consentire solo a Gestione API di attivare l'app per la logica, è possibile usare le impostazioni dell'intervallo IP in ingresso dell'app per la logica.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Accesso alle operazioni dell'app per la logica

È possibile consentire solo a utenti o gruppi specifici di eseguire operazioni specifiche, ad esempio la gestione, la modifica e la visualizzazione di app per la logica. Per controllare le autorizzazioni, usare il controllo degli accessi in [base al ruolo (RBAC) di Azure](../role-based-access-control/role-assignments-portal.md) per assegnare ruoli personalizzati o predefiniti ai membri nella sottoscrizione di Azure:

* [Collaboratore per app per la logica](../role-based-access-control/built-in-roles.md#logic-app-contributor): Consente di gestire le app per la logica, ma non di modificarne l'accesso.

* [Operatore per app per la logica](../role-based-access-control/built-in-roles.md#logic-app-operator): Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle.

Per impedire ad altri utenti di modificare o eliminare l'app per la logica, è possibile usare il [blocco di risorse di Azure](../azure-resource-manager/resource-group-lock-resources.md), che impedisce ad altri utenti di modificare o eliminare le risorse di produzione.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Accesso ai dati della cronologia di esecuzione

Durante l'esecuzione di un'app per la logica, tutti i dati vengono crittografati durante il transito usando [TLS (Transit Layer Security)](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) e [inattivi](../security/fundamentals/encryption-atrest.md). Al termine dell'esecuzione dell'app per la logica, è possibile visualizzare la cronologia dell'esecuzione, inclusi i passaggi eseguiti con lo stato, la durata, gli input e gli output per ogni azione. Questo approfondimento fornisce informazioni dettagliate sulle modalità di esecuzione dell'app per la logica e su dove è possibile iniziare la risoluzione dei problemi che si verificano.

Quando si accede alla cronologia di esecuzione dell'app per la logica, app per la logica autentica l'accesso e fornisce collegamenti agli input e agli output delle richieste e delle risposte nell'esecuzione dell'app per la logica. Tuttavia, per le azioni che gestiscono password, segreti, chiavi o altre informazioni riservate, si desidera impedire ad altri utenti di visualizzare e accedere a tali dati. Ad esempio, se l'app per la logica ottiene un segreto da [Azure Key Vault](../key-vault/key-vault-whatis.md) da usare durante l'autenticazione di un'azione http, si vuole nascondere tale segreto dalla visualizzazione.

Per controllare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, sono disponibili le opzioni seguenti:

* [Limitare l'accesso in base all'intervallo di indirizzi IP](#restrict-ip).

  Questa opzione consente di proteggere l'accesso alla cronologia di esecuzione in base alle richieste provenienti da un intervallo di indirizzi IP specifico.

* [Nascondere i dati dalla cronologia di esecuzione usando l'offuscamento](#obfuscate).

  In molti trigger e azioni è possibile nascondere gli input, gli output o entrambi dalla cronologia di esecuzione di un'app per la logica.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Limita l'accesso in base all'intervallo di indirizzi IP

È possibile limitare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, in modo che solo le richieste provenienti da intervalli di indirizzi IP specifici possano visualizzare tali dati. Ad esempio, per impedire a tutti gli utenti di accedere a input e output, specificare un intervallo di `0.0.0.0-0.0.0.0`indirizzi IP, ad esempio. Solo una persona con autorizzazioni di amministratore può rimuovere questa restrizione, che offre la possibilità di accedere "just-in-Time" ai dati dell'app per la logica. È possibile specificare gli intervalli IP da limitare usando il portale di Azure o in un modello di Azure Resource Manager usato per la distribuzione dell'app per la logica.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Limitare gli intervalli IP nel portale di Azure

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso (Configurazione del controllo di accesso)**  > **Allowed inbound IP addresses (Indirizzi IP in ingresso consentiti)** , selezionare **Specific IP ranges (Intervalli IP specifici)** .

1. In **Intervalli IP per i contenuti**, specificare gli intervalli di indirizzi IP che possono accedere al contenuto da input e output. 

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli IP nel modello di Azure Resource Manager

Se si automatizzano le distribuzioni di app per la logica usando un [modello di Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), è possibile specificare gli intervalli `accessControl` IP usando la `contents` sezione con la sezione nella definizione di risorsa dell'app per la logica, ad esempio:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Nascondere i dati dalla cronologia di esecuzione usando l'offuscamento

Molti trigger e azioni hanno impostazioni per nascondere gli input, gli output o entrambi dalla cronologia di esecuzione di un'app per la logica. Di seguito sono riportate alcune [considerazioni da considerare](#obfuscation-considerations) quando si usano queste impostazioni per proteggere questi dati.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Proteggere gli input e gli output nella finestra di progettazione

1. Se l'app per la logica non è già aperta nella [portale di Azure](https://portal.azure.com), aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

   ![Apri app per la logica di esempio](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. Nel trigger o nell'azione in cui si desidera proteggere i dati, selezionare il pulsante con i puntini di sospensione ( **...** ) e quindi selezionare **Impostazioni**.

   ![Apri "Impostazioni"](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Attivare gli **input protetti**, gli **output protetti**o entrambi. Al termine, fare clic su **Fine**.

   ![Attiva input o output protetti](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   L'azione o il trigger Mostra ora un'icona di blocco nella barra del titolo.

   ![Icona di blocco sulla barra del titolo](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   I token che rappresentano output protetti dalle azioni precedenti mostrano anche icone di blocco. Ad esempio, quando si seleziona un output di questo tipo dall'elenco di contenuto dinamico da usare in un'azione, il token Mostra un'icona di blocco.

   ![Seleziona output](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Dopo l'esecuzione dell'app per la logica, è possibile visualizzare la cronologia per l'esecuzione.

   1. Nel riquadro di **Panoramica** dell'app per la logica selezionare l'esecuzione che si vuole visualizzare.

   1. Nel riquadro **esecuzione App** per la logica, espandere le azioni che si desidera esaminare.

      Se si sceglie di proteggere gli input e gli output, questi valori vengono ora nascosti.

      ![Dati nascosti nella cronologia di esecuzione](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteggere gli input e gli output nella visualizzazione codice

Nella definizione di azione o trigger sottostante aggiungere o aggiornare la `runtimeConfiguration.secureData.properties` matrice con uno o entrambi i valori seguenti:

* `"inputs"`: Protegge gli input nella cronologia di esecuzione.
* `"outputs"`: Protegge gli output nella cronologia di esecuzione.

Di seguito sono riportate alcune [considerazioni da considerare](#obfuscation-considerations) quando si usano queste impostazioni per proteggere questi dati.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Considerazioni per nascondere gli input e gli output

* Quando si proteggono gli input o gli output in un trigger o un'azione, app per la logica non invia i dati protetti ad Azure Log Analytics. Non è inoltre possibile aggiungere [Proprietà](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) rilevate a tale trigger o azione per il monitoraggio.

* L' [API app per la logica per la gestione della cronologia del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/) non restituisce output protetti.

* In un'azione in cui si proteggono gli input o che usano in modo esplicito gli output protetti e questa azione restituisce una risposta con output che includono i dati protetti, è necessario attivare manualmente gli **output protetti** in questa azione per proteggere questi output.

* Assicurarsi di attivare gli **input protetti** o gli **output protetti** nelle azioni downstream in cui si prevede che la cronologia di esecuzione protegga i dati.

  **Impostazione degli output protetti**

  Quando si attivano manualmente gli **output protetti** in un trigger o un'azione, le app per la logica proteggono questi output nella cronologia di esecuzione. Se un'azione downstream USA in modo esplicito questi output protetti come input, app per la logica nasconde gli input dell'azione nella cronologia di esecuzione, ma *non Abilita* l'impostazione degli **input protetti** dell'azione.

  ![Output protetti come input e effetto downstream sulla maggior parte delle azioni](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Le azioni compose, parse JSON e Response hanno solo l'impostazione degli **input protetti** . Se attivata, l'impostazione nasconde anche gli output di tali azioni. Se queste azioni usano in modo esplicito gli output protetti upstream come input, app per la logica nasconde gli input e gli output di tali azioni, ma *non Abilita* l'impostazione degli **input protetti** di queste azioni. Se un'azione downstream USA in modo esplicito gli output nascosti dalle azioni compose, parse JSON o Response come input, app per *la logica non nasconde gli input o gli output dell'azione downstream*.

  ![Output protetti come input con effetto downstream su azioni specifiche](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Impostazione degli input protetti**

  Quando si attivano manualmente gli **input protetti** in un trigger o un'azione, le app per la logica proteggono questi input nella cronologia di esecuzione. Se un'azione downstream USA in modo esplicito gli output visibili da tale trigger o azione come input, app per la logica nasconde gli input dell'azione downstream nella cronologia di esecuzione, ma *non Abilita* gli **input protetti** in questa azione e non nasconde l'azione uscite.

  ![Input protetti e effetto downstream sulla maggior parte delle azioni](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Se le azioni compose, parse JSON e Response usano in modo esplicito gli output visibili del trigger o dell'azione con gli input protetti, le app per la logica nascondono gli input e gli output di tali azioni, ma *non abilitano* gli **input protetti** di tali azioni impostazione. Se un'azione downstream USA in modo esplicito gli output nascosti dalle azioni compose, parse JSON o Response come input, app per *la logica non nasconde gli input o gli output dell'azione downstream*.

  ![Input protetti e effetto downstream su azioni specifiche](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Accesso agli input di parametri

Se si esegue la distribuzione in ambienti diversi, provare a parametrizzazione i valori nella definizione del flusso di lavoro che variano in base a tali ambienti. In questo modo, è possibile usare un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters) per distribuire l'app per la logica, proteggere le informazioni riservate definendo parametri protetti e fornire tali input separatamente tramite i parametri del modello usando un [parametro file](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Se ad esempio si autenticano azioni HTTP con [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), è possibile definire e proteggere i parametri che accettano l'ID client e il segreto client usati per l'autenticazione. Per definire questi parametri per l'app per la logica, `parameters` usare la sezione nella definizione del flusso di lavoro dell'app per la logica. Per proteggere i valori dei parametri che non si vuole visualizzare quando si modifica l'app per la logica o si visualizza la cronologia di `securestring` esecuzione `secureobject` , definire i parametri usando il tipo o e usare la codifica in base alle esigenze. I parametri con questo tipo non vengono restituiti con la definizione di risorsa e non sono accessibili durante la visualizzazione della risorsa dopo la distribuzione. Per accedere a questi valori di parametro durante il runtime `@parameters('<parameter-name>')` , usare l'espressione all'interno della definizione del flusso di lavoro. Questa espressione viene valutata solo in fase di esecuzione ed è descritta dal [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se si usa un parametro nelle intestazioni o nel corpo di una richiesta HTTP, tale parametro potrebbe essere visibile quando si visualizza la cronologia di esecuzione dell'app per la logica e la richiesta HTTP in uscita. Assicurarsi di impostare anche i criteri di accesso al contenuto di conseguenza. Le intestazioni di autorizzazione non sono mai visibili tramite input o output. Se quindi viene usato un segreto, questo non sarà recuperabile.

Per ulteriori informazioni, vedere [proteggere i parametri nelle definizioni del flusso di lavoro](#secure-parameters-workflow) più avanti in questo argomento.

Quando si automatizzano le distribuzioni con i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), è possibile definire i parametri di modello protetti, che vengono valutati `securestring` in fase di distribuzione, usando i tipi e. `secureobject` Per definire i parametri del modello, usare la sezione di `parameters` primo livello del modello, che è separata e diversa dalla sezione `parameters` della definizione del flusso di lavoro. Per specificare i valori per i parametri del modello, usare un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)separato.

Se, ad esempio, si usano i segreti, è possibile definire e usare i parametri di modello protetti che recuperano i segreti da [Azure Key Vault](../key-vault/key-vault-whatis.md) durante la distribuzione. È quindi possibile fare riferimento all'insieme di credenziali delle chiavi e al segreto nel file dei parametri. Per altre informazioni, vedere gli argomenti seguenti:

* [Usare Azure Key Vault per passare i valori dei parametri protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Proteggere i parametri nei modelli di Azure Resource Manager](#secure-parameters-deployment-template) più avanti in questo argomento

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteggere i parametri nelle definizioni del flusso di lavoro

Per proteggere le informazioni riservate nella definizione del flusso di lavoro dell'app per la logica, usare parametri protetti in modo che queste informazioni non siano visibili dopo il salvataggio dell'app per la logica. Si supponga, ad esempio, che un'azione HTTP richieda l'autenticazione di base, che usa un nome utente e una password. Nella definizione del flusso di lavoro `parameters` , la sezione `basicAuthPasswordParam` definisce `basicAuthUsernameParam` i parametri e usando `securestring` il tipo. La definizione dell'azione fa quindi riferimento a questi `authentication` parametri nella sezione.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Proteggere i parametri nei modelli di Azure Resource Manager

Un modello di gestione risorse per un'app per la `parameters` logica include più sezioni. Per proteggere password, chiavi, segreti e altre informazioni riservate, definire parametri protetti a livello di modello e di definizione del flusso di lavoro `securestring` usando `secureobject` il tipo o. È quindi possibile archiviare questi valori in [Azure Key Vault](../key-vault/key-vault-whatis.md) e usare il [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) per fare riferimento all'insieme di credenziali delle chiavi e al segreto. Il modello recupera quindi tali informazioni in fase di distribuzione. Per ulteriori informazioni, vedere [utilizzare Azure Key Vault per passare i valori dei parametri protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Di seguito sono riportate `parameters` altre informazioni su queste sezioni:

* Al livello principale del modello, una `parameters` sezione definisce i parametri per i valori utilizzati dal modello durante la *distribuzione*. Questi valori, ad esempio, possono includere stringhe di connessione per un ambiente di distribuzione specifico. È quindi possibile archiviare questi valori in un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)separato, rendendo più semplice la modifica di questi valori.

* All'interno della definizione di risorsa dell'app per la logica, ma all'esterno `parameters` della definizione del flusso di lavoro, una sezione specifica i valori per i parametri della definizione del flusso di lavoro. In questa sezione è possibile assegnare questi valori usando espressioni di modello che fanno riferimento ai parametri del modello. Queste espressioni vengono valutate in fase di distribuzione.

* All'interno della definizione del flusso `parameters` di lavoro, una sezione definisce i parametri usati dall'app per la logica in fase di esecuzione. È quindi possibile fare riferimento a questi parametri nel flusso di lavoro dell'app per la logica usando le espressioni di definizione del flusso di lavoro, che vengono valutate in fase di esecuzione

Questo modello di esempio con più definizioni di parametro protette che usano `securestring` il tipo:

| Nome parametro | Descrizione |
|----------------|-------------|
| `TemplatePasswordParam` | Parametro di modello che accetta una password che viene quindi passata al `basicAuthPasswordParam` parametro della definizione del flusso di lavoro. |
| `TemplateUsernameParam` | Parametro di modello che accetta un nome utente che viene quindi passato al `basicAuthUserNameParam` parametro della definizione del flusso di lavoro. |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Accesso ai servizi e ai sistemi chiamati dalle app per la logica

Ecco alcuni modi in cui è possibile proteggere gli endpoint in cui l'app per la logica necessita dell'accesso per inviare le richieste:

* Aggiungere l'autenticazione alle richieste in uscita.

  Quando si lavora con un'azione HTTP, HTTP + spavalderia (Open API) o webhook, è possibile aggiungere l'autenticazione alla richiesta inviata dall'app per la logica. Potrebbe trattarsi di autenticazione di base, autenticazione del certificato o l'autenticazione di Azure Active Directory. Per altre informazioni, vedere [Autenticare i trigger o le azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* Limitare l'accesso dagli indirizzi IP delle app per la logica.

  Tutte le chiamate agli endpoint da app per la logica provengono da indirizzi IP specifici designati basati sulle aree delle app per la logica. È possibile aggiungere un filtro che accetti le richieste solo da tali indirizzi IP. Per ottenere questi indirizzi IP, vedere [limiti e configurazione per app per la logica di Azure](logic-apps-limits-and-config.md#configuration).

* Autenticare e accedere alle risorse come identità gestite.

  Per accedere alle risorse in altri tenant di Azure Active Directory (Azure AD), l'app per la logica è in grado di autenticare l'identità senza accedere usando un'identità gestita (precedentemente nota come identità del servizio gestita o MSI), anziché con credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Per altre informazioni su come configurare e usare un'identità gestita assegnata dal sistema per l'app per la logica, vedere [eseguire l'autenticazione e l'accesso alle risorse con identità gestite in app per la logica di Azure](../logic-apps/create-managed-service-identity.md).

* Proteggere le connessioni ai sistemi locali.

  Le app per la logica offrono integrazione con tali servizi per garantire una comunicazione locale sicura e affidabile.

  * Gateway dati locale

    Molti connettori gestiti in app per la logica di Azure forniscono connessioni sicure ai sistemi locali, ad esempio file System, SQL, SharePoint e DB2. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni su [come funziona il gateway dati locale](logic-apps-gateway-install.md#gateway-cloud-service).

  * Connettersi tramite gestione API di Azure

    [Gestione API di Azure](../api-management/api-management-key-concepts.md) dispone di numerose opzioni di connettività locale, tra cui l'integrazione ExpressRoute e per rete privata locale da sito a sito per la protezione del proxy e la comunicazione con i sistemi locali. Dal flusso di lavoro dell'app per la logica nella finestra di progettazione dell'app per la logica è possibile selezionare un'API esposta da gestione API, che consente di accedere rapidamente ai sistemi locali.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di modelli di distribuzione](logic-apps-create-deploy-template.md)  
* [Monitorare le app per la logica](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosi degli errori delle app per la logica](logic-apps-diagnosing-failures.md)  
* [Automatizzare la distribuzione delle app per la logica](logic-apps-azure-resource-manager-templates-overview.md)
