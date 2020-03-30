---
title: Panoramica - Automatizzare la distribuzione per le app per la logica di AzureOverview - Automate deployment for Azure Logic Apps
description: Informazioni sui modelli di Azure Resource Manager per automatizzare la distribuzione per le app per la logica di AzureLearn about Azure Resource Manager templates to automate deployment for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 920d8bfbcef33464d528306113abe6223d752889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477749"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Panoramica: automatizzare la distribuzione per le app per la logica di Azure usando i modelli di Azure Resource ManagerOverview: Automate deployment for Azure Logic Apps by using Azure Resource Manager templates

Quando si è pronti ad automatizzare la creazione e la distribuzione dell'app per la logica, è possibile espandere la definizione del flusso di lavoro sottostante dell'app per la logica in un modello di [Azure Resource Manager.](../azure-resource-manager/management/overview.md) Questo modello definisce l'infrastruttura, le risorse, i parametri e altre informazioni per il provisioning e la distribuzione dell'app per la logica. Definendo i parametri per i valori che variano al momento della distribuzione, noti anche come *parametrizzazione*, è possibile distribuire ripetutamente e in modo coerente app per la logica in base a diverse esigenze di distribuzione.

Ad esempio, se si esegue la distribuzione in ambienti per lo sviluppo, il test e la produzione, è probabile che si utilizzino stringhe di connessione diverse per ogni ambiente. È possibile dichiarare parametri di modello che accettano stringhe di connessione diverse e quindi archiviare tali stringhe in un file di [parametri](../azure-resource-manager/templates/parameter-files.md)separato. In questo modo, è possibile modificare tali valori senza dover aggiornare e ridistribuire il modello. Per gli scenari in cui sono presenti valori di parametro sensibili o protetti, ad esempio password e segreti, è possibile archiviare tali valori in [Archivio](../azure-resource-manager/templates/key-vault-parameter.md) chiavi di Azure e fare in modo che il file dei parametri recuperi tali valori. Tuttavia, in questi scenari, è necessario ridistribuire per recuperare i valori correnti.

Questa panoramica descrive gli attributi in un modello di Resource Manager che include una definizione del flusso di lavoro dell'app per la logica. Sia il modello che la definizione del flusso di lavoro usano la sintassi JSON, ma esistono alcune differenze perché la definizione del flusso di lavoro segue anche lo schema del linguaggio di [definizione del flusso di lavoro.](../logic-apps/logic-apps-workflow-definition-language.md) Ad esempio, le espressioni di modello e le espressioni di definizione del flusso di lavoro differiscono nel modo in cui [fanno riferimento](#parameter-references) ai parametri e nei valori che possono accettare.

> [!TIP]
> For the easiest way to get a valid parameterized logic app template that's mostly ready for deployment, use Visual Studio (free Community edition or greater) and the Azure Logic Apps Tools for Visual Studio. È quindi possibile [creare l'app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) oppure trovare e scaricare [un'app per la logica esistente da Azure in Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> In alternativa, è possibile creare modelli di app per la logica usando [Azure PowerShell con il modulo LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

L'app per la logica di esempio in questo argomento usa un trigger di Office 365 Outlook che viene generato all'arrivo di un nuovo messaggio di posta elettronica e [un'azione](/connectors/azureblob/) di Archiviazione BLOB di Azure che crea un BLOB per il corpo del messaggio di posta elettronica e carica il BLOB in un contenitore di archiviazione di Azure.The example Logic app in this topic uses an [Office 365 Outlook trigger](/connectors/office365/) that fires when a new email arrives and an Azure Blob Storage action that creates a blob for the email body and uploads that blob to an Azure storage container. Negli esempi viene inoltre illustrato come parametrizzare i valori che variano al momento della distribuzione.

Per altre informazioni sui modelli di Resource Manager, vedere gli argomenti seguenti:For more information about Resource Manager templates, see these topics:

* [Struttura e sintassi dei modelli di Azure Resource ManagerAzure Resource Manager template structure and syntax](../azure-resource-manager/templates/template-syntax.md)
* [Procedure consigliate per i modelli di Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [I modelli di Azure Resource Manager possono essere sviluppati per la coerenza cloud](../azure-resource-manager/templates/templates-cloud-consistency.md)

Per i modelli di app per la logica di esempio, vedere gli esempi seguenti:For sample logic app templates, see these examples:

* [Modello completo](#full-example-template) utilizzato per gli esempi di questo argomento
* [Esempio di modello di app per la logica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) di avvio veloce in GitHubSample quickstart logic app template in GitHub

Per informazioni sulle risorse modello specifiche per le app per la logica, gli account di integrazione e gli elementi degli account di integrazione, vedere [Tipi di risorse Microsoft.Logic .](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)

<a name="template-structure"></a>

## <a name="template-structure"></a>Struttura del modello

Al livello superiore, un modello di Resource Manager segue questa struttura, descritta in modo completo nell'argomento relativo alla struttura e alla [sintassi](../azure-resource-manager/templates/template-syntax.md) di Azure Resource Manager:At the top level, a Resource Manager template follows this structure, which is fully described in the Azure Resource Manager template structure and syntax topic:

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

Per un modello di app per la logica, si lavora principalmente con questi oggetti modello:For a logic app template, you primarily work with these template objects:

| Attributo | Descrizione |
|-----------|-------------|
| `parameters` | Dichiara i parametri di modello per accettare i valori da usare durante la creazione e la personalizzazione delle risorse per la distribuzione in Azure.Declares the [template parameters](../azure-resource-manager/templates/template-syntax.md#parameters) for accepting the values to use when creating and customizing resources for deployment in Azure. Ad esempio, questi parametri accettano i valori per il nome e il percorso dell'app per la logica, le connessioni e altre risorse necessarie per la distribuzione. È possibile archiviare questi valori di parametro in un file di [parametri,](#template-parameter-files)descritto più avanti in questo argomento. Per informazioni generali, vedere [Parametri - Struttura e sintassi](../azure-resource-manager/templates/template-syntax.md#parameters)del modello di Resource Manager . |
| `resources` | Definisce le [risorse](../azure-resource-manager/templates/template-syntax.md#resources) da creare o aggiornare e distribuire in un gruppo di risorse di Azure, ad esempio l'app per la logica, le connessioni, gli account di archiviazione di Azure e così via. Per informazioni generali, vedere [Risorse - Struttura e sintassi](../azure-resource-manager/templates/template-syntax.md#resources)dei modelli di Resource Manager. |
||||

Il modello di app per la logica usa questo formato di nome file:Your logic app template uses this file name format:

**<*.nome-app>-logica***

> [!IMPORTANT]
> Sintassi del modello fa distinzione tra maiuscole e minuscole in modo da assicurarsi di utilizzare maiuscole/minuscole coerenti. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parametri del modello

Un modello di `parameters` app per la logica ha più oggetti che esistono a livelli diversi ed eseguono funzioni diverse. Ad esempio, al livello principale, è possibile dichiarare i parametri di modello per i valori da accettare e usare durante la distribuzione durante la creazione e la distribuzione di risorse in Azure, ad esempio:For example, at the top level, you can declare [template parameters](../azure-resource-manager/templates/template-syntax.md#parameters) for the values to accept and use at deployment when creating and deploying resources in Azure, for example:

* L'app per la logica
* Connessioni utilizzate dalla logica per accedere ad altri servizi e sistemi tramite [connettori gestiti](../connectors/apis-list.md)
* Altre risorse necessarie per la distribuzione dell'app per la logica

  Ad esempio, se l'app per la logica usa un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per gli `parameters` scenari business-to-business (B2B), l'oggetto di primo livello del modello dichiara il parametro che accetta l'ID risorsa per l'account di integrazione.

Di seguito è riportata la struttura generale e la sintassi per una definizione di parametro, descritta in modo completo da [Parametri - Struttura e sintassi](../azure-resource-manager/templates/template-syntax.md#parameters)del modello di Resource Manager:

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

Questo esempio mostra solo i parametri di modello per i valori usati per creare e distribuire queste risorse in Azure:This example shows just the template parameters for the values used to create and deploy these resources in Azure:

* Nome e posizione dell'app per la logica
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
         "minLength": 1,
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

Ad eccezione dei parametri che gestiscono valori sensibili o che devono essere protetti, `defaultValue` ad esempio nomi utente, password e segreti, tutti questi parametri includono attributi, anche se in alcuni casi i valori predefiniti sono valori vuoti. I valori di distribuzione da utilizzare per questi parametri di modello vengono forniti dal file di [parametri](#template-parameter-files) di esempio descritto più avanti in questo argomento.

Per altre informazioni sulla protezione dei parametri di modello, vedere gli argomenti seguenti:For more information about securing template parameters, see these topics:

* [Consigli sulla sicurezza per i parametri di modelloSecurity recommendations for template parameters](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Migliorare la sicurezza per i parametri del modello](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Passare valori di parametro protetti con l'insieme di credenziali delle chiavi di AzurePass secured parameter values with Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Altri oggetti modello spesso fanno riferimento ai parametri di modello in modo che possano usare i valori che passano attraverso i parametri di modello, ad esempio:Other template objects often reference template parameters so that they can use the values that pass through template parameters, for example:

* [L'oggetto risorse del modello](#template-resources), descritto più avanti in questo argomento, definisce ogni risorsa in Azure che si vuole creare e distribuire, ad esempio la [definizione di risorsa dell'app per la logica.](#logic-app-resource-definition) Queste risorse usano spesso i valori dei parametri di modello, ad esempio il nome e la posizione dell'app per la logica e le informazioni di connessione.

* A un livello più profondo nella definizione di risorsa dell'app per la logica, [l'oggetto parameters della definizione del flusso](#workflow-definition-parameters) di lavoro dichiara i parametri per i valori da usare nel runtime dell'app per la logica. Ad esempio, è possibile dichiarare i parametri di definizione del flusso di lavoro per il nome utente e la password utilizzati da un trigger HTTP per l'autenticazione. Per specificare i valori per `parameters` i parametri di definizione del flusso di lavoro, usare l'oggetto esterno *alla* definizione del flusso di lavoro ma *comunque all'interno* della definizione di risorsa dell'app per la logica. In questo `parameters` oggetto esterno è possibile fare riferimento a parametri di modello dichiarati in precedenza, che possono accettare valori durante la distribuzione da un file di parametri.

Quando si fa riferimento a parametri, espressioni di modello e funzioni utilizzano una sintassi diversa e si comportano in modo diverso dalle espressioni e dalle funzioni di definizione del flusso di lavoro. Per altre informazioni su queste differenze, vedere [Riferimenti ai parametri](#parameter-references) più avanti in questo argomento.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Procedure consigliate - parametri di modelloBest practices - template parameters

Di seguito sono riportate alcune procedure consigliate per la definizione dei parametri:

* Dichiarare i parametri solo per i valori che variano in base alle esigenze di distribuzione. Non dichiarare parametri per valori che rimangono invariati in base ai diversi requisiti di distribuzione.

* Includere `defaultValue` l'attributo, che può specificare valori vuoti, per tutti i parametri ad eccezione dei valori sensibili o che devono essere protetti. Utilizzare sempre parametri protetti per nomi utente, password e segreti. Per nascondere o proteggere i valori dei parametri sensibili, seguire le indicazioni contenute negli argomenti seguenti:To hide or protect sensitive parameter values, follow the guidance in these topics:

  * [Consigli sulla sicurezza per i parametri di modelloSecurity recommendations for template parameters](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Migliorare la sicurezza per i parametri del modello](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Passare valori di parametro protetti con l'insieme di credenziali delle chiavi di AzurePass secured parameter values with Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Per differenziare i nomi dei parametri di modello dai nomi dei parametri di definizione del flusso di lavoro, è possibile utilizzare nomi descrittivi dei parametri di modello, ad esempio:To differentiate template parameter names from workflow definition parameter names, you can use descriptive template parameter names, for example:`TemplateFabrikamPassword`

Per altre procedure consigliate per i modelli, vedere [Procedure consigliate per i parametri](../azure-resource-manager/templates/template-best-practices.md#parameters)di modello .

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>File di parametri del modello

Per fornire i valori per i parametri di modello, archiviare tali valori in un file di [parametri](../azure-resource-manager/templates/parameter-files.md). In questo modo, è possibile utilizzare file di parametri diversi in base alle esigenze di distribuzione. Di seguito è riportato il formato del nome file da utilizzare:

* Nome del file modello di app per la logica: ** < *logic-app-name*>.json**
* Nome file parametri: ** < *logic-app-name*>.parameters.json**

Di seguito è riportata la struttura all'interno del file dei parametri, che include un riferimento all'insieme di credenziali delle chiavi per il passaggio di un valore di parametro protetto con L'insieme di credenziali delle chiavi di Azure:Here is the structure inside the parameters file, which includes a key vault reference for [passing a secured parameter value with Azure Key Vault:](../azure-resource-manager/templates/key-vault-parameter.md)

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

Questo file di parametri di esempio specifica i valori per i parametri di modello dichiarati in precedenza in questo argomento:This example parameters file specifies the values for the template parameters declared earlier in this topic:

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

Il modello `resources` include un oggetto, ovvero una matrice che contiene le definizioni per ogni risorsa da creare e distribuire in Azure, ad esempio la definizione di [risorsa dell'app per la logica,](#logic-app-resource-definition)tutte [le definizioni](#connection-resource-definitions)delle risorse di connessione e tutte le altre risorse necessarie per la distribuzione dell'app per la logica.

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
> I modelli possono includere definizioni di risorse per più app per la logica, quindi assicurarsi che tutte le risorse dell'app per la logica specifichino lo stesso gruppo di risorse di Azure.Templates can include resource definitions for multiple logic apps, so make sure that all your logic app resources specify the same Azure resource group. Quando si distribuisce il modello in un gruppo di risorse di Azure usando Visual Studio, viene richiesto quale app per la logica si vuole aprire. Inoltre, il progetto del gruppo di risorse di Azure può contenere più di un modello, pertanto assicurarsi di selezionare il file di parametri corretto quando richiesto.

Per informazioni generali sulle risorse modello e sui relativi attributi, vedere gli argomenti seguenti:For general information about template resources and their attributes, see these topics:

* [Risorse - Struttura e sintassi dei modelli di Resource Manager](../azure-resource-manager/templates/template-syntax.md#resources)
* [Procedure consigliate per le risorse modelloBest practices for template resources](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definizione di risorsa dell'app per la logica

La definizione di risorsa dell'app per la logica inizia con l'oggetto, `properties` che include queste informazioni:Your logic app's resource definition starts with the object, which includes this information:

* Stato dell'app per la logica al momento della distribuzione
* ID per qualsiasi account di integrazione usato dall'app per la logica
* Definizione del flusso di lavoro dell'app per la logica
* Oggetto `parameters` che imposta i valori da utilizzare in fase di esecuzione
* Altre informazioni sulle risorse relative all'app per la logica, ad esempio nome, tipo, posizione e così via

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

Ecco gli attributi specifici della definizione di risorsa dell'app per la logica:Here are the attributes that are specific to your logic app resource definition:

| Attributo | Obbligatoria | Type | Descrizione |
|-----------|----------|------|-------------|
| `state` | Sì | string | Lo stato dell'app per `Enabled` la logica in `Disabled` fase di distribuzione, dove significa che l'app per la logica è attiva e indica che l'app per la logica è inattiva. Ad esempio, se non sei pronto per la trasmissione dell'app per la `Disabled` logica ma vuoi distribuire una versione bozza, puoi usare l'opzione. |
| `integrationAccount` | No | Oggetto | Se l'app per la logica usa un account di integrazione, che archivia `id` gli elementi per gli scenari business-to-business (B2B), questo oggetto include l'attributo, che specifica l'ID per l'account di integrazione. |
| `definition` | Sì | Oggetto | Definizione del flusso di lavoro sottostante dell'app per la logica, che è lo stesso oggetto visualizzato nella visualizzazione codice ed è descritto in modo completo nell'argomento Riferimento allo schema per il linguaggio di [definizione del flusso di lavoro.](../logic-apps/logic-apps-workflow-definition-language.md) In questa definizione `parameters` del flusso di lavoro, l'oggetto dichiara i parametri per i valori da usare in fase di esecuzione dell'app per la logica. Per ulteriori informazioni, vedere [Definizione e parametri del flusso di lavoro](#workflow-definition-parameters). <p><p>Per visualizzare gli attributi nella definizione del flusso di lavoro dell'app per la logica, passare da "visualizzazione progettazione" a "visualizzazione codice" nel portale di Azure o in Visual Studio oppure usare uno strumento come [Azure Resource Explorer.](https://resources.azure.com) |
| `parameters` | No | Oggetto | Valori dei parametri di [definizione del flusso di lavoro](#workflow-definition-parameters) da usare in fase di esecuzione dell'app per la logica. Le definizioni dei parametri per questi valori vengono visualizzate all'interno [dell'oggetto parameters della definizione del flusso di lavoro.](#workflow-definition-parameters) Inoltre, se l'app per la logica usa [connettori gestiti](../connectors/apis-list.md) per l'accesso ad altri servizi e sistemi, questo oggetto include un `$connections` oggetto che imposta i valori di connessione da usare in fase di esecuzione. |
| `accessControl` | No | Oggetto | Per specificare gli attributi di sicurezza per l'app per la logica, ad esempio per limitare l'accesso IP ai trigger di richiesta o agli input e agli output della cronologia di esecuzione. Per ulteriori informazioni, consultate [Proteggere l'accesso alle app per la logica.](../logic-apps/logic-apps-securing-a-logic-app.md) |
||||

Per informazioni sulle risorse modello specifiche per le app per la logica, gli account di integrazione e gli elementi degli account di integrazione, vedere [Tipi di risorse Microsoft.Logic .](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definizione e parametri del flusso di lavoro

La definizione del flusso di `definition` lavoro dell'app `properties` per la logica viene visualizzata nell'oggetto, che viene visualizzato nell'oggetto all'interno della definizione di risorsa dell'app per la logica. Questo `definition` oggetto è lo stesso oggetto che viene visualizzato nella visualizzazione codice ed è descritto in modo completo nell'argomento [Riferimento allo schema per il linguaggio](../logic-apps/logic-apps-workflow-definition-language.md) di definizione del flusso di lavoro. La definizione del `parameters` flusso di lavoro include un oggetto dichiarazione interna in cui è possibile definire nuovi parametri o modificare quelli esistenti per i valori utilizzati dalla definizione del flusso di lavoro in fase di esecuzione. È quindi possibile fare riferimento a questi parametri all'interno del trigger o delle azioni nel flusso di lavoro. Per impostazione `parameters` predefinita, questo oggetto è vuoto a meno che l'app per la logica non crei connessioni ad altri servizi e sistemi tramite [connettori gestiti.](../connectors/apis-list.md)

Per impostare i valori per `parameters` i parametri di definizione del flusso di lavoro, usare l'oggetto esterno *alla* definizione del flusso di lavoro ma *comunque all'interno* della definizione di risorsa dell'app per la logica. In questo `parameters` oggetto esterno è quindi possibile fare riferimento ai parametri di modello dichiarati in precedenza, che possono accettare valori durante la distribuzione da un file di parametri.

> [!TIP]
>
> Come procedura consigliata, non fare riferimento direttamente ai parametri del modello, che vengono valutati in fase di distribuzione, dall'interno della definizione del flusso di lavoro. Dichiarare invece un parametro di definizione `parameters` del flusso di lavoro, che è quindi possibile impostare nell'oggetto esterno *alla* definizione del flusso di lavoro, ma comunque *all'interno* della definizione di risorsa dell'app per la logica. Per ulteriori informazioni, vedere [Riferimenti ai parametri](#parameter-references).

Questa sintassi mostra dove è possibile dichiarare i parametri sia a livello di modello che a livello di definizione del flusso di lavoro insieme alla posizione in cui è possibile impostare tali valori facendo riferimento ai parametri di definizione del modello e del flusso di lavoro:

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

### <a name="secure-workflow-definition-parameters"></a>Proteggere i parametri di definizione del flusso di lavoroSecure workflow definition parameters

Per un parametro di definizione del flusso di lavoro che gestisce informazioni riservate, password, chiavi di accesso o segreti in fase di esecuzione, dichiarare o modificare il parametro per utilizzare il `securestring` tipo di parametro o `secureobject` . È possibile fare riferimento a questo parametro in tutta e all'interno della definizione del flusso di lavoro. Al livello principale del modello dichiarare un parametro con lo stesso tipo per gestire queste informazioni al momento della distribuzione.

Per impostare il valore per il `parameters` parametro di definizione del flusso di lavoro, usare l'oggetto esterno *alla* definizione del flusso di lavoro, ma *comunque all'interno* della definizione di risorsa dell'app per la logica per fare riferimento al parametro di modello. Infine, per passare il valore al parametro di modello al momento della distribuzione, archiviare tale valore nell'insieme di credenziali delle chiavi di [Azure](../azure-resource-manager/templates/key-vault-parameter.md) e fare riferimento all'insieme di credenziali delle chiavi nel file dei [parametri](#template-parameter-files) usato dal modello al momento della distribuzione.

Questo modello di esempio mostra come completare queste attività definendo i parametri protetti quando necessario in modo da poter archiviare i valori in Archiviazione chiave di Azure:This example template shows how you can complete these tasks by defining secured parameters when necessary so that you can store their values in Azure Key Vault:

* Dichiarare i parametri protetti per i valori utilizzati per autenticare l'accesso.
* Utilizzare questi valori a livello di definizione del modello e del flusso di lavoro.
* Fornire questi valori utilizzando un file di parametri.

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

## <a name="best-practices---workflow-definition-parameters"></a>Procedure consigliate - parametri di definizione del flusso di lavoro

Per assicurarsi che Progettazione app per la logica possa visualizzare correttamente i parametri di definizione del flusso di lavoro, attenersi alle procedure consigliate seguenti:To make sure that the Logic App Designer can correctly show workflow definition parameters, follow these best practices:

* Includere `defaultValue` l'attributo, che può specificare valori vuoti, per tutti i parametri ad eccezione dei valori sensibili o che devono essere protetti.

* Utilizzare sempre parametri protetti per nomi utente, password e segreti. Per nascondere o proteggere i valori dei parametri sensibili, seguire le indicazioni contenute negli argomenti seguenti:To hide or protect sensitive parameter values, follow the guidance in these topics:

  * [Raccomandazioni sulla sicurezza per i parametri di azione](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Consigli sulla sicurezza per i parametri nelle definizioni del flusso di lavoroSecurity recommendations for parameters in workflow definitions](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Passare valori di parametro sicuri con l'insieme di credenziali delle chiavi di AzurePass secure parameter values with Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Per ulteriori informazioni sui parametri di definizione del flusso di lavoro, vedere [Parametri - Linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definizioni delle risorse di connessioneConnection resource definitions

Quando l'app per la logica crea e usa connessioni ad altri `resources` servizi e sistemi usando [connettori gestiti,](../connectors/apis-list.md)l'oggetto del modello contiene le definizioni delle risorse per tali connessioni.

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

Le definizioni delle risorse di connessione fanno riferimento ai parametri di primo livello del modello per i relativi valori, il che significa che è possibile fornire questi valori durante la distribuzione usando un file di parametri. Assicurarsi che le connessioni usino lo stesso gruppo di risorse di Azure e lo stesso percorso dell'app per la logica.

Di seguito è riportata una definizione di risorsa di esempio per una connessione di Office 365 Outlook e i parametri del modello corrispondenti:

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

La definizione di risorsa dell'app per la logica funziona anche con le definizioni delle risorse di connessione nei modi seguenti:Your logic app's resource definition also works with connection resource definitions in these ways:

* All'interno della `parameters` definizione del `$connections` flusso di lavoro, l'oggetto dichiara un parametro per i valori di connessione da usare in fase di esecuzione dell'app per la logica. Inoltre, il trigger o l'azione che crea una `$connections` connessione utilizza i valori corrispondenti che passano attraverso questo parametro.

* *All'esterno* della definizione del flusso di lavoro, ma ancora *all'interno* della definizione di risorsa dell'app per la logica, un altro `parameters` oggetto imposta i valori da usare in fase di esecuzione per il `$connections` parametro facendo riferimento ai parametri di modello corrispondenti. Questi valori usano espressioni modello per fare riferimento a risorse che archiviano in modo sicuro i metadati per le connessioni nell'app per la logica.

  Ad esempio, i metadati possono includere stringhe di connessione e token di accesso, che è possibile archiviare in Archiviazione delle chiavi di Azure .For example, metadata can include connection strings and access tokens, which you can store in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Per passare tali valori ai parametri del modello, fare riferimento a tale insieme di credenziali di chiave nel file dei [parametri](#template-parameter-files) utilizzato dal modello al momento della distribuzione. Per ulteriori informazioni sulle differenze nel riferimento ai parametri, vedere [Riferimenti ai parametri](#parameter-references) più avanti in questo argomento.

  Quando si apre la definizione del flusso di lavoro dell'app `$connections` per la logica nella visualizzazione codice tramite il portale di Azure o Visual Studio, l'oggetto viene visualizzato all'esterno della definizione del flusso di lavoro ma allo stesso livello. Questo ordinamento nella visualizzazione codice semplifica il riferimento a questi parametri quando si aggiorna manualmente la definizione del flusso di lavoro:This ordering in code view makes these parameters easier to reference when you manually update the workflow definition:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* La definizione di risorsa `dependsOn` dell'app per la logica ha un oggetto che specifica le dipendenze dalle connessioni usate dall'app per la logica.

Ogni connessione creata ha un nome univoco in Azure.Each connection that you create has a unique name in Azure. Quando si creano più connessioni allo stesso servizio o sistema, a ogni nome di connessione viene aggiunto `office365` `office365-1`un numero, che viene incrementato con ogni nuova connessione creata, ad esempio , e così via.

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

Per un parametro di connessione che gestisce informazioni riservate, password, chiavi `parameterValues` di accesso o segreti, la definizione di risorsa della connessione include un oggetto che specifica questi valori in formato coppia nome-valore. Per nascondere queste informazioni, è possibile dichiarare o modificare `securestring` i `secureobject` parametri di modello per questi valori utilizzando i tipi di parametro o . È quindi possibile archiviare tali informazioni in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Per passare tali valori ai parametri del modello, fare riferimento a tale insieme di credenziali di chiave nel file dei [parametri](#template-parameter-files) utilizzato dal modello al momento della distribuzione.

Ecco un esempio che fornisce il nome dell'account e la chiave di accesso per una connessione di Archiviazione BLOB di Azure:Here is an example that provides the account name and access key for an Azure Blob Storage connection:

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

Dopo la distribuzione, l'app per la logica funziona end-to-end con parametri validi. Tuttavia, è comunque necessario autorizzare tutte le connessioni OAuth per generare token di accesso validi per [l'autenticazione delle credenziali.](../active-directory/develop/authentication-scenarios.md) Per ulteriori informazioni, consultate [Autorizzare le connessioni OAuth.](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)

Alcune connessioni supportano l'uso di [un'entità servizio](../active-directory/develop/app-objects-and-service-principals.md) di Azure Active Directory (Azure AD) per autorizzare le connessioni per un'app per la logica registrata in Azure [AD.](../active-directory/develop/quickstart-register-app.md) Ad esempio, questa definizione di risorsa di connessione di Azure Data Lake mostra come fare riferimento ai parametri di modello che gestiscono le informazioni dell'entità servizio e come il modello dichiara questi parametri:For example, this Azure Data Lake connection resource definition shows how to reference the template parameters that handle the service principal's information and how the template declares these parameters:

**Definizione delle risorse di connessione**

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
| `token:clientId` | L'id applicazione o client associato all'entità servizio |
| `token:clientSecret` | Valore della chiave associato all'entità servizio |
| `token:TenantId` | ID di directory per il tenant di Azure AD |
| `token:grantType` | Il tipo di sovvenzione `client_credentials`richiesto, che deve essere . Per ulteriori informazioni, vedere Piattaforma di identità Microsoft e flusso di [credenziali client OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definizioni dei parametri di modello**

L'oggetto di primo `parameters` livello del modello dichiara questi parametri per la connessione di esempio:The template's top-level object declares these parameters for the example connection:

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

Per altre informazioni sull'uso delle entità servizio, vedere gli argomenti seguenti:For more information about working with service principals, see these topics:

* [Create a service principal by using the Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) (Creare un'entità servizio usando il portale di Azure)
* [Creare un'entità servizio di Azure usando Azure PowerShellCreate an Azure service principal by using Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Creare un'entità servizio con un certificato tramite Azure PowerShellCreate a service principal with a certificate by using Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Riferimenti ai parametri

Per fare riferimento ai parametri del modello, è possibile utilizzare espressioni di modello con funzioni di [modello](../azure-resource-manager/templates/template-functions.md), che vengono valutate al momento della distribuzione. Le espressioni modello utilizzano parentesi quadre (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Per fare riferimento ai parametri di definizione del flusso di lavoro, utilizzare le espressioni e le funzioni del linguaggio di [definizione del flusso di](../logic-apps/workflow-definition-language-functions-reference.md)lavoro, che vengono valutate in fase di esecuzione. È possibile notare che alcune funzioni di modello e le funzioni di definizione del flusso di lavoro hanno lo stesso nome. Le espressioni di definizione del**@** flusso di lavoro iniziano con il simbolo "at" ( ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

È possibile passare i valori dei parametri del modello alla definizione del flusso di lavoro per l'app per la logica da usare in fase di esecuzione. Tuttavia, evitare di usare parametri di modello, espressioni e sintassi nella definizione del flusso di lavoro perché Progettazione app per la logica non supporta gli elementi del modello. Inoltre, la sintassi e le espressioni del modello possono complicare il codice a causa delle differenze in quando vengono valutate le espressioni.

Seguire invece questi passaggi generali per dichiarare e fare riferimento ai parametri di definizione del flusso di lavoro da utilizzare in fase di esecuzione, dichiarare e fare riferimento ai parametri di modello da utilizzare in fase di distribuzione e specificare i valori da passare in fase di distribuzione tramite un file di parametri. Per i dettagli completi, vedere la sezione [Definizione e parametri del flusso di lavoro](#workflow-definition-parameters) più indietro in questo argomento.

1. Creare il modello e dichiarare i parametri del modello per i valori da accettare e utilizzare durante la distribuzione.

1. Nella definizione del flusso di lavoro dichiarare i parametri per i valori da accettare e utilizzare in fase di esecuzione. È quindi possibile fare riferimento a questi valori in tutta e all'interno della definizione del flusso di lavoro.

1. Nell'oggetto `parameters` che *si trova all'esterno* della definizione del flusso di lavoro ma ancora *all'interno* della definizione di risorsa dell'app per la logica, impostare i valori per i parametri di definizione del flusso di lavoro facendo riferimento ai parametri del modello corrispondenti. In questo modo, è possibile passare i valori dei parametri di modello nei parametri di definizione del flusso di lavoro.

1. Nel file dei parametri specificare i valori per il modello da utilizzare durante la distribuzione.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Modello di esempio completo

Di seguito è riportato il modello di esempio con parametri usato dagli esempi di questo argomento:Here is the parameterized sample template that's used by this topic's examples:

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
         "minLength": 1,
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
