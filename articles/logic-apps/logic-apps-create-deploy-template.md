---
title: Creare modelli di distribuzione per le app per la logica di Azure | Microsoft Docs
description: Creare modelli di Azure Resource Manager per la distribuzione di app per la logica
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128601"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Creare modelli di Azure Resource Manager per la distribuzione di app per la logica

Quando si crea un'app per la logica, è possibile espandere la definizione dell'app per la logica in un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). È quindi possibile usare questo modello per l'automazione delle distribuzioni definendo le risorse e i parametri che si desidera utilizzare per la distribuzione e fornire i valori dei parametri tramite un [file dei parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
In questo modo, è possibile distribuire App per la logica più facilmente e in qualsiasi ambiente o un gruppo di risorse di Azure desiderato. 

App per la logica di Azure fornisce una [modello di Azure Resource Manager di App per la logica predefinite](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) che è possibile riutilizzare, non solo per la creazione di App per la logica, ma anche per definire le risorse e i parametri da usare per la distribuzione. È possibile usare questo modello per i propri scenari aziendali o personalizzarlo in base alle esigenze. Altre informazioni sulle [struttura modello di Resource Manager e la sintassi](../azure-resource-manager/resource-group-authoring-templates.md). Per la sintassi e le proprietà JSON, vedere [Microsoft.Logic resource types](/azure/templates/microsoft.logic/allversions) (Tipi di risorsa Microsoft.Logic).

Per altre informazioni sui modelli di Azure Resource Manager, vedere questi articoli:

* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Sviluppare modelli di Azure Resource Manager per la coerenza di cloud](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Struttura dell'app per la logica

La definizione dell'app per la logica dispone di queste sezioni di base, che è possibile visualizzare passando da "visualizzazione di progettazione" per "visualizzazione codice" o con uno strumento, ad esempio [Azure Resource Explorer](http://resources.azure.com). Definizioni di app per la logica usare Javascript Object Notation (JSON), pertanto per altre informazioni sulla sintassi JSON e le proprietà, vedere [tipi di risorse Logic](/azure/templates/microsoft.logic/allversions).

* **Risorsa dell'app per la logica**: Descrive le informazioni come la posizione dell'app per la logica o area, piano tariffario e definizione del flusso di lavoro.

* **Definizione del flusso di lavoro**: Viene descritto dell'app per la logica di trigger e azioni ed esecuzione del flusso di lavoro del servizio App per la logica di Azure. Nella visualizzazione codice, è possibile trovare la definizione del flusso di lavoro nel `definition` sezione.

* **Connessioni**: Se si usano connettori gestiti nell'app per la logica di `$connections` sezione fa riferimento ad altre risorse che archiviano in modo sicuro i metadati relativi a queste connessioni tra l'app per la logica e altri sistemi o servizi, ad esempio le stringhe di connessione e i token di accesso. All'interno della definizione di app per la logica, riferimenti a queste connessioni vengono visualizzati all'interno della definizione di app per la logica `parameters` sezione.

Per creare un modello di app per la logica che è possibile usare con distribuzioni di gruppi di risorse di Azure, è necessario definire le risorse e impostare i parametri in base alle esigenze. Ad esempio, se si esegue la distribuzione in un ambiente di sviluppo, un ambiente di test e un ambiente di produzione, è probabile che in ogni si vogliano usare stringhe di connessione a un database SQL diverse.
In alternativa, è possibile che si voglia eseguire la distribuzione in sottoscrizioni o gruppi di risorse diversi.

## <a name="create-logic-app-deployment-templates"></a>Creare modelli di distribuzione di app per la logica

Per il modo più semplice creare un modello di distribuzione di app per la logica valida, usare Visual Studio e gli strumenti di App per la logica di Azure per l'estensione di Visual Studio. Scaricare l'app per la logica dal portale di Azure in Visual Studio, per ottenere un modello di distribuzione valido che è possibile usare con qualsiasi sottoscrizione di Azure e il percorso. Inoltre, automaticamente il download dell'app per la logica Parametrizza la definizione dell'app per la logica che viene incorporata nel modello.
Per altre informazioni sulla creazione e gestione delle App per la logica in Visual Studio, vedere [creare App per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [gestire le App per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Diverso da Visual Studio o la creazione manuale del modello e i parametri necessari seguendo le indicazioni fornite in questo argomento, è anche possibile usare la [modulo di PowerShell per la creazione di modelli di app per la logica](https://github.com/jeffhollan/LogicAppTemplateCreator). Questo modulo open source valuta innanzitutto tutte le connessioni che usa l'app per la logica e app per la logica. Il modulo quindi genera risorse del modello con i parametri necessari per la distribuzione. Ad esempio, si supponga di che avere un'app per la logica che riceve un messaggio da una coda del Bus di servizio di Azure e aggiunge dati a un database SQL di Azure. Lo strumento di modulo consente di mantenere tutta la logica di orchestrazione e la parametrizzazione le stringhe di connessione SQL e Bus di servizio in modo che sia possibile impostare tali valori in fase di distribuzione.

> [!IMPORTANT]
> Le connessioni devono esistere nello stesso gruppo di risorse di Azure come app per la logica.
> Per il modulo di PowerShell lavorare con qualsiasi tenant e la sottoscrizione accesso token da usare in Azure il modulo con il [dello strumento client di Azure Resource Manager](https://github.com/projectkudu/ARMClient). Per altre informazioni, vedere questo [articolo sullo strumento client di Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) illustra ARMClient in modo più dettagliato.

### <a name="install-powershell-module-for-logic-app-templates"></a>Installare il modulo di PowerShell per i modelli di app per la logica

Per il modo più semplice installare il modulo dal [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), usare questo comando:

`Install-Module -Name LogicAppTemplate`

È anche possibile installare manualmente il modulo di PowerShell:

1. Scaricare la versione più recente [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Estrarre la cartella nella cartella del modulo PowerShell, che è in genere `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Generare il modello di app per la logica - PowerShell

Dopo aver installato PowerShell, è possibile generare un modello utilizzando il comando seguente:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` è l'ID sottoscrizione di Azure. Questa riga ottiene innanzitutto un token di accesso tramite ARMClient, lo trasmette allo script di PowerShell e quindi crea il modello in un file JSON.

## <a name="parameters-in-logic-app-templates"></a>Parametri nei modelli di app per la logica

Dopo aver creato il modello di app per la logica, è possibile aggiungere e modificare i parametri necessari. Il modello include più di una `parameters` sezione, ad esempio: 

* Definizione di flusso di lavoro dell'app per la logica dispone di propri [ `parameters` sezione](../logic-apps/logic-apps-workflow-definition-language.md#parameters) che consente di definire tutti i parametri che l'app per la logica Usa per accettare gli input in fase di distribuzione.

* Modello di Resource Manager dispone di propri [ `parameters` sezione](../azure-resource-manager/resource-group-authoring-templates.md#parameters), separato dell'app per la logica `parameters` sezione. Ad esempio: 

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Si supponga, ad esempio, la definizione dell'app per la logica fa riferimento a un ID di risorsa che rappresenta un flusso di lavoro di app per la logica annidata o una funzione di Azure e si desidera distribuire tale ID di risorsa con l'app per la logica come singola distribuzione. È possibile aggiungere tale ID come risorsa nel modello e impostare i parametri per tale ID. È possibile usare questo stesso approccio per i riferimenti a endpoint personalizzati, le API o OpenAPI (in precedenza "Swagger") da distribuire con ogni gruppo di risorse di Azure.

Quando si usano parametri nel modello di distribuzione, seguire queste linee guida per la progettazione di App per la logica può visualizzati correttamente questi parametri:

* Usare parametri solo in questi trigger e azioni:

  * App per le funzioni di Azure
  * Annidati o flusso di lavoro app per la logica figlio
  * Chiamata di gestione API
  * URL di runtime della connessione API
  * Percorso di connessione API

* Quando si definiscono i parametri, assicurarsi di specificare i valori predefiniti utilizzando i `defaultValue` valore della proprietà, ad esempio:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Per proteggere o nascondere informazioni riservate nei modelli, proteggere i parametri. Altre informazioni sulle [come usare i parametri protetti](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Ecco un esempio che illustra come è possibile parametrizzare l'azione "Invia messaggio" del Bus di servizio di Azure:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Fare riferimento alle risorse dipendenti

Se l'app per la logica richiede i riferimenti a risorse dipendenti, è possibile usare [funzioni del modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) nel modello di distribuzione dell'app per la logica. Si supponga, ad esempio, che si vuole che l'app per la logica faccia riferimento a una funzione di Azure o un account di integrazione con le definizioni per i partner, contratti e altri elementi desiderati distribuiti insieme all'app per la logica.
È possibile usare funzioni di modello di Resource Manager, ad esempio `parameters`, `variables`, `resourceId`, `concat`e così via.

Ecco un esempio che illustra come è possibile sostituire l'ID risorsa per una funzione di Azure con la definizione di questi parametri:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Ecco come si usano questi parametri quando si fa riferimento la funzione di Azure:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Aggiungere app per la logica al progetto gruppo di risorse

Se si dispone di un progetto gruppo di risorse di Azure esistente, è possibile aggiungere app per la logica al progetto usando la finestra Struttura JSON. È inoltre possibile aggiungere un'altra app per la logica con l'applicazione creata in precedenza.

1. In Esplora soluzioni aprire il file `<template>.json`.

2. Dal **View** dal menu **Other Windows** > **struttura JSON**.

3. Per aggiungere una risorsa del file di modello, scegliere **Aggiungi risorsa** nella parte superiore della finestra Struttura JSON. Oppure nella finestra Struttura JSON fare clic con il tasto destro del mouse su **risorse**e selezionare **Aggiungi nuova risorsa**.

   ![Finestra Struttura JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Nella finestra di dialogo **Aggiungi risorsa**, individuare e selezionare **App per la logica**. Dare un nome all'app per la logica e scegliere **Aggiungi**.

   ![Aggiungere una risorsa](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Supporto

In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire modelli di app per la logica](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
