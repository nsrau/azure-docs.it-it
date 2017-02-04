---
title: Creare un modello di distribuzione di app per la logica | Documentazione Microsoft
description: Informazioni su come creare un modello di distribuzione di app per la logica e utilizzarlo per la gestione del rilascio
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 4e1de756696bec4d3d0ff443e643d735523b851b


---
# <a name="create-a-logic-app-deployment-template"></a>Creare un modello di distribuzione di app per la logica
Una volta creata un'app per la logica, è possibile crearla come un modello di Azure Resource Manager. Così facendo, l'app per la logica potrà essere facilmente distribuita in qualsiasi ambiente o gruppo di risorse in cui potrebbe essere necessaria. Per un'introduzione ai modelli di distribuzione Resource Manager, vedere gli articoli [Creazione di un modello di distribuzione Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [Distribuzione di risorse usando i modelli Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modello di distribuzione di app per la logica
Un'app per la logica dispone di tre componenti di base:

* **Risorsa dell'app per la logica**. Questa risorsa contiene le informazioni su elementi quali piano tariffario, posizione e definizione del flusso di lavoro.
* **Definizione del flusso di lavoro**. È ciò che compare nella visualizzazione codice. Include la definizione dei passaggi del flusso e le modalità di esecuzione del motore. Corrisponde alla proprietà `definition` della risorsa app per la logica.
* **Connessioni**. Risorse separate per archiviare in modo sicuro i metadati su qualsiasi connessione del connettore, ad esempio una stringa di connessione e un token di accesso. In un'app per la logica si fa riferimento a queste informazioni nella sezione `parameters` della risorsa app per la logica.

È possibile visualizzare tutti questi elementi per le app per la logica esistenti utilizzando uno strumento come [Esplora risorse di Azure](http://resources.azure.com).

Per creare un modello per un'app per la logica da utilizzare con distribuzioni di gruppi di risorse, è necessario definire le risorse e creare i parametri necessari. Ad esempio, se si esegue la distribuzione in un ambiente di sviluppo, un ambiente di test e un ambiente di produzione, è probabile che si vogliano utilizzare stringhe di connessione diverse per un database SQL in ogni ambiente In alternativa, è possibile che si voglia eseguire la distribuzione in sottoscrizioni o gruppi di risorse diversi.  

## <a name="create-a-logic-app-deployment-template"></a>Creare un modello di distribuzione di app per la logica
Il modo più semplice per ottenere un modello di distribuzione di app per la logica consiste nell'usare [Visual Studio Tools per app per la logica](logic-apps-deploy-from-vs.md).  Gli strumenti di Visual Studio generano un modello di distribuzione valido, che può essere usato in qualsiasi sottoscrizione o località.

Esistono altri strumenti utili per la creazione di un modello di distribuzione di app per la logica. È possibile eseguire la creazione manualmente utilizzando le risorse appena descritte per creare i parametri in base alle esigenze. In alternativa è possibile utilizzare un modulo [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) di PowerShell. Questo modulo open source valuta l'app per la logica ed eventuali connessioni che utilizza, quindi genera risorse del modello con i parametri necessari per la distribuzione. Se, ad esempio, è disponibile un'app per la logica che ha ricevuto un messaggio da una coda del bus del servizio Azure e ha aggiunto dati a un database SQL di Azure, lo strumento conserverebbe l'intera logica di orchestrazione e creerebbe parametri per le stringhe di connessione di SQL e del bus di servizio, in modo che sia possibile configurarle in fase di distribuzione.

> [!NOTE]
> Le connessioni devono essere incluse nello stesso gruppo di risorse dell'app per la logica.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Installare il modulo PowerShell per il modello di app per la logica
Il modo più semplice per installare il modulo consiste nell'usare la [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) con il comando `Install-Module -Name LogicAppTemplate`.  

È inoltre possibile installare il modulo PowerShell manualmente:

1. Scaricare la versione più recente di [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Estrarre la cartella nella cartella del modulo PowerShell, in genere `%UserProfile%\Documents\WindowsPowerShell\Modules`.

Per consentire il funzionamento del modulo con qualsiasi tenant e qualsiasi token di accesso della sottoscrizione, è consigliabile utilizzare lo strumento da riga di comando [ARMClient](https://github.com/projectkudu/ARMClient) .  Questo [post di blog ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) illustra ARMClient in modo più dettagliato.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generare un modello di app per la logica tramite PowerShell
Dopo aver installato PowerShell, è possibile generare un modello utilizzando il comando seguente:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` è l'ID sottoscrizione di Azure. Questa riga ottiene innanzitutto un token di accesso tramite ARMClient, lo trasmette allo script di PowerShell e quindi crea il modello in un file JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Aggiungere parametri a un modello di app per la logica
Dopo aver creato il modello di app per la logica, è possibile continuare ad aggiungere o modificare i parametri necessari. Se, ad esempio, la definizione include un ID di risorsa in una funzione di Azure o in un flusso di lavoro annidato in cui si prevede eseguire una singola distribuzione, è possibile aggiungere altre risorse al modello e creare parametri per gli ID in base alla necessità. Lo stesso approccio è applicabile a qualsiasi riferimento ad API personalizzate o endpoint Swagger che si prevede di distribuire in ogni gruppo di risorse.

## <a name="deploy-a-logic-app-template"></a>Distribuire un modello di app per la logica
Per distribuire il modello, è possibile utilizzare alcuni strumenti, inclusi PowerShell, API REST, Visual Studio, Release Management e la distribuzione dei modelli del portale di Azure. Vedere l'articolo sulla [distribuzione di risorse tramite i modelli di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) per ulteriori informazioni. È inoltre consigliabile creare anche un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#parameters) per l'archiviazione di valori per il parametro.

### <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth
Una volta distribuite, le app per la logica funzionano end-to-end con parametri validi. Tuttavia, sarà comunque necessario autorizzare le connessioni OAuth per la generazione di un token di accesso valido. Per farlo, aprire l'app per la logica nella finestra di progettazione e quindi autorizzare le connessioni. In alternativa, è possibile automatizzare l'operazione utilizzando uno script per consentire ogni connessione OAuth. Nel progetto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) è presente uno script di esempio su GitHub.

## <a name="visual-studio-release-management"></a>Release Management per Visual Studio
Uno scenario comune per la distribuzione e la gestione di ambienti consiste nell'usare uno strumento come Release Management per Visual Studio con un modello di distribuzione di app per la logica. Visual Studio Team Services include un'attività [Distribuisci gruppo di risorse di Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) , che può essere aggiunta in una build o in una pipeline di rilascio. Per l'autorizzazione alla distribuzione è necessario avere un'[entità servizio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Sarà quindi possibile generare la definizione della versione.

1. Per creare una nuova definizione in Release Management, selezionare **Vuoto** per iniziare da una definizione vuota.

    ![Creare una nuova definizione vuota][1]   
2. Scegliere le risorse necessarie per questa operazione, ad esempio il modello di app per la logica generato manualmente o come parte del processo di compilazione.
3. Aggiungere un'attività **Distribuzione gruppo di risorse di Azure** .
4. Eseguire la configurazione con un' [entità servizio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), quindi fare riferimento ai file Modello e Parametri modello.
5. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png



<!--HONumber=Jan17_HO3-->


