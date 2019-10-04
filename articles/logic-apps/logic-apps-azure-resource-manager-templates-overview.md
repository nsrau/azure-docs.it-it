---
title: Panoramica-automatizzare la distribuzione per le app per la logica di Azure
description: Informazioni sui modelli di Azure Resource Manager per automatizzare la distribuzione per le app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: f2c6676284e8ed58f1626ab824aa7a7c9c456a31
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494450"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Panoramica Automatizzare la distribuzione per le app per la logica di Azure usando modelli di Azure Resource Manager

Quando si è pronti per automatizzare la creazione e la distribuzione dell'app per la logica, è possibile espandere la definizione del flusso di lavoro sottostante dell'app per la logica in un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Questo modello definisce l'infrastruttura, le risorse, i parametri e altre informazioni per il provisioning e la distribuzione dell'app per la logica. Definendo i parametri per i valori che variano in base alla distribuzione, nota anche come *parametrizzazione*, è possibile distribuire ripetutamente e in modo coerente le app per la logica in base alle diverse esigenze di distribuzione.

Se ad esempio si esegue la distribuzione in ambienti per sviluppo, test e produzione, è probabile che si usino stringhe di connessione diverse per ogni ambiente. È possibile dichiarare parametri di modello che accettano stringhe di connessione diverse e quindi archiviare tali stringhe in un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)separato. In questo modo, è possibile modificare tali valori senza dover aggiornare e ridistribuire il modello. Per gli scenari in cui sono presenti valori di parametro sensibili o che devono essere protetti, ad esempio password e segreti, è possibile archiviare tali valori in [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) e fare in modo che il file di parametri recuperi tali valori. Tuttavia, in questi scenari è necessario eseguire nuovamente la distribuzione per recuperare i valori correnti.

Questa panoramica descrive gli attributi in un modello di Gestione risorse che include una definizione del flusso di lavoro dell'app per la logica. Sia il modello che la definizione del flusso di lavoro usano la sintassi JSON, ma esistono alcune differenze perché la definizione del flusso di lavoro segue lo [schema del linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md). Le espressioni di modello e le espressioni di definizione del flusso di lavoro, ad esempio, differiscono per quanto [riguarda i parametri](#parameter-references) e i valori che possono accettare.

> [!TIP]
> Per il modo più semplice per ottenere un modello di app per la logica con parametri valido, prevalentemente pronto per la distribuzione, usare Visual Studio (versione Community gratuita o versione successiva) e gli strumenti delle app per la logica di Azure per Visual Studio. È quindi possibile [creare l'app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) o [trovare e scaricare un'app per la logica esistente da Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> In alternativa, è possibile creare modelli di app per [la logica usando Azure PowerShell con il modulo LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

L'app per la logica di esempio in questo argomento usa un [trigger di Office 365 Outlook](/connectors/office365/) che viene attivato all'arrivo di un nuovo messaggio di posta elettronica e un' [azione di archiviazione BLOB di Azure](/connectors/azureblob/) che crea un BLOB per il corpo del messaggio di posta elettronica e carica il BLOB in un contenitore di archiviazione di Azure. Negli esempi viene inoltre illustrato come parametrizzare i valori che variano in fase di distribuzione.

Per ulteriori informazioni sui modelli di Gestione risorse, vedere gli argomenti seguenti:

* [Struttura e sintassi del modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Procedure consigliate per i modelli di Azure Resource Manager](../azure-resource-manager/template-best-practices.md)
* [Sviluppare i modelli di Azure Resource Manager per la coerenza cloud](../azure-resource-manager/templates-cloud-consistency.md)

Per i modelli di app per la logica di esempio, vedere questi esempi:

* [Modello completo](#full-example-template) usato per gli esempi di questo argomento
* [Esempio di modello di app](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) per la logica introduttiva in GitHub

Per informazioni sulle risorse del modello specifiche per le app per la logica, gli account di integrazione e gli elementi dell'account di integrazione, vedere [tipi di risorse Microsoft. Logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Struttura del modello

Al livello principale, un modello di Gestione risorse segue questa struttura, descritta completamente nell'argomento relativo alla [struttura e alla sintassi del modello Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Per un modello di app per la logica, è possibile usare principalmente questi oggetti modello:

| Attributo | Descrizione |
|-----------|-------------|
| `parameters` | Dichiara i [parametri del modello](../azure-resource-manager/resource-group-authoring-templates.md#parameters) per accettare i valori da usare durante la creazione e la personalizzazione delle risorse per la distribuzione in Azure. Ad esempio, questi parametri accettano i valori per il nome e il percorso dell'app per la logica, le connessioni e altre risorse necessarie per la distribuzione. È possibile archiviare questi valori di parametro in un [file di parametri](#template-parameter-files), descritto più avanti in questo argomento. Per informazioni generali, vedere [parametri: Gestione risorse la struttura e la sintassi del modello](../azure-resource-manager/resource-group-authoring-templates.md#parameters). |
| `resources` | Definisce le [risorse](../azure-resource-manager/resource-group-authoring-templates.md#resources) da creare o aggiornare e distribuire in un gruppo di risorse di Azure, ad esempio l'app per la logica, le connessioni, gli account di archiviazione di Azure e così via. Per informazioni generali, vedere [risorse-Gestione risorse la struttura e la sintassi del modello](../azure-resource-manager/resource-group-authoring-templates.md#resources). |
||||

Il modello di app per la logica usa questo formato di nome file:

**<*Logic-app-name*>. JSON**

> [!IMPORTANT]
> La sintassi del modello fa distinzione tra maiuscole e minuscole, quindi assicurarsi di usare un involucro coerente. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parametri del modello

Un modello di app per la `parameters` logica include più oggetti che esistono a livelli diversi ed eseguono funzioni diverse. Ad esempio, al livello principale, è possibile dichiarare [parametri di modello](../azure-resource-manager/resource-group-authoring-templates.md#parameters) per i valori da accettare e usare durante la distribuzione durante la creazione e la distribuzione di risorse in Azure, ad esempio:

* App per la logica
* Connessioni utilizzate dalla logica per accedere ad altri servizi e sistemi tramite [connettori gestiti](../connectors/apis-list.md)
* Altre risorse necessarie per la distribuzione dell'app per la logica

  Ad esempio, se l'app per la logica usa un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per gli scenari business-to-business (B2B), l'oggetto `parameters` di primo livello del modello dichiara il parametro che accetta l'ID risorsa per l'account di integrazione.

Di seguito è riportata la struttura e la sintassi generali per una definizione di parametro, descritta in modo completo dai [parametri, gestione risorse struttura del modello e la sintassi](../azure-resource-manager/resource-group-authoring-templates.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Questo esempio Mostra solo i parametri del modello per i valori usati per creare e distribuire queste risorse in Azure:

* Nome e percorso dell'app per la logica
* ID da usare per un account di integrazione collegato all'app per la logica

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Ad eccezione dei parametri che gestiscono valori sensibili o che devono essere protetti, ad esempio nomi utente, password e segreti, tutti questi parametri includono `defaultValue` attributi, sebbene in alcuni casi i valori predefiniti siano valori vuoti. I valori di distribuzione da utilizzare per questi parametri di modello vengono forniti dal [file dei parametri](#template-parameter-files) di esempio descritto più avanti in questo argomento.

Per proteggere i parametri del modello, vedere gli argomenti seguenti:

* [Raccomandazioni sulla sicurezza per i parametri del modello](../azure-resource-manager/template-best-practices.md#parameters)
* [Parametri di modello protetti](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Passare i valori dei parametri protetti con Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

Altri oggetti modello spesso fanno riferimento a parametri di modello in modo che possano usare i valori che passano attraverso parametri di modello, ad esempio:

* L' [oggetto risorse del modello](#template-resources), descritto più avanti in questo argomento, definisce ogni risorsa in Azure che si vuole creare e distribuire, ad esempio la [definizione di risorsa dell'app](#logic-app-resource-definition)per la logica. Queste risorse usano spesso valori di parametri di modello, ad esempio il nome e il percorso dell'app per la logica e le informazioni di connessione.

* A un livello più profondo nella definizione di risorsa dell'app per la logica, l' [oggetto parametri della definizione del flusso di lavoro](#workflow-definition-parameters) dichiara i parametri per i valori da usare nel runtime dell'app per la logica. È ad esempio possibile dichiarare i parametri di definizione del flusso di lavoro per il nome utente e la password utilizzati da un trigger HTTP per l'autenticazione. Per specificare i valori per i parametri di definizione del flusso `parameters` di lavoro, usare l'oggetto *esterno* alla definizione del flusso di lavoro ma ancora *all'interno* della definizione di risorsa dell'app per la logica. In questo oggetto `parameters` esterno è possibile fare riferimento ai parametri di modello dichiarati in precedenza, che possono accettare valori in fase di distribuzione da un file di parametri.

Quando si fa riferimento a parametri, le espressioni e le funzioni del modello utilizzano una sintassi diversa e si comportano in modo diverso dalle espressioni e dalle funzioni Per ulteriori informazioni su queste differenze, vedere [riferimenti ai parametri](#parameter-references) più avanti in questo argomento.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Procedure consigliate-parametri di modello

Di seguito sono riportate alcune procedure consigliate per la definizione dei parametri:

* Dichiarare i parametri solo per i valori che variano in base alle esigenze di distribuzione. Non dichiarare i parametri per i valori che rimaneno invariati nei diversi requisiti di distribuzione.

* Includere l' `defaultValue` attributo, che può specificare valori vuoti, per tutti i parametri ad eccezione dei valori sensibili o che devono essere protetti. Usare sempre parametri protetti per i nomi utente, le password e i segreti. Per nascondere o proteggere i valori dei parametri sensibili, seguire le istruzioni fornite negli argomenti seguenti:

  * [Raccomandazioni sulla sicurezza per i parametri del modello](../azure-resource-manager/template-best-practices.md#parameters)

  * [Parametri di modello protetti](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Passare i valori dei parametri protetti con Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

* Per distinguere i nomi dei parametri di modello dai nomi dei parametri di definizione del flusso di lavoro, è possibile usare nomi di parametri di modello descrittivi, ad esempio:`TemplateFabrikamPassword`

Per altre procedure consigliate sui modelli, vedere procedure consigliate [per i parametri del modello](../azure-resource-manager/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>File dei parametri del modello

Per specificare i valori per i parametri del modello, archiviare i valori in un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files). In questo modo, è possibile usare file di parametri diversi in base alle esigenze di distribuzione. Di seguito è riportato il formato del nome file da utilizzare:

* Nome file modello app per la logica:  **<nome *-app-logica*>. JSON**
* Nome file dei parametri:  **< *nome-app-logica*>. Parameters. JSON**

Di seguito è illustrata la struttura all'interno del file dei parametri, che include un riferimento a Key Vault per [passare un valore di parametro sicuro con Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Questo file di parametri di esempio specifica i valori per i parametri del modello dichiarati in precedenza in questo argomento:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Risorse del modello

Il modello include un `resources` oggetto, ovvero una matrice che contiene le definizioni per ogni risorsa da creare e distribuire in Azure, ad esempio la [definizione di risorsa dell'app](#logic-app-resource-definition)per la logica, le [definizioni delle risorse di connessione](#connection-resource-definitions)e altre risorse l'app per la logica deve essere distribuita.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> I modelli possono includere definizioni di risorse per più app per la logica, quindi assicurarsi che tutte le risorse dell'app per la logica specifichino lo stesso gruppo di risorse di Azure. Quando si distribuisce il modello in un gruppo di risorse di Azure usando Visual Studio, viene richiesto quale app per la logica si vuole aprire. Inoltre, il progetto del gruppo di risorse di Azure può contenere più di un modello, quindi assicurarsi di selezionare il file dei parametri corretto quando richiesto.

Per informazioni generali sulle risorse modello e sui relativi attributi, vedere gli argomenti seguenti:

* [Risorse-Gestione risorse struttura e la sintassi del modello](../azure-resource-manager/resource-group-authoring-templates.md#resources)
* [Procedure consigliate per le risorse modello](../azure-resource-manager/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definizione di risorsa dell'app per la logica

La definizione di risorsa dell'app per la logica `properties` inizia con l'oggetto, che include le informazioni seguenti:

* Stato dell'app per la logica in fase di distribuzione
* ID per qualsiasi account di integrazione usato dall'app per la logica
* Definizione del flusso di lavoro dell'app per la logica
* `parameters` Oggetto che imposta i valori da utilizzare in fase di esecuzione
* Altre informazioni sulle risorse sull'app per la logica, ad esempio nome, tipo, posizione e così via

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Ecco gli attributi specifici della definizione di risorsa dell'app per la logica:

| Attributo | Obbligatorio | Type | DESCRIZIONE |
|-----------|----------|------|-------------|
| `state` | Sì | String | Lo stato dell'app per la logica in `Enabled` fase di distribuzione, dove indica che `Disabled` l'app per la logica è Live e indica che l'app per la logica è inattiva. Se, ad esempio, non si è pronti per l'app per la logica, ma si vuole distribuire una versione bozza, è possibile usare `Disabled` l'opzione. |
| `integrationAccount` | No | Object | Se l'app per la logica usa un account di integrazione che archivia gli artefatti per gli scenari business-to-business (B2B), `id` questo oggetto include l'attributo, che specifica l'ID dell'account di integrazione. |
| `definition` | Sì | Object | La definizione del flusso di lavoro sottostante dell'app per la logica, che è lo stesso oggetto visualizzato nella visualizzazione codice ed è descritta completamente nell'argomento [riferimento allo schema per il linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md) . In questa definizione del flusso di `parameters` lavoro, l'oggetto dichiara i parametri per i valori da usare in fase di esecuzione dell'app per la logica. Per altre informazioni, vedere [definizione del flusso di lavoro e parametri](#workflow-definition-parameters). <p><p>Per visualizzare gli attributi nella definizione del flusso di lavoro dell'app per la logica, passare dalla "visualizzazione progettazione" alla "visualizzazione codice" nel portale di Azure o in Visual Studio oppure usando uno strumento come [Azure Resource Explorer](http://resources.azure.com). |
| `parameters` | No | Object | [Valori dei parametri di definizione del flusso di lavoro](#workflow-definition-parameters) da usare in fase di esecuzione Le definizioni dei parametri per questi valori vengono visualizzate all'interno dell' [oggetto parametri della definizione del flusso di lavoro](#workflow-definition-parameters). Inoltre, se l'app per la logica USA [connettori gestiti](../connectors/apis-list.md) per accedere ad altri servizi e sistemi, questo oggetto `$connections` include un oggetto che imposta i valori di connessione da usare in fase di esecuzione. |
| `accessControl` | No | Object | Per specificare gli attributi di sicurezza per l'app per la logica, ad esempio per limitare l'accesso IP ai trigger di richiesta o gli input e gli output della cronologia di esecuzione. Per altre informazioni, vedere [proteggere l'accesso alle app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Per informazioni sulle risorse del modello specifiche per le app per la logica, gli account di integrazione e gli elementi dell'account di integrazione, vedere [tipi di risorse Microsoft. Logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definizione del flusso di lavoro e parametri

La definizione del flusso di lavoro dell'app per `definition` la logica viene visualizzata nell'oggetto `properties` , che viene visualizzato nell'oggetto all'interno della definizione di risorsa dell'app per la logica. Questo `definition` oggetto è lo stesso oggetto visualizzato nella visualizzazione codice ed è descritto completamente nell'argomento [riferimento allo schema per il linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md) . La definizione del flusso di lavoro `parameters` include un oggetto dichiarazione interna in cui è possibile definire nuovi parametri esistenti o modificarli per i valori usati dalla definizione del flusso di lavoro in fase di esecuzione. È quindi possibile fare riferimento a questi parametri all'interno del trigger o delle azioni nel flusso di lavoro. Per impostazione predefinita, `parameters` questo oggetto è vuoto, a meno che l'app per la logica non crei connessioni ad altri servizi e sistemi tramite [connettori gestiti](../connectors/apis-list.md).

Per impostare i valori per i parametri di definizione del flusso `parameters` di lavoro, usare l'oggetto *esterno* alla definizione del flusso di lavoro ma ancora *all'interno* della definizione di risorsa dell'app per la logica. In questo oggetto `parameters` esterno è quindi possibile fare riferimento ai parametri di modello dichiarati in precedenza, che possono accettare valori in fase di distribuzione da un file di parametri.

> [!TIP]
>
> Come procedura consigliata, non fare riferimento direttamente ai parametri di modello, che vengono valutati in fase di distribuzione, dall'interno della definizione del flusso di lavoro. Dichiarare invece un parametro di definizione del flusso di lavoro, che è quindi possibile `parameters` impostare nell'oggetto che si trova al di *fuori* della definizione del flusso di lavoro ma ancora *all'interno* della definizione di risorsa dell'app per la logica. Per ulteriori informazioni, vedere [riferimenti ai parametri](#parameter-references).

Questa sintassi Mostra la posizione in cui è possibile dichiarare parametri sia a livello di modello che di definizione del flusso di lavoro insieme alla posizione in cui è possibile impostare i valori dei parametri facendo riferimento ai parametri di definizione del flusso di lavoro e del modello

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": "[parameters('<template-parameter-name>')]"
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parametri di definizione del flusso di lavoro sicuro

Per un parametro di definizione del flusso di lavoro che gestisce informazioni riservate, password, chiavi di accesso o segreti in fase di esecuzione, dichiarare `securestring` o `secureobject` modificare il parametro per usare il tipo di parametro o. È possibile fare riferimento a questo parametro in tutta la definizione del flusso di lavoro. Al livello principale del modello, dichiarare un parametro con lo stesso tipo per gestire queste informazioni in fase di distribuzione.

Per impostare il valore per il parametro di definizione del flusso di `parameters` lavoro, usare l'oggetto *esterno* alla definizione del flusso di lavoro ma ancora *all'interno* della definizione di risorsa dell'app per la logica per fare riferimento al parametro di modello. Infine, per passare il valore al parametro di modello in fase di distribuzione, archiviare tale valore in [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) e fare riferimento all'insieme di credenziali delle chiavi nel [file dei parametri](#template-parameter-files) usato dal modello in fase di distribuzione.

Questo modello di esempio Mostra come è possibile completare queste attività definendo parametri protetti quando necessario, in modo da poter archiviare i valori in Azure Key Vault:

* Dichiarare i parametri sicuri per i valori usati per autenticare l'accesso.
* Usare questi valori a livello di definizione del flusso di lavoro e del modello.
* Specificare questi valori usando un file di parametri.

**Modello**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**File dei parametri**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Procedure consigliate-parametri di definizione del flusso di lavoro

Per assicurarsi che la finestra di progettazione dell'app per la logica possa visualizzare correttamente i parametri di definizione del flusso di lavoro, seguire queste procedure consigliate:

* Includere l' `defaultValue` attributo, che può specificare valori vuoti, per tutti i parametri ad eccezione dei valori sensibili o che devono essere protetti.

* Usare sempre parametri protetti per i nomi utente, le password e i segreti. Per nascondere o proteggere i valori dei parametri sensibili, seguire le istruzioni fornite negli argomenti seguenti:

  * [Raccomandazioni sulla sicurezza per i parametri dell'azione](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Raccomandazioni sulla sicurezza per i parametri nelle definizioni del flusso di lavoro](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Passare i valori dei parametri protetti con Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

Per ulteriori informazioni sui parametri di definizione del flusso di lavoro, vedere [parametri-Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definizioni delle risorse di connessione

Quando l'app per la logica crea e usa le connessioni ad altri servizi e sistemi usando i [connettori gestiti](../connectors/apis-list.md), `resources` l'oggetto del modello contiene le definizioni delle risorse per tali connessioni.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Le definizioni delle risorse di connessione fanno riferimento ai parametri di primo livello del modello. Ciò significa che è possibile specificare questi valori in fase di distribuzione usando un file di parametri. Assicurarsi che le connessioni usino lo stesso gruppo di risorse e la stessa località di Azure dell'app per la logica.

Di seguito è riportato un esempio di definizione di risorsa per una connessione Office 365 Outlook e i parametri di modello corrispondenti:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

La definizione di risorsa dell'app per la logica funziona anche con le definizioni delle risorse di connessione nei modi seguenti:

* All'interno della definizione del flusso `parameters` di lavoro, l' `$connections` oggetto dichiara un parametro per i valori di connessione da usare in fase di esecuzione dell'app per la logica. Inoltre, il trigger o l'azione che crea una connessione utilizza i valori corrispondenti che passano tramite `$connections` questo parametro.

* Al di *fuori* della definizione del flusso di lavoro ma ancora *all'interno* della definizione `parameters` di risorsa dell'app per la logica, un altro `$connections` oggetto imposta i valori da usare in fase di esecuzione per il parametro facendo riferimento ai parametri di modello corrispondenti. Questi valori usano le espressioni di modello per fare riferimento a risorse che archiviano in modo sicuro i metadati per le connessioni nell'app per la logica.

  I metadati, ad esempio, possono includere stringhe di connessione e token di accesso, che è possibile archiviare in [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md). Per passare questi valori ai parametri del modello, si fa riferimento all'insieme di credenziali delle chiavi nel [file dei parametri](#template-parameter-files) usato dal modello in fase di distribuzione. Per ulteriori informazioni sulle differenze nei parametri di riferimento, vedere [riferimenti ai parametri](#parameter-references) più avanti in questo argomento.

  Quando si apre la definizione del flusso di lavoro dell'app per la logica nella visualizzazione codice tramite il portale di Azure `$connections` o Visual Studio, l'oggetto viene visualizzato al di fuori della definizione del flusso di lavoro, ma allo stesso livello. Questo ordinamento nella visualizzazione codice rende più semplice fare riferimento a questi parametri quando si aggiorna manualmente la definizione del flusso di lavoro:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* La definizione di risorsa dell'app per la `dependsOn` logica contiene un oggetto che specifica le dipendenze dalle connessioni usate dall'app per la logica.

Ogni connessione creata ha un nome univoco in Azure. Quando si creano più connessioni allo stesso servizio o sistema, a ogni nome di connessione viene aggiunto un numero che viene incrementato a ogni nuova connessione creata, ad esempio `office365` `office365-1`,, e così via.

Questo esempio mostra le interazioni tra la definizione di risorsa dell'app per la logica e una definizione di risorsa di connessione per Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parametri di connessione protetti

Per un parametro di connessione che gestisce informazioni riservate, password, chiavi di accesso o segreti, la definizione di risorsa della `parameterValues` connessione include un oggetto che specifica questi valori nel formato della coppia nome-valore. Per nascondere queste informazioni, è possibile dichiarare o modificare i parametri del modello per questi valori usando i `securestring` tipi `secureobject` di parametro o. È quindi possibile archiviare tali informazioni in [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md). Per passare questi valori ai parametri del modello, si fa riferimento all'insieme di credenziali delle chiavi nel [file dei parametri](#template-parameter-files) usato dal modello in fase di distribuzione.

Di seguito è riportato un esempio che fornisce il nome dell'account e la chiave di accesso per una connessione all'archivio BLOB di Azure:

**File dei parametri**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Modello**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Autenticare le connessioni

Dopo la distribuzione, l'app per la logica funziona end-to-end con parametri validi. Tuttavia, è comunque necessario autorizzare le connessioni OAuth per generare token di accesso validi per [autenticare le credenziali](../active-directory/develop/authentication-scenarios.md). Per altre informazioni, vedere [autorizzare le connessioni OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Alcune connessioni supportano l'uso di un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure ad) per autorizzare le connessioni per un'app per la logica [registrata in Azure ad](../active-directory/develop/quickstart-register-app.md). Ad esempio, questo Azure Data Lake definizione della risorsa di connessione Mostra come fare riferimento ai parametri del modello che gestiscono le informazioni dell'entità servizio e in che modo il modello dichiara questi parametri:

**Definizione della risorsa di connessione**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Attributo | Descrizione |
|-----------|-------------|
| `token:clientId` | L'applicazione o l'ID client associato all'entità servizio |
| `token:clientSecret` | Valore della chiave associato all'entità servizio |
| `token:TenantId` | ID directory per il tenant di Azure AD |
| `token:grantType` | Tipo di concessione richiesto, che deve essere `client_credentials`. Per altre informazioni, vedere [piattaforma Microsoft Identity e il flusso di credenziali client OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definizioni dei parametri di modello**

L'oggetto di primo livello `parameters` del modello dichiara questi parametri per la connessione di esempio:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Per ulteriori informazioni sull'utilizzo delle entità servizio, vedere gli argomenti seguenti:

* [Create a service principal by using the Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) (Creare un'entità servizio usando il portale di Azure)
* [Creare un'entità servizio di Azure usando Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Creare un'entità servizio con un certificato usando Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Riferimenti a parametri

Per fare riferimento ai parametri di modello, è possibile usare espressioni di modello con [funzioni modello](../azure-resource-manager/resource-group-template-functions.md), che vengono valutate in fase di distribuzione. Le espressioni modello utilizzano le parentesi quadre ( **[]** ):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Per fare riferimento ai parametri della definizione del flusso di lavoro, è possibile utilizzare [espressioni e funzioni del linguaggio di definizione del flusso di lavoro](../logic-apps/workflow-definition-language-functions-reference.md), che vengono valutate È possibile notare che alcune funzioni modello e funzioni di definizione del flusso di lavoro hanno lo stesso nome. Le espressioni di definizione del flusso di lavoro iniziano con il **@** simbolo "chiocciola" ():

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

È possibile passare i valori dei parametri di modello alla definizione del flusso di lavoro per l'app per la logica da usare in fase di esecuzione. Tuttavia, evitare di usare parametri di modello, espressioni e sintassi nella definizione del flusso di lavoro perché la finestra di progettazione dell'app per la logica non supporta gli elementi di modello. Inoltre, la sintassi e le espressioni del modello possono complicare il codice a causa delle differenze nel momento in cui vengono valutate le espressioni.

Attenersi invece alla procedura generale per dichiarare e fare riferimento ai parametri di definizione del flusso di lavoro da usare in fase di esecuzione, dichiarare e fare riferimento ai parametri del modello da usare in fase di distribuzione e specificare i valori da passare alla distribuzione usando un file di parametri. Per informazioni dettagliate, vedere la sezione relativa alla [definizione e ai parametri del flusso di lavoro](#workflow-definition-parameters) più indietro in questo argomento.

1. Creare il modello e dichiarare i parametri del modello per i valori da accettare e usare durante la distribuzione.

1. Nella definizione del flusso di lavoro dichiarare i parametri per i valori da accettare e usare in fase di esecuzione. È quindi possibile fare riferimento a questi valori in tutta la definizione del flusso di lavoro.

1. Nell'oggetto che si trova al di *fuori* della definizione del flusso di lavoro ma ancora *all'interno* della definizione di risorsa dell'app per la logica, impostare i valori per i parametri di definizione del flusso di lavoro facendo riferimento ai parametri di modello corrispondenti. `parameters` In questo modo, è possibile passare i valori dei parametri di modello nei parametri della definizione del flusso di lavoro.

1. Nel file dei parametri specificare i valori per il modello da usare durante la distribuzione.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Modello di esempio completo

Ecco il modello di esempio con parametri usato negli esempi di questo argomento:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare modelli di app per la logica](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)