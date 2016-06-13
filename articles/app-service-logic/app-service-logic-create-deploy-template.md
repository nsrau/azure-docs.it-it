<properties
   pageTitle="Creare modelli di distribuzione di app per la logica | Microsoft Azure"
   description="Informazioni sulla creazione di modelli di distribuzione di app per la logica e sull'uso dei modelli per la gestione del rilascio."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# Creare modelli di distribuzione di app per la logica

Dopo la creazione di un'app per la logica, è possibile che la si voglia creare come modello di distribuzione Azure Resource Manager, in modo da semplificare la distribuzione dell'app per la logica in qualsiasi ambiente o gruppo di risorse necessario. Per un'introduzione ai modelli di distribuzione Resource Manager, vedere gli articoli [Creazione di un modello di distribuzione Azure Resource Manager](../resource-group-authoring-templates.md) e [Distribuire le risorse con un modello Azure Resource Manager](../resource-group-template-deploy.md)

## Panoramica dei modelli di distribuzione di app per la logica

Un'app per la logica è costituita da tre componenti di base:

* **Risorsa App per la logica**: risorsa contenente le informazioni su elementi quali piano tariffario, posizione e definizione del flusso di lavoro.
* **Definizione flusso di lavoro**: elemento visualizzato quando si seleziona **Visualizzazione Codice**, ovvero la definizione dei passaggi del flusso e del modo in cui il motore deve essere eseguito. Corrisponde alla proprietà `definition` della risorsa App per la logica.
* **Connessioni**: risorse separate per archiviare in modo sicuro i metadati su qualsiasi connessione del connettore, ad esempio stringhe di connessione e token di accesso. In un'app per la logica si fa riferimento a queste informazioni nella proprietà `parameters` della risorsa App per la logica.

È possibile visualizzare tutti questi elementi per le app per la logica esistenti usando strumenti come [Esplora risorse di Azure](http://resources.azure.com).

Per creare un modello per un'app per la logica che possa essere usato con distribuzioni di gruppi di risorse, è necessario definire le risorse e creare i parametri necessari. Ad esempio, se si esegue la distribuzione in un ambiente di sviluppo, un ambiente di test e un ambiente di produzione, è probabile che si vogliano usare stringhe di connessione diverse per un database SQL in ogni ambiente oppure che si voglia eseguire la distribuzione entro sottoscrizioni o gruppi di risorse diversi.

## Creazione di un modello di distribuzione di app per la logica

Sono disponibili alcuni strumenti utili per la creazione di un modello di distribuzione di app per la logica. È possibile eseguire questa operazione manualmente, selezionando le risorse illustrate in precedenza e creando i parametri necessari. È anche possibile usare un modulo [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) di PowerShell. Il funzionamento del modulo open source è basato sulla valutazione dell'app per la logica e di eventuali connessioni usate dall'app, quindi sulla generazione di risorse del modello con i parametri necessari per la distribuzione. Se, ad esempio, è disponibile un'app per la logica che ha ricevuto un messaggio da una coda del bus di servizio e ha aggiunto dati a un database SQL di Azure, lo strumento conserverebbe l'intera logica di orchestrazione e creerebbe parametri per le stringhe di connessione di SQL e del bus di servizio, in modo che sia possibile configurarle in fase di distribuzione.

>[AZURE.NOTE] Le connessioni devono essere incluse nello stesso gruppo di risorse dell'app per la logica

### Installazione del modulo PowerShell per il modello di app per la logica

Il modo più semplice per eseguire l'installazione consiste nell'usare la [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) con il comando `Install-Module -Name LogicAppTemplate`.

È anche possibile eseguire l'installazione manualmente:

1. Scaricare la versione più recente di [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Estrarre la cartella nella cartella del modulo PowerShell, in genere `%UserProfile%\Documents\WindowsPowerShell\Modules`.

Per consentire il funzionamento del modulo con qualsiasi tenant e qualsiasi token di accesso della sottoscrizione, è consigliabile usare lo strumento da riga di comando [armclient](https://github.com/projectkudu/ARMClient). Per informazioni dettagliate su `armclient`, vedere [qui](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### Generazione del modello di app per la logica tramite PowerShell

Dopo l'installazione, è possibile generare un modello con il comando seguente:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

Dove `$SubscriptionId` è l'ID sottoscrizione di Azure. Questa riga ottiene prima di tutto un token di accesso tramite `armclient`, lo invia tramite pipe allo script di PowerShell, quindi restituisce il modello come output in un file `.json`.

## Aggiunta di parametri a un modello di app per la logica

Dopo avere creato un modello di app per la logica, è possibile continuare ad aggiungere o modificare eventuali parametri aggiuntivi necessari. Se, ad esempio, la definizione include un ID di risorsa in una funzione di Azure o in un flusso di lavoro annidato in cui si prevede eseguire una singola distribuzione, è consigliabile aggiungere altre risorse al modello e creare parametri per gli ID in base alla necessità. Lo stesso approccio è applicabile a qualsiasi riferimento ad API personalizzate o endpoint Swagger che si prevede di distribuire in ogni gruppo di risorse.

## Distribuzione di un modello di app per la logica

Dopo avere creato un modello, è possibile eseguire la distribuzione usando alcuni strumenti, inclusi PowerShell, API REST, Visual Studio, Release Management o la distribuzione modello del portale di Azure. Per informazioni dettagliate sulla distribuzione, vedere [qui](../resource-group-template-deploy.md). È consigliabile creare anche un [file di parametri](../resource-group-template-deploy.md#parameter-file) per l'archiviazione di valori per il parametro.

### Autorizzazione delle connessioni OAuth

Dopo la distribuzione, l'app per la logica funzionerà end-to-end con parametri validi, ma sarà comunque necessario autorizzare le connessioni OAuth per generare un token di accesso valido. È possibile eseguire questa operazione aprendo l'app per la logica nella finestra di progettazione e autorizzando le connessione. In alternativa, se si vuole automatizzare l'operazione, è anche possibile usare uno script per fornire l'autorizzazione per ogni connessione OAuth. Uno script di esempio è disponibile su GitHub nel progetto [Connection Auth](https://github.com/logicappsio/LogicAppConnectionAuth).

## Uso di Release Management per Visual Studio

Uno scenario comune per la distribuzione e la gestione di ambienti consiste nell'usare uno strumento come Release Management per Visual Studio con un modello di distribuzione di app per la logica. VSTS include un'attività [Deploy Azure Resource Group](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup), che può essere aggiunta in una build o in una pipeline di rilascio. Per l'autorizzazione alla distribuzione è necessario avere un'[entità servizio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Sarà quindi possibile generare la definizione della versione.

1. In **Versione** selezionare un valore per iniziare a creare una **Nuova definizione** con una definizione **Vuota**.

    ![][1]

1. Scegliere eventuali elementi necessari per questa operazione, ad esempio il modello di app per la logica generato manualmente o come parte del processo di compilazione.
1. Aggiungere un'attività **Distribuzione gruppo di risorse di Azure**.
1. Eseguire la configurazione con un'[entità servizio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), quindi fare riferimento ai file **Modello** e **Parametri modello**.
1. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->