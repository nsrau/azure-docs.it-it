---
title: Proteggere l'accesso e i dati-app per la logica di Azure
description: Aggiungere sicurezza per proteggere gli input, gli output, i trigger basati su richiesta, la cronologia di esecuzione, le attività di gestione e l'accesso ad altre risorse in app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: c9dfc4ed6fce186fea9474222875a072edb32f59
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084720"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteggere l'accesso e i dati in app per la logica di Azure

Per controllare l'accesso e proteggere i dati in app per la logica di Azure, è possibile configurare la sicurezza in queste aree:

* [Accesso ai trigger basati su richiesta](#secure-triggers)
* [Accesso alle operazioni dell'app per la logica](#secure-operations)
* [Accesso agli input e agli output della cronologia di esecuzione](#secure-run-history)
* [Accesso agli input di parametri](#secure-action-parameters)
* [Accesso ai servizi e ai sistemi chiamati dalle app per la logica](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Accesso ai trigger basati su richiesta

Se l'app per la logica usa un trigger basato su richiesta, che riceve le chiamate o le richieste in ingresso, ad esempio il trigger di [richiesta](../connectors/connectors-native-reqres.md) o [webhook](../connectors/connectors-native-webhook.md) , è possibile limitare l'accesso in modo che solo i client autorizzati possano chiamare l'app per la logica. Tutte le richieste ricevute da un'app per la logica vengono crittografate e protette con il protocollo Secure Sockets Layer (SSL).

Ecco i modi in cui è possibile proteggere l'accesso a questo tipo di trigger:

* [Generare firme di accesso condiviso](#sas)
* [Limitare gli indirizzi IP in ingresso](#restrict-inbound-ip-addresses)
* [Aggiungere Azure Active Directory OAuth o un'altra sicurezza](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Genera firme di accesso condiviso (SAS)

Ogni endpoint di richiesta in un'app per la logica ha una [firma di accesso condiviso (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) nell'URL dell'endpoint, che segue questo formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Ogni URL contiene i parametri di query `sp`, `sv`e `sig` come descritto in questa tabella:

| Query parameter (Parametro di query) | DESCRIZIONE |
|-----------------|-------------|
| `sp` | Specifica le autorizzazioni per i metodi HTTP consentiti da utilizzare. |
| `sv` | Specifica la versione SAS da usare per la generazione della firma. |
| `sig` | Specifica la firma da utilizzare per l'autenticazione dell'accesso al trigger. Questa firma viene generata usando l'algoritmo SHA256 con una chiave di accesso segreta su tutti i percorsi e le proprietà dell'URL. Mai esposta o pubblicata, questa chiave viene mantenuta crittografata e archiviata con l'app per la logica. L'app per la logica autorizza solo i trigger che contengono una firma valida creata con la chiave privata. |
|||

Per ulteriori informazioni sulla protezione dell'accesso con SAS, vedere le sezioni seguenti in questo argomento:

* [Per rigenerare le chiavi di accesso](#access-keys)
* [Creare URL di callback in scadenza](#expiring-urls)
* [Creare URL con una chiave primaria o secondaria](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

Per generare una nuova chiave di accesso protetta in qualsiasi momento, usare l'API REST di Azure o portale di Azure. Tutti gli URL generati in precedenza che usano la chiave precedente vengono invalidati e non sono più autorizzati ad attivare l'app per la logica. Gli URL recuperati dopo la rigenerazione vengono firmati con la nuova chiave di accesso.

1. Nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica che contiene la chiave che si vuole rigenerare.

1. Nel menu di app per la logica in **Impostazioni**, selezionare **Chiave di accesso**.

1. Selezionare la chiave che si desidera rigenerare e completare il processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Creare URL di callback in scadenza

Se si condivide l'URL dell'endpoint per un trigger basato su richiesta con altre entità, è possibile generare URL di callback che usano chiavi specifiche e hanno date di scadenza. In questo modo, è possibile eseguire facilmente il rollup delle chiavi o limitare l'accesso per attivare l'app per la logica in base a un intervallo di tempo specifico. Per specificare una data di scadenza per un URL, usare l' [API REST di app](https://docs.microsoft.com/rest/api/logic/workflowtriggers)per la logica, ad esempio:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `NotAfter`usando una stringa di data JSON. Questa proprietà restituisce un URL di callback valido solo fino a data e ora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Creare URL con una chiave privata primaria o secondaria

Quando si generano o elencano URL di callback per un trigger basato su richiesta, è possibile specificare la chiave da usare per firmare l'URL. Per generare un URL firmato da una chiave specifica, usare l' [API REST di app](https://docs.microsoft.com/rest/api/logic/workflowtriggers)per la logica, ad esempio:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `KeyType` come `Primary` o `Secondary`. Questa proprietà restituisce un URL firmato dalla chiave sicura specificata.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Limitare gli indirizzi IP in ingresso

Insieme alla firma di accesso condiviso, potrebbe essere necessario limitare in modo specifico i client che possono chiamare l'app per la logica. Ad esempio, se si gestisce l'endpoint di richiesta usando gestione API di Azure, è possibile limitare l'app per la logica ad accettare le richieste solo dall'indirizzo IP per l'istanza di gestione API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Limitare gli intervalli di indirizzi IP in ingresso in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso (Configurazione del controllo di accesso)**  > **Allowed inbound IP addresses (Indirizzi IP in ingresso consentiti)** , selezionare **Specific IP ranges (Intervalli IP specifici)** .

1. In **IP ranges for triggers (Intervalli di IP per i trigger)** , specificare gli intervalli di indirizzi IP accettati dal trigger.

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

Se si vuole che l'app per la logica venga attivata solo come app per la logica annidata, selezionare **solo altre app**per la logica dall'elenco **indirizzi IP in ingresso consentiti** . Questa opzione scrive una matrice vuota nella risorsa dell'app per la logica. In questo modo, solo le chiamate dal servizio app per la logica (app per la logica padre) possono attivare l'app per la logica nidificata.

> [!NOTE]
> Indipendentemente dall'indirizzo IP, è comunque possibile eseguire un'app per la logica che includa un trigger basato su richiesta usando `/triggers/<trigger-name>/run` tramite l'API REST di Azure o tramite Gestione API. Tuttavia, questo scenario richiede ancora l'autenticazione per l'API REST di Azure. Tutti gli eventi vengono visualizzati nel log di controllo di Azure. Assicurarsi di impostare i criteri di controllo di accesso di conseguenza.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli di indirizzi IP in ingresso nel modello di Azure Resource Manager

Se si [automatizza la distribuzione per le app per la logica usando modelli di gestione risorse](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile specificare gli intervalli IP usando la sezione `accessControl` con la sezione `triggers` nella definizione di risorsa dell'app per la logica, ad esempio:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Aggiungere Azure Active Directory OAuth o un'altra sicurezza

Per aggiungere altri protocolli di autorizzazione all'app per la logica, provare a usare il servizio [gestione API di Azure](../api-management/api-management-key-concepts.md) . Questo servizio consente di esporre l'app per la logica come API e offre funzionalità avanzate di monitoraggio, sicurezza, criteri e documentazione per qualsiasi endpoint. Gestione API può esporre un endpoint pubblico o privato per l'app per la logica. Per autorizzare l'accesso a questo endpoint, è possibile usare [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), un [certificato client](#client-certificate-authentication)o altri standard di sicurezza per autorizzare l'accesso a tale endpoint. Quando Gestione API riceve una richiesta, il servizio invia la richiesta all'app per la logica, eseguendo anche eventuali trasformazioni necessarie e restrizioni. Per consentire solo a gestione API di attivare l'app per la logica, è possibile usare le impostazioni dell'intervallo IP in ingresso dell'app per la logica.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Accesso alle operazioni dell'app per la logica

È possibile consentire solo a utenti o gruppi specifici di eseguire attività specifiche, ad esempio la gestione, la modifica e la visualizzazione di app per la logica. Per controllare le autorizzazioni, usare il [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) per poter assegnare ruoli personalizzati o predefiniti ai membri nella sottoscrizione di Azure:

* [Collaboratore app](../role-based-access-control/built-in-roles.md#logic-app-contributor)per la logica: consente di gestire le app per la logica, ma non di modificarne l'accesso.

* [Operatore app](../role-based-access-control/built-in-roles.md#logic-app-operator)per la logica: consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle.

Per impedire la modifica o l'eliminazione delle app per la logica da parte di altri utenti è inoltre possibile usare i [blocchi per le risorse di Azure](../azure-resource-manager/resource-group-lock-resources.md). Questa funzionalità impedisce ad altri utenti di modificare o eliminare le risorse di produzione.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Accesso ai dati della cronologia di esecuzione

Durante l'esecuzione di un' [app per la](../security/fundamentals/encryption-atrest.md)logica, tutti i dati vengono [crittografati durante il transito](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) con Transport Layer Security (TLS) e inattivi. Al termine dell'esecuzione dell'app per la logica, è possibile visualizzare la cronologia dell'esecuzione, inclusi i passaggi eseguiti con lo stato, la durata, gli input e gli output per ogni azione. Questo approfondimento fornisce informazioni dettagliate sulle modalità di esecuzione dell'app per la logica e su dove è possibile iniziare la risoluzione dei problemi che si verificano.

Quando si visualizza la cronologia di esecuzione dell'app per la logica, app per la logica autentica l'accesso e quindi fornisce collegamenti agli input e agli output per le richieste e le risposte per ogni esecuzione. Tuttavia, per le azioni che gestiscono password, segreti, chiavi o altre informazioni riservate, si desidera impedire ad altri utenti di visualizzare e accedere a tali dati. Ad esempio, se l'app per la logica ottiene un segreto da [Azure Key Vault](../key-vault/key-vault-overview.md) da usare durante l'autenticazione di un'azione http, si vuole nascondere tale segreto dalla visualizzazione.

Per controllare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, sono disponibili le opzioni seguenti:

* [Limitare l'accesso in base all'intervallo di indirizzi IP](#restrict-ip).

  Questa opzione consente di proteggere l'accesso alla cronologia di esecuzione in base alle richieste provenienti da un intervallo di indirizzi IP specifico.

* [Nascondere i dati dalla cronologia di esecuzione usando l'offuscamento](#obfuscate).

  In molti trigger e azioni è possibile nascondere gli input, gli output o entrambi dalla cronologia di esecuzione di un'app per la logica.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Limita l'accesso in base all'intervallo di indirizzi IP

È possibile limitare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, in modo che solo le richieste provenienti da intervalli di indirizzi IP specifici possano visualizzare tali dati. Ad esempio, per impedire a tutti gli utenti di accedere a input e output, specificare un intervallo di indirizzi IP come `0.0.0.0-0.0.0.0`. Solo una persona con autorizzazioni di amministratore può rimuovere questa restrizione, che offre la possibilità di accedere "just-in-Time" ai dati dell'app per la logica. È possibile specificare gli intervalli IP da limitare usando il portale di Azure o in un modello di Azure Resource Manager usato per la distribuzione dell'app per la logica.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Limitare gli intervalli IP nel portale di Azure

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso (Configurazione del controllo di accesso)**  > **Allowed inbound IP addresses (Indirizzi IP in ingresso consentiti)** , selezionare **Specific IP ranges (Intervalli IP specifici)** .

1. In **Intervalli IP per i contenuti**, specificare gli intervalli di indirizzi IP che possono accedere al contenuto da input e output. 

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli IP nel modello di Azure Resource Manager

Se si [automatizza la distribuzione per le app per la logica usando modelli di gestione risorse](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile specificare gli intervalli IP usando la sezione `accessControl` con la sezione `contents` nella definizione di risorsa dell'app per la logica, ad esempio:

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

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

   ![Aprire l'app per la logica in progettazione app per la logica](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Nel trigger o nell'azione in cui si desidera proteggere i dati, selezionare il pulsante con i puntini di sospensione ( **...** ) e quindi selezionare **Impostazioni**.

   ![Apri impostazioni trigger o azione](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Attivare gli **input protetti**, gli **output protetti**o entrambi. Al termine, fare clic su **Fine**.

   ![Attiva input o output protetti](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   L'azione o il trigger Mostra ora un'icona di blocco nella barra del titolo.

   ![Barra del titolo azione o trigger Mostra icona blocco](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   I token che rappresentano output protetti dalle azioni precedenti mostrano anche icone di blocco. Ad esempio, quando si seleziona un output di questo tipo dall'elenco di contenuto dinamico da usare in un'azione, il token Mostra un'icona di blocco.

   ![Selezionare il token per l'output protetto](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Dopo l'esecuzione dell'app per la logica, è possibile visualizzare la cronologia per l'esecuzione.

   1. Nel riquadro di **Panoramica** dell'app per la logica selezionare l'esecuzione che si vuole visualizzare.

   1. Nel riquadro **esecuzione App** per la logica, espandere le azioni che si desidera esaminare.

      Se si sceglie di proteggere gli input e gli output, questi valori vengono ora nascosti.

      ![Input e output nascosti nella cronologia di esecuzione](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteggere gli input e gli output nella visualizzazione codice

Nel trigger o nella definizione di azione sottostante aggiungere o aggiornare la matrice di `runtimeConfiguration.secureData.properties` con uno o entrambi i valori seguenti:

* `"inputs"`: protegge gli input nella cronologia di esecuzione.
* `"outputs"`: protegge gli output nella cronologia di esecuzione.

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

* Quando si proteggono gli input o gli output in un trigger o un'azione, app per la logica non invia i dati protetti ad Azure Log Analytics. Non è inoltre possibile aggiungere [proprietà rilevate](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) a tale trigger o azione per il monitoraggio.

* L' [API app per la logica per la gestione della cronologia del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/) non restituisce output protetti.

* Per proteggere gli output da un'azione che protegge gli input o USA in modo esplicito gli output protetti, attivare manualmente gli **output protetti** in tale azione.

* Assicurarsi di attivare gli **input protetti** o gli **output protetti** nelle azioni downstream in cui si prevede che la cronologia di esecuzione protegga i dati.

  **Impostazione degli output protetti**

  Quando si attivano manualmente gli **output protetti** in un trigger o un'azione, le app per la logica proteggono questi output nella cronologia di esecuzione. Se un'azione downstream USA in modo esplicito questi output protetti come input, app per la logica nasconde gli input dell'azione nella cronologia di esecuzione, ma *non Abilita* l'impostazione degli **input protetti** dell'azione.

  ![Output protetti come input e effetto downstream sulla maggior parte delle azioni](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Le azioni compose, parse JSON e Response hanno solo l'impostazione degli **input protetti** . Se attivata, l'impostazione nasconde anche gli output di tali azioni. Se queste azioni usano in modo esplicito gli output protetti upstream come input, app per la logica nasconde gli input e gli output di tali azioni, ma *non Abilita* l'impostazione degli **input protetti** di queste azioni. Se un'azione downstream USA in modo esplicito gli output nascosti dalle azioni compose, parse JSON o Response come input, app per *la logica non nasconde gli input o gli output dell'azione downstream*.

  ![Output protetti come input con effetto downstream su azioni specifiche](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Impostazione degli input protetti**

  Quando si attivano manualmente gli **input protetti** in un trigger o un'azione, le app per la logica proteggono questi input nella cronologia di esecuzione. Se un'azione downstream USA in modo esplicito gli output visibili da tale trigger o azione come input, app per la logica nasconde gli input dell'azione downstream nella cronologia di esecuzione, ma *non Abilita* gli **input protetti** in questa azione e non nasconde gli output dell'azione.

  ![Input protetti e effetto downstream sulla maggior parte delle azioni](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se le azioni compose, parse JSON e Response usano in modo esplicito gli output visibili del trigger o dell'azione con gli input protetti, app per la logica nasconde gli input e gli output di queste azioni, ma *non Abilita* l'impostazione degli **input protetti** dell'azione. Se un'azione downstream USA in modo esplicito gli output nascosti dalle azioni compose, parse JSON o Response come input, app per *la logica non nasconde gli input o gli output dell'azione downstream*.

  ![Input protetti e effetto downstream su azioni specifiche](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Accesso agli input di parametri

Se si esegue la distribuzione in ambienti diversi, provare a parametrizzazione i valori nella definizione del flusso di lavoro che variano in base a tali ambienti. In questo modo, è possibile evitare i dati hardcoded usando un modello di [Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md) per distribuire l'app per la logica, proteggere i dati sensibili definendo parametri protetti e passare i dati come input distinti tramite i [parametri del modello](../azure-resource-manager/template-parameters.md) usando un [file di parametri](../azure-resource-manager/resource-manager-parameter-files.md).

Se ad esempio si autenticano azioni HTTP con [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), è possibile definire e proteggere i parametri che accettano l'ID client e il segreto client usati per l'autenticazione. Per definire questi parametri nell'app per la logica, usare la sezione `parameters` nella definizione del flusso di lavoro dell'app per la logica e Gestione risorse modello per la distribuzione. Per nascondere i valori dei parametri che non si vuole visualizzare quando si modifica l'app per la logica o si visualizza la cronologia di esecuzione, definire i parametri usando il tipo `securestring` o `secureobject` e usare la codifica in base alle esigenze. I parametri con questo tipo non vengono restituiti con la definizione di risorsa e non sono accessibili durante la visualizzazione della risorsa dopo la distribuzione. Per accedere a questi valori di parametro durante la fase di esecuzione, usare l'espressione `@parameters('<parameter-name>')` all'interno della definizione del flusso di lavoro. Questa espressione viene valutata solo in fase di esecuzione ed è descritta dal [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se si usa un parametro in un'intestazione o in un corpo della richiesta, tale parametro potrebbe essere visibile quando si visualizza la cronologia di esecuzione dell'app per la logica e la richiesta HTTP in uscita. Assicurarsi di impostare anche i criteri di accesso al contenuto di conseguenza. È anche possibile usare l' [offuscamento](#obfuscate) per nascondere gli input e gli output nella cronologia di esecuzione. Le intestazioni di autorizzazione non sono mai visibili tramite input o output. Se quindi viene usato un segreto, questo non sarà recuperabile.

Per ulteriori informazioni, vedere le sezioni seguenti in questo argomento:

* [Proteggere i parametri nelle definizioni del flusso di lavoro](#secure-parameters-workflow)
* [Nascondere i dati dalla cronologia di esecuzione usando l'offuscamento](#obfuscate)

Se si [automatizza la distribuzione per le app per la logica usando modelli di gestione risorse](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile definire i [parametri di modello](../azure-resource-manager/template-parameters.md)protetti, che vengono valutati in fase di distribuzione, usando i tipi di `securestring` e `secureobject`. Per definire i parametri del modello, usare la sezione del `parameters` di primo livello del modello, che è separata e diversa dalla sezione `parameters` della definizione del flusso di lavoro. Per specificare i valori per i parametri del modello, usare un [file di parametri](../azure-resource-manager/resource-manager-parameter-files.md)separato.

Se, ad esempio, si usano i segreti, è possibile definire e usare i parametri di modello protetti che recuperano i segreti da [Azure Key Vault](../key-vault/key-vault-overview.md) durante la distribuzione. È quindi possibile fare riferimento all'insieme di credenziali delle chiavi e al segreto nel file dei parametri. Per altre informazioni, vedere gli argomenti seguenti:

* [Passare i valori sensibili alla distribuzione usando Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Proteggere i parametri nei modelli di Azure Resource Manager](#secure-parameters-deployment-template) più avanti in questo argomento

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteggere i parametri nelle definizioni del flusso di lavoro

Per proteggere le informazioni riservate nella definizione del flusso di lavoro dell'app per la logica, usare parametri protetti in modo che queste informazioni non siano visibili dopo il salvataggio dell'app per la logica. Si supponga, ad esempio, che un'azione HTTP richieda l'autenticazione di base, che usa un nome utente e una password. Nella definizione del flusso di lavoro, la sezione `parameters` definisce i parametri `basicAuthPasswordParam` e `basicAuthUsernameParam` usando il tipo di `securestring`. La definizione dell'azione fa quindi riferimento a questi parametri nella sezione `authentication`.

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

Un [modello di gestione risorse](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) per un'app per la logica include più sezioni `parameters`. Per proteggere password, chiavi, segreti e altre informazioni riservate, definire parametri protetti a livello di modello e di definizione del flusso di lavoro usando il tipo `securestring` o `secureobject`. È quindi possibile archiviare questi valori in [Azure Key Vault](../key-vault/key-vault-overview.md) e usare il [file di parametri](../azure-resource-manager/resource-manager-parameter-files.md) per fare riferimento all'insieme di credenziali delle chiavi e al segreto. Il modello recupera quindi tali informazioni in fase di distribuzione. Per altre informazioni, vedere [passare valori sensibili alla distribuzione usando Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Di seguito sono riportate altre informazioni su queste `parameters` sezioni:

* Al livello principale del modello, una sezione `parameters` definisce i parametri per i valori utilizzati dal modello durante la *distribuzione*. Questi valori, ad esempio, possono includere stringhe di connessione per un ambiente di distribuzione specifico. È quindi possibile archiviare questi valori in un [file di parametri](../azure-resource-manager/resource-manager-parameter-files.md)separato, rendendo più semplice la modifica di questi valori.

* All'interno della definizione di risorsa dell'app per la logica, ma all'esterno della definizione del flusso di lavoro, una sezione `parameters` specifica i valori per i parametri della definizione del flusso di lavoro. In questa sezione è possibile assegnare questi valori usando espressioni di modello che fanno riferimento ai parametri del modello. Queste espressioni vengono valutate in fase di distribuzione.

* All'interno della definizione del flusso di lavoro, una sezione `parameters` definisce i parametri usati dall'app per la logica in fase di esecuzione. È quindi possibile fare riferimento a questi parametri nel flusso di lavoro dell'app per la logica usando le espressioni di definizione del flusso di lavoro, che vengono valutate in fase di esecuzione

Questo modello di esempio con più definizioni di parametro protette che usano il tipo di `securestring`:

| Nome parametro | DESCRIZIONE |
|----------------|-------------|
| `TemplatePasswordParam` | Parametro di modello che accetta una password che viene quindi passata al parametro `basicAuthPasswordParam` della definizione del flusso di lavoro. |
| `TemplateUsernameParam` | Parametro di modello che accetta un nome utente che viene quindi passato al parametro `basicAuthUserNameParam` della definizione del flusso di lavoro. |
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

Ecco alcuni modi in cui è possibile proteggere gli endpoint che ricevono chiamate o richieste dall'app per la logica:

* Aggiungere l'autenticazione alle richieste in uscita.

  Quando si lavora con un trigger basato su HTTP o un'azione che esegue chiamate in uscita, ad esempio HTTP, HTTP + spavalderia o webhook, è possibile aggiungere l'autenticazione alla richiesta inviata dall'app per la logica. Ad esempio, è possibile usare questi tipi di autenticazione:

  * [Autenticazione di base](#basic-authentication)

  * [Autenticazione con certificato client](#client-certificate-authentication)

  * [Autenticazione Active Directory OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticazione identità gestita](#managed-identity-authentication)
  
  Per ulteriori informazioni, vedere [aggiungere l'autenticazione alle chiamate in uscita](#add-authentication-outbound) più avanti in questo argomento.

* Limitare l'accesso dagli indirizzi IP delle app per la logica.

  Tutte le chiamate agli endpoint da app per la logica provengono da indirizzi IP specifici designati basati sulle aree delle app per la logica. È possibile aggiungere un filtro che accetti le richieste solo da tali indirizzi IP. Per ottenere questi indirizzi IP, vedere [limiti e configurazione per app per la logica di Azure](logic-apps-limits-and-config.md#configuration).

* Proteggere le connessioni ai sistemi locali.

  Le app per la logica offrono integrazione con tali servizi per garantire una comunicazione locale sicura e affidabile.

  * Gateway dati locale

    Molti connettori gestiti in app per la logica di Azure forniscono connessioni sicure ai sistemi locali, ad esempio file System, SQL, SharePoint e DB2. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni su [come funziona il gateway dati locale](logic-apps-gateway-install.md#gateway-cloud-service).

  * Connettersi tramite gestione API di Azure

    [Gestione API di Azure](../api-management/api-management-key-concepts.md) dispone di numerose opzioni di connettività locale, tra cui l'integrazione ExpressRoute e per rete privata locale da sito a sito per la protezione del proxy e la comunicazione con i sistemi locali. Dal flusso di lavoro dell'app per la logica nella finestra di progettazione dell'app per la logica è possibile selezionare un'API esposta da gestione API, che consente di accedere rapidamente ai sistemi locali.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Aggiungere l'autenticazione alle chiamate in uscita

Gli endpoint HTTP e HTTPS supportano vari tipi di autenticazione. In base al trigger o all'azione che si utilizza per effettuare chiamate in uscita o richieste che accedono a questi endpoint, è possibile scegliere tra diversi intervalli di tipi di autenticazione. Per assicurarsi di proteggere le informazioni riservate gestite dall'app per la logica, usare i parametri protetti e codificare i dati in modo necessario. Per altre informazioni sull'uso e la protezione dei parametri, vedere [accesso agli input dei parametri](#secure-action-parameters).

| Tipo di autenticazione | Supportato da |
|---------------------|--------------|
| [Basic](#basic-authentication) | Gestione API di Azure, app Azure Services, HTTP, HTTP + spavalderia, webhook HTTP |
| [Certificato client](#client-certificate-authentication) | Gestione API di Azure, app Azure Services, HTTP, HTTP + spavalderia, webhook HTTP |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Gestione API di Azure, servizi app Azure, funzioni di Azure, HTTP, HTTP + spavalderia, webhook HTTP |
| [Non elaborati](#raw-authentication) | Gestione API di Azure, servizi app Azure, funzioni di Azure, HTTP, HTTP + spavalderia, webhook HTTP |
| [Identità gestita](#managed-identity-authentication) (solo assegnata dal sistema) | Gestione API di Azure, servizi app Azure, funzioni di Azure, HTTP, HTTP + spavalderia, webhook HTTP |
|||

> [!NOTE]
> Nella finestra di progettazione dell'app per la logica la proprietà **Authentication** potrebbe essere nascosta in alcuni trigger e azioni in cui è possibile specificare il tipo di autenticazione. Per fare in modo che la proprietà venga visualizzata in questi casi, nel trigger o nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare **autenticazione**. Per altre informazioni, vedere [autenticare l'accesso con identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticazione di base

Se è disponibile l'opzione di [base](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) , specificare i valori delle proprietà seguenti:

| Proprietà (finestra di progettazione) | Property (JSON) | obbligatori | Valore | DESCRIZIONE |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticazione** | `type` | Sì | Basic | Tipo di autenticazione da usare |
| **Nome utente** | `username` | Sì | <*nome utente*>| Il nome utente per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
| **Password** | `password` | Sì | > *password* < | La password per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
||||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager per automatizzare la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile usare le espressioni per accedere a questi valori di parametri in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica il `type` di autenticazione come `Basic` e usa la [funzione Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

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

Se l'opzione [certificato client](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) è disponibile, specificare i valori delle proprietà seguenti:

| Proprietà (finestra di progettazione) | Property (JSON) | obbligatori | Valore | DESCRIZIONE |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticazione** | `type` | Sì | **Certificato client** <br>oppure <br>`ClientCertificate` | Il tipo di autenticazione da usare per i certificati client di Secure Sockets Layer (SSL). Benché siano supportati i certificati autofirmati, non sono supportati i certificati autofirmati per SSL. |
| **PFX** | `pfx` | Sì | <*codificato-pfx-file-contenuto*> | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) <p><p>Per convertire il file PFX in formato con codifica Base64, è possibile usare PowerShell attenendosi alla procedura seguente: <p>1. salvare il contenuto del certificato in una variabile: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. convertire il contenuto del certificato usando la funzione `ToBase64String()` e salvare il contenuto in un file di testo: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Password** | `password`| Vedere la descrizione | <*password per il file pfx*> | Password per l'accesso al file PFX. <p><p>**Nota**: il valore di questa proprietà è obbligatorio quando si lavora nella finestra di progettazione dell'app per la logica e *non* è necessario quando si lavora nella visualizzazione codice. |
|||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager per automatizzare la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile usare le espressioni per accedere a questi valori di parametri in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica il `type` di autenticazione come `ClientCertificate` e usa la [funzione Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

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

Per ulteriori informazioni sulla protezione dei servizi tramite l'autenticazione del certificato client, vedere gli argomenti seguenti:

* [Proteggere le API usando l'autenticazione con certificati client in gestione API di Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Proteggere i servizi back-end usando l'autenticazione con certificati client in gestione API di Azure](../api-management/api-management-howto-mutual-certificates.md)
* [Proteggere il servizio RESTful usando certificati client](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
* [Credenziali del certificato per l'autenticazione dell'applicazione](../active-directory/develop/active-directory-certificate-credentials.md)
* [Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Autenticazione Azure Active Directory OAuth

Se è disponibile l'opzione [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) , specificare i valori delle proprietà seguenti:

| Proprietà (finestra di progettazione) | Property (JSON) | obbligatori | Valore | DESCRIZIONE |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticazione** | `type` | Sì | **Active Directory OAuth** <br>oppure <br>`ActiveDirectoryOAuth` | Tipo di autenticazione da usare. App per la logica segue attualmente il [protocollo OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Inquilino** | `tenant` | Sì | <*tenant-ID*> | L'ID tenant per il tenant di Azure AD |
| **Destinatari** | `audience` | Sì | <*resource-to-authorize*> | La risorsa che si vuole usare per l'autorizzazione, ad esempio `https://management.core.windows.net/` |
| **ID client** | `clientId` | Sì | <*client-ID*> | L'ID client per l'app richiedente l'autorizzazione |
| **Tipo di credenziale** | `credentialType` | Sì | Certificate <br>oppure <br>Segreto | Tipo di credenziale utilizzato dal client per richiedere l'autorizzazione. Questa proprietà e il valore non vengono visualizzati nella definizione sottostante dell'app per la logica, ma determinano le proprietà visualizzate per il tipo di credenziali selezionato. |
| **Segreto** | `secret` | Sì, ma solo per il tipo di credenziale "Secret" | <> *Secret client* | Il segreto client per la richiesta dell'autorizzazione |
| **PFX** | `pfx` | Sì, ma solo per il tipo di credenziale "Certificate" | <*codificato-pfx-file-contenuto*> | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) |
| **Password** | `password` | Sì, ma solo per il tipo di credenziale "Certificate" | <*password per il file pfx*> | Password per accedere al file PFX. |
| **Autorità** | `authority` | No | <*URL-for-authority-token-issuer*> | URL dell'autorità che fornisce il token di autenticazione. Per impostazione predefinita, questo valore è `https://login.windows.net`. <p>**Nota**: per rendere visibile questa proprietà nella finestra di progettazione, nel trigger o nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare **autorità**. |
|||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager per automatizzare la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile usare le espressioni per accedere a questi valori di parametri in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica il `type` di autenticazione come `ActiveDirectoryOAuth`, il tipo di credenziale come `Secret`e usa la [funzione Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

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

### <a name="raw-authentication"></a>Autenticazione non elaborata

Se l'opzione non **elaborata** è disponibile, è possibile usare questo tipo di autenticazione quando è necessario usare [schemi di autenticazione](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) che non seguono il [protocollo OAuth 2,0](https://oauth.net/2/). Con questo tipo, si crea manualmente il valore dell'intestazione di autorizzazione inviato con la richiesta in uscita e si specifica il valore dell'intestazione nel trigger o nell'azione.

Ecco ad esempio un'intestazione di esempio per una richiesta HTTPS che segue il [protocollo OAuth 1,0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Nel trigger o nell'azione che supporta l'autenticazione non elaborata specificare i valori delle proprietà seguenti:

| Proprietà (finestra di progettazione) | Property (JSON) | obbligatori | Valore | DESCRIZIONE |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticazione** | `type` | Sì | Non elaborati | Tipo di autenticazione da usare |
| **Valore** | `value` | Sì | <*authorization-header-value*> | Valore dell'intestazione di autorizzazione da usare per l'autenticazione |
||||||

Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager per automatizzare la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile usare le espressioni per accedere a questi valori di parametri in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica il `type` di autenticazione come `Raw`e usa la [funzione Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

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

Se l'opzione [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) è disponibile, l'app per la logica può usare l'identità assegnata dal sistema per autenticare l'accesso alle risorse in altri tenant Azure Active Directory (Azure ad) senza accedere. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Scopri di più sui [servizi di Azure che supportano identità gestite per l'autenticazione Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Prima che l'app per la logica possa usare l'identità assegnata dal sistema, seguire i passaggi descritti in [autenticazione dell'accesso alle risorse di Azure usando identità gestite in app per la logica di Azure](../logic-apps/create-managed-service-identity.md). Questi passaggi abilitano l'identità gestita nell'app per la logica e configurano l'accesso dell'identità alla risorsa di Azure di destinazione.

2. Prima che una funzione di Azure possa usare l'identità assegnata dal sistema, [abilitare prima l'autenticazione per funzioni di Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

3. Nel trigger o nell'azione in cui si vuole usare l'identità gestita specificare i valori delle proprietà seguenti:

   | Proprietà (finestra di progettazione) | Property (JSON) | obbligatori | Valore | DESCRIZIONE |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticazione** | `type` | Sì | **Identità gestita** <br>oppure <br>`ManagedServiceIdentity` | Tipo di autenticazione da usare |
   | **Destinatari** | `audience` | Sì | <*destinazione-Resource-ID*> | ID risorsa per la risorsa di destinazione a cui si vuole accedere. <p>Ad esempio, `https://storage.azure.com/` rende validi i token di accesso per l'autenticazione per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare un URL del servizio radice, ad esempio `https://fabrikamstorageaccount.blob.core.windows.net` per un account di archiviazione specifico. <p>**Nota**: questa proprietà potrebbe essere nascosta in alcuni trigger o azioni. Per rendere visibile questa proprietà, nel trigger o nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare **audience**. <p><p>**Importante**: assicurarsi che l'ID risorsa di destinazione corrisponda esattamente al valore previsto da Azure ad, incluse le barre finali richieste. Quindi, l'ID di risorsa `https://storage.azure.com/` per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID risorsa per un account di archiviazione specifico non richiede una barra finale. Per trovare questi ID di risorsa, vedere [servizi di Azure che supportano Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Quando si usano [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager per automatizzare la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile usare le espressioni per accedere a questi valori di parametri in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica il `type` di autenticazione come `ManagedServiceIdentity` e usa la [funzione Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Automatizzare la distribuzione per le app per la logica di Azure](logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorare le app per la logica](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosi degli errori delle app per la logica](logic-apps-diagnosing-failures.md)  
* [Automatizzare la distribuzione delle app per la logica](logic-apps-azure-resource-manager-templates-overview.md)
