<properties
   pageTitle="Creazione e distribuzione di progetti Gruppo di risorse di Azure con Visual Studio | Microsoft Azure"
   description="Usare Visual Studio per creare un progetto Gruppo di risorse di Azure e distribuire le risorse in Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor="" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="tomfitz" />

# Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio

Con Visual Studio e [Azure SDK](https://azure.microsoft.com/downloads/) è possibile creare un progetto che distribuisce l'infrastruttura e il codice in Azure. Ad esempio, è possibile definire l'host Web, il sito Web e il database per l'app e distribuire l'infrastruttura insieme al codice. In alternativa, è possibile definire una macchina virtuale, una rete virtuale e un account di archiviazione e distribuire questa infrastruttura insieme a uno script che viene eseguito nella macchina virtuale. Il progetto di distribuzione **Gruppo di risorse di Azure** consente di distribuire tutte le risorse necessarie in una singola operazione ripetibile. Per altre informazioni sulla distribuzione e sulla gestione delle risorse, vedere [Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md).

I progetti di tipo Gruppo di risorse di Azure contengono modelli JSON di Gestione risorse di Azure, che definiscono le risorse che vengono distribuite in Azure. Per informazioni sugli elementi del modello di Gestione risorse, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md). Visual Studio consente di modificare questi modelli e offre strumenti utili per semplificare l'utilizzo dei modelli.

In questo argomento verrà eseguita la distribuzione di un'app Web e un database SQL. Questa procedura, tuttavia, è quasi uguale per qualsiasi tipo di risorsa. È possibile distribuire con altrettanta facilità una macchina virtuale e le rispettive risorse. Visual Studio offre molti modelli di partenza per la distribuzione di scenari comuni.

## Creare un progetto Gruppo di risorse di Azure

In questa procedura verrà creato un progetto Gruppo di risorse di Azure con un modello **App Web e SQL**.

1. In Visual Studio scegliere **File**, **Nuovo progetto**, scegliere **C#** o **Visual Basic**. Scegliere quindi **Cloud** e infine scegliere il progetto **Gruppo di risorse di Azure**.

    ![Progetto Distribuzione cloud](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Scegliere il modello da distribuire in Gestione risorse di Azure. Si noti che sono disponibili molte opzioni diverse in base al tipo di progetto da distribuire. Per questo argomento verrà scelto il modello **App Web e SQL**.

    ![Scelta di un modello](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Il modello scelto è solo un punto di partenza. È possibile aggiungere e rimuovere risorse per ottenere lo scenario desiderato.

    >[AZURE.NOTE] L'elenco dei modelli disponibili viene recuperato online ed è soggetto a modifiche.

    Visual Studio crea un progetto di distribuzione di tipo Gruppo di risorse per l'app Web e il database SQL.

1. Espandere i nodi nel progetto di distribuzione per visualizzare gli elementi che sono stati creati.

    ![visualizzazione dei nodi](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Poiché per questo esempio è stato scelto il modello App Web e SQL, verranno visualizzati i file seguenti.

    |Nome file|Descrizione|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Script di PowerShell che richiama i comandi PowerShell per la distribuzione in Gestione risorse di Azure.<br />**Nota** Questo script PowerShell viene usato da Visual Studio per distribuire il modello. Eventuali modifiche apportate allo script influiranno anche sulla distribuzione in Visual Studio. Occorre quindi prestare attenzione.|
    |WebSiteSQLDatabase.json|Modello di Gestione risorse che definisce l'infrastruttura da distribuire in Azure e i parametri che possono essere specificati durante la distribuzione. Definisce anche le dipendenze tra le risorse, in modo che vengano distribuite nell'ordine corretto.|
    |WebSiteSQLDatabase.parameters.json|File di parametri contenente i valori necessari per il modello, ovvero i valori da passare per personalizzare ogni distribuzione.|
    |AzCopy.exe|Strumento usato dallo script di PowerShell per copiare i file dal percorso di destinazione dell'archivio locale al contenitore dell'account di archiviazione. Questo strumento viene usato solo se si configura il progetto di distribuzione per distribuire il codice insieme al modello.|

    Tutti i progetti di distribuzione di tipo Gruppo di risorse contengono questi quattro file di base. Altri progetti potrebbero includere file aggiuntivi per supportare altre funzionalità.

## Personalizzare il modello di Gestione risorse

È possibile personalizzare un progetto di distribuzione modificando i modelli JSON che descrivono le risorse da distribuire. JSON è l'acronimo di JavaScript Object Notation ed è un formato dati serializzati facile da usare. I file JSON usano uno schema a cui viene fatto riferimento all'inizio di ogni file. Se necessario, è possibile scaricare lo schema ed esaminarlo. Lo schema definisce gli elementi consentiti, i tipi e i formati dei campi, i valori possibili dei valori enumerati e così via. Per informazioni sugli elementi del modello di Gestione risorse, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

Per modificare il modello, aprire il file **WebSiteSQLDatabase.json**.

L'editor di Visual Studio offre strumenti utili per la modifica del modello di Gestione risorse. La finestra **Struttura JSON** semplifica la visualizzazione degli elementi definiti nel modello.

![visualizzazione della struttura JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Se si seleziona un elemento disponibile nella struttura, viene visualizzata la parte corrispondente del modello e viene evidenziato il rispettivo codice JSON.

![esplorazione di JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

È possibile aggiungere una nuova risorsa al modello selezionando il pulsante **Aggiungi risorsa** nella parte superiore della finestra Struttura JSON oppure facendo clic con il pulsante destro del mouse su **risorse** e scegliendo **Aggiungi nuova risorsa**.

![aggiungere una risorsa](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Per questa esercitazione selezionare **Account di archiviazione** e specificare un nome. Il nome di un account di archiviazione deve contenere solo numeri e lettere minuscole e non deve superare i 24 caratteri di lunghezza. Il progetto aggiungerà una stringa univoca di 13 caratteri al nome specificato. Assicurarsi quindi che il nome scelto non superi gli 11 caratteri.

![aggiunta di una risorsa di archiviazione](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Si noti che non è stata aggiunta solo la risorsa, ma anche un parametro per il tipo di account di archiviazione e una variabile per il nome dell'account di archiviazione.

![visualizzazione della struttura](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Il parametro **WebSitePicturesType** è preimpostato con i tipi consentiti e un tipo predefinito. È possibile usare questi valori o modificarli per il proprio scenario. Se si vuole impedire agli utenti di distribuire un account di archiviazione di tipo **Premium\_LRS** tramite questo modello, è sufficiente rimuoverlo dai tipi consentiti, come illustrato di seguito.

    "WebSitePicturesType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio fornisce anche il supporto per IntelliSense, per semplificare l'individuazione delle proprietà disponibili durante la modifica del modello. Ad esempio, per modificare le proprietà del piano di servizio app, passare alla risorsa **HostingPlan** e aggiungere un nuovo valore per **properties**. Si noti che IntelliSense mostra i valori disponibili e fornisce una descrizione di ogni valore.

![visualizzazione di IntelliSense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

È possibile impostare **numberOfWorkers** su 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Distribuire il progetto di tipo Gruppo di risorse in Azure

È ora possibile distribuire il progetto. Quando si distribuisce un progetto di tipo Gruppo di risorse di Azure, il progetto viene distribuito in un gruppo di risorse di Azure, ovvero un raggruppamento logico di risorse in Azure, ad esempio app Web, database e così via.

1. Dal menu di scelta rapida del nodo del progetto di distribuzione scegliere **Distribuisci**, **Nuova distribuzione**.

    ![Voce di menu Distribuisci, Nuova distribuzione](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    Verrà visualizzata la finestra di dialogo **Distribuisci in gruppo di risorse**.

    ![Finestra di dialogo Distribuisci in gruppo di risorse](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Nella casella di riepilogo a discesa **Gruppo di risorse** scegliere un gruppo di risorse esistente o crearne uno nuovo. Per creare un gruppo di risorse, aprire la casella di riepilogo a discesa **Gruppo di risorse** e scegliere **<Create New...>**.

    ![Finestra di dialogo Distribuisci in gruppo di risorse](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    Viene visualizzata la finestra di dialogo **Crea gruppo di risorse**. Specificare un nome e una posizione per il gruppo, quindi selezionare **Crea**.

    ![Finestra di dialogo Crea gruppo di risorse](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. È possibile modificare i parametri per la distribuzione scegliendo il pulsante **Modifica parametri**. Specificare i valori per i parametri e selezionare **Salva**.

    ![Finestra di dialogo Modifica parametri](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    L'opzione **Salva password** indica che le password verranno salvate come testo normale nel file JSON. Questa opzione non è sicura.

1. Scegliere il pulsante **Distribuisci** per distribuire il progetto in Azure. È possibile visualizzare lo stato di avanzamento della distribuzione nella finestra **Output**. In base alla configurazione, il completamento della distribuzione potrebbe richiedere alcuni minuti. Immettere la password dell'amministratore del database, se richiesta.

    >[AZURE.NOTE] Potrebbe essere necessario installare i cmdlet di Azure PowerShell. Poiché questi cmdlet sono necessari per distribuire i gruppi di risorse di Azure, sarà necessario installarli.
    
1. Al termine della distribuzione, dovrebbe essere visualizzato un messaggio simile al seguente nella finestra **Output**:

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. In un browser aprire il [portale di Azure](https://portal.azure.com/) e accedere al proprio account. Per visualizzare il gruppo di risorse, selezionare **Gruppi di risorse** e il gruppo di risorse in cui è stata effettuata la distribuzione.

    ![selezione di un gruppo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Verranno visualizzate tutte le risorse distribuite.

    ![visualizzazione delle risorse](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Se si apportano modifiche e si vuole ridistribuire il progetto, è possibile scegliere il gruppo di risorse esistente direttamente dal menu di scelta rapida del progetto di tipo Gruppo di risorse di Azure. Dal menu di scelta rapida scegliere **Distribuisci** e quindi fare clic sul gruppo di risorse in cui è stata appena eseguita la distribuzione.

    ![Gruppo di risorse di Azure distribuito](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Distribuire codice con l'infrastruttura

A questo punto è stata distribuita l'infrastruttura per l'app, ma non è stato ancora distribuito codice effettivo con il progetto. Questo argomento illustra come distribuire un'app Web e tabelle del database SQL durante la distribuzione. Se si sta distribuendo una macchina virtuale invece di un'app Web, è consigliabile eseguire codice nella macchina virtuale come parte della distribuzione. Il processo di distribuzione di codice per un'app Web o per la configurazione di una macchina virtuale è quasi uguale.

1. Nella soluzione di Visual Studio aggiungere un'**Applicazione Web ASP.NET**. 

    ![aggiunta di un'app Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Selezionare **MVC** e deselezionare il campo per **Host nel cloud**, perché il progetto di tipo Gruppo di risorse eseguirà questa attività.

    ![selezione di MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Dopo la creazione dell'app Web, aggiungere un riferimento al progetto di tipo App Web nel progetto di tipo Gruppo di risorse.

    ![aggiunta di un riferimento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Aggiungendo un riferimento si collega il progetto di tipo App Web al progetto di tipo Gruppo di risorse e si impostano tre proprietà chiave. In **Additional Properties** è disponibile la posizione di staging del pacchetto di distribuzione Web di cui verrà eseguito il push nell'Archiviazione di Azure. In **Include File Path** è disponibile il percorso in cui verrà creato il pacchetto. In **Include Targets** è disponibile il comando che verrà eseguito dalla distribuzione. Il valore predefinito di **Build;Package** consente alla distribuzione di sviluppare e creare un pacchetto di distribuzione Web (package.zip). Il profilo di pubblicazione non è necessario, perché la distribuzione ottiene le informazioni necessarie dalle proprietà per creare il pacchetto.
    
      ![visualizzazione di un riferimento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Aggiungere una nuova risorsa al modello e questa volta selezionare **Distribuzione Web per app Web**.

    ![aggiunta della distribuzione Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Ridistribuire il progetto di tipo Gruppo di risorse nel gruppo di risorse. Questa volta sono disponibili alcuni nuovi parametri. Non è necessario specificare valori per **\_artifactsLocation** o **\_artifactsLocationSasToken**, perché vengono generati automaticamente. Impostare la cartella e il nome del file nel percorso che contiene il pacchetto di distribuzione.

    ![aggiunta della distribuzione Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Per **Account di archiviazione elementi** è possibile usare l'account distribuito con questo gruppo di risorse.
    
Al termine della distribuzione è possibile passare al sito e verificare che l'app ASP.NET predefinita sia stata distribuita.

![visualizzazione dell'app distribuita](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## Passaggi successivi

- Per informazioni sulla gestione delle risorse tramite il portale, vedere [Uso del portale di Azure per gestire le risorse di Azure](./azure-portal/resource-group-portal.md).
- Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0218_2016-->