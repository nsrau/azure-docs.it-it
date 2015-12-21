<properties
   pageTitle="Creazione e distribuzione di progetti Gruppo di risorse di Azure con Visual Studio | Microsoft Azure"
   description="Usare Visual Studio per creare un progetto Gruppo di risorse di Azure e distribuire le risorse in Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/13/2015"
   ms.author="kempb" />

# Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio

Il modello di progetto di distribuzione **Gruppo di risorse di Azure** è disponibile in Visual Studio quando è installato Azure SDK 2.6. Il progetto Gruppo di risorse di Azure consente di raggruppare e pubblicare con un'unica operazione di distribuzione più risorse di Azure correlate. Per il funzionamento dei progetti Gruppo di risorse di Azure, viene usata la tecnologia **Gestione risorse di Azure**. **Gestione risorse di Azure** è un servizio API REST che consente di definire gruppi di risorse di Azure, contenenti più risorse di Azure che vengono in genere usate insieme e hanno un ciclo di vita analogo. L'uso dei gruppi di risorse permette di operare su tutte le risorse in un gruppo con una sola chiamata della funzione, invece di chiamare funzioni diverse per ogni singola risorsa. Per altre informazioni sui gruppi di risorse di Azure, vedere [Uso del portale di anteprima di Azure per gestire le risorse di Azure](resource-group-portal.md). Per uno scenario end-to-end più dettagliato relativo alla distribuzione del gruppo di risorse di Azure, vedere [Gruppo di risorse di Azure per Visual Studio](https://azure.microsoft.com/blog/azure-resource-manager-2-5-for-visual-studio/).

I progetti di tipo Gruppo di risorse di Azure contengono modelli JSON di Gestione risorse di Azure, che definiscono gli elementi distribuiti in un gruppo di risorse. Per altre informazioni, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

Gestione risorse di Azure include diversi provider di risorse, che possono essere usati per distribuire risorse quali Ubuntu Server e Windows Server 2012 R2. Questo argomento usa una risorsa **App Web**, che distribuisce un sito Web vuoto di base in Azure.

## Creazione di progetti Gruppo di risorse di Azure

In questa procedura viene illustrato come creare un progetto di tipo Gruppo di risorse di Azure con un modello **App Web**.

### Per creare un progetto Gruppo di risorse di Azure

1. In Visual Studio scegliere **File**, **Nuovo progetto**, scegliere **C#** o **Visual Basic**. Scegliere quindi **Cloud** e infine scegliere il progetto **Gruppo di risorse di Azure**.

    ![Progetto Distribuzione cloud](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Scegliere il modello da distribuire in Gestione risorse di Azure. Per questo esempio verrà scelto il modello **App Web**.

    ![Scelta di un modello](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    È anche possibile aggiungere altre risorse al gruppo di risorse in un secondo momento.

    >[AZURE.NOTE]L'elenco dei modelli disponibili viene recuperato online ed è soggetto a modifiche.

    Visual Studio crea un progetto di distribuzione di tipo Gruppo di risorse di Azure per un'app Web.

1. Espandere i nodi nel progetto di distribuzione per visualizzare gli elementi che sono stati creati.

    Poiché per questo esempio è stato scelto il modello App Web, verranno visualizzati i file seguenti.

    |Nome file|Descrizione|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Script di PowerShell che richiama i comandi PowerShell per la distribuzione in Gestione risorse di Azure.<br />**Nota** Questo script PowerShell viene usato da Visual Studio per distribuire il modello. Eventuali modifiche apportate allo script influiranno anche sulla distribuzione in Visual Studio. Occorre quindi prestare attenzione.|
    !WebSite.json|Modello che definisce l'infrastruttura da distribuire in Azure.|
    |WebSite.param.dev.json|File di parametri contenente i valori specifici necessari al file di configurazione.|
    |AzCopy.exe|Strumento usato dallo script di PowerShell per copiare i file dal percorso di destinazione dell'archivio locale al contenitore dell'account di archiviazione. Questo strumento viene usato solo se si configura il progetto di distribuzione per distribuire il codice insieme al modello.|

    Tutti i progetti di distribuzione di tipo Gruppo di risorse di Azure contengono questi quattro file di base. Altri progetti potrebbero includere file aggiuntivi per supportare altre funzionalità.

## Personalizzazione di un progetto Gruppo di risorse di Azure

È possibile personalizzare un progetto di distribuzione modificando i file di modello JSON che descrivono le risorse di Azure da distribuire. JSON è l'acronimo di JavaScript Object Notation ed è un formato dati serializzati facile da usare.

I progetti Gruppo di risorse di Azure includono due file di modello nel nodo **Modelli** in Esplora soluzioni, che è possibile modificare: un file di modello di Gestione risorse di Azure e un file di parametri.

- I **file di modello di Gestione risorse di Azure** (con estensione json) specificano i file contenenti le risorse desiderate, oltre ai parametri necessari per il progetto di distribuzione, ad esempio nome del sito e posizione. Specificano anche le dipendenze dei componenti nel Gruppo di risorse di Azure e le rispettive proprietà, ad esempio nomi, tag e regole per i trigger. È possibile modificare questo file per aggiungere funzionalità personalizzate. Ad esempio, è possibile aggiungere un database al modello. Per informazioni sui parametri da specificare, vedere la documentazione di ogni provider di risorse. Per altre informazioni, vedere [Provider di risorse](https://msdn.microsoft.com/library/azure/dn790572.aspx).

- I **file dei parametri** (con estensione `.param.*.json`) contengono i valori per i parametri specificati nel file di configurazione, necessari per ogni provider di risorse. In questo esempio il file di configurazione per un'app Web (WebSite.json) definisce i parametri per *siteName* e *siteLocation*. Durante la distribuzione viene richiesto di fornire valori per i parametri nel file di modello e tali valori vengono archiviati nel file dei parametri. È anche possibile modificare direttamente il file dei parametri.

I file JSON possono essere modificati nell'editor di Visual Studio. Se si installa [PowerShell Tools for Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597), si otterrà anche l'evidenziazione della sintassi, la corrispondenza delle parentesi graffe e IntelliSense per semplificare la lettura e la modifica degli script di PowerShell. Un collegamento per l'installazione di PowerShell Tools viene visualizzato nella parte superiore dell'editor, se non è già installato.

![Installazione di PowerShell Tools](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

I file JSON usano uno schema a cui viene fatto riferimento all'inizio di ogni file. Se necessario, è possibile scaricare lo schema ed esaminarlo. Lo schema definisce gli elementi consentiti, i tipi e i formati dei campi, i valori possibili dei valori enumerati e così via.

Per eseguire spesso la distribuzione in configurazioni diverse o modificare le impostazioni, è possibile creare copie diverse del file *param*. Provare a usare lo stesso modello per tutti gli ambienti.

## Distribuzione di un progetto Gruppo di risorse di Azure in un gruppo di risorse di Azure

Quando si distribuisce un progetto di tipo Gruppo di risorse di Azure, il progetto viene distribuito in un gruppo di risorse di Azure, ovvero un raggruppamento logico di risorse in Azure, ad esempio app Web, database e così via.

1. Dal menu di scelta rapida del nodo del progetto di distribuzione scegliere **Distribuisci**, **Nuova distribuzione**.

    ![Voce di menu Distribuisci, Nuova distribuzione](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    Verrà visualizzata la finestra di dialogo **Distribuisci in gruppo di risorse**.

    ![Finestra di dialogo Distribuisci in gruppo di risorse](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. Nella casella a discesa **Gruppo di risorse** scegliere un gruppo di risorse esistente o crearne uno nuovo. Per creare un gruppo di risorse, aprire la casella a discesa **Gruppo di risorse** e scegliere **<Create New...>**.

    Viene visualizzata la finestra di dialogo **Crea gruppo di risorse**.

    ![Finestra di dialogo Crea gruppo di risorse](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]In genere, quando si avvia un nuovo progetto di distribuzione è consigliabile creare un nuovo gruppo di risorse in cui eseguire la distribuzione.

1. Immettere un nome e una posizione per il gruppo di risorse, quindi fare clic sul pulsante **Crea**.

    La posizione del gruppo di risorse non deve essere uguale alla posizione delle risorse, perché le risorse di un gruppo possono interessare diverse aree geografiche.

1. Scegliere i file di configurazione dei modelli e i file dei parametri da usare per la distribuzione oppure accettare le impostazioni predefinite.

    È possibile modificare le proprietà di una risorsa facendo clic sul pulsante **Modifica parametri**. Se i parametri obbligatori non sono presenti quando si esegue la distribuzione, verrà visualizzata la finestra di dialogo **Modifica parametri**, in cui è possibile specificarli. Nella casella **Valore** accanto ai parametri privi di valori viene visualizzato **<null>**. Per questo esempio (una risorsa app Web), i parametri obbligatori includono nome del sito, piano di hosting e posizione del sito. Come si ricorderà, i valori di questi parametri vengono impostati su null per impostazione predefinita nel file dei parametri. Gli altri parametri hanno valori predefiniti.

    ![Finestra di dialogo Modifica parametri](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. Nella finestra di dialogo **Modifica parametri** immettere il nome del sito, la posizione del sito, il nome del piano di hosting e verificare i valori delle eventuali altre proprietà. Al termine, scegliere il pulsante **Salva**.

    - Il parametro *siteName* è la prima parte dell'URL della pagina Web. Ad esempio, per l'URL mywebsitename.azurewebsites.net, il nome del sito è **mywebsitename**.

    - Il parametro *hostingPlanName* specifica il piano di hosting. Per questo esempio è possibile usare "Gratuito". Per altre informazioni sui piani di hosting, vedere [Panoramica approfondita dei piani del servizio app di Azure](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).

    - Il parametro *siteLocation* fa riferimento all'area di Azure in cui il sito deve essere ospitato, ad esempio "Stati Uniti occidentali". Per un elenco delle aree disponibili, vedere [Aree di Azure](http://azure.microsoft.com/regions/).

1. Scegliere il pulsante **Distribuisci** per distribuire il progetto in Azure.

    È possibile visualizzare lo stato di avanzamento della distribuzione nella finestra **Output**. In base alla configurazione, il completamento della distribuzione potrebbe richiedere alcuni minuti.

    >[AZURE.NOTE]Potrebbe essere necessario installare i cmdlet di Microsoft Azure PowerShell. Poiché questi cmdlet sono necessari per distribuire i gruppi di risorse di Azure, sarà necessario installarli.

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com/) da un browser. Poiché si tratta di una nuova modifica, nella scheda **Notifiche** dovrebbe essere disponibile un nuovo messaggio di notifica. Scegliere il messaggio per visualizzare i dettagli relativi al nuovo gruppo di risorse di Azure. Per visualizzare un elenco di tutti i gruppi di risorse disponibili, scegliere la scheda **Sfoglia** e quindi scegliere **Gruppi di risorse**.

    ![Gruppo di risorse di Azure di cui è stato effettuato il provisioning](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. Se si apportano modifiche e si vuole ridistribuire il progetto, è possibile scegliere il gruppo di risorse esistente direttamente dal menu di scelta rapida del progetto di tipo Gruppo di risorse di Azure. Dal menu di scelta rapida scegliere **Distribuisci** e quindi fare clic sul gruppo di risorse in cui è stata appena eseguita la distribuzione.

    ![Gruppo di risorse di Azure distribuito](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    La distribuzione di un gruppo di risorse di Azure distribuisce solo tale progetto. Se la soluzione include un progetto di codice o progetti di altro tipo, sarà necessario distribuirli separatamente.

## Uso di progetti Distribuzione cloud di Azure SDK 2.5 con Azure SDK 2.6

Se si usano progetti di tipo Distribuzione cloud creati con Azure SDK 2.5, è consigliabile eseguire l'aggiornamento ad Azure SDK 2.6 o versione successiva per poter usare le nuove funzionalità per la modifica e la distribuzione di modelli di risorsa di Azure. Il modo più semplice per riutilizzare i modelli creati con Azure SDK 2.5 consiste nel creare la versione di Azure SDK 2.6 o successiva del progetto, spostare i modelli in tale progetto e apportare alcune modifiche.

### Per usare progetti Distribuzione cloud di Azure SDK 2.5 con Azure SDK 2.6 o versione successiva

1. Aggiungere un nuovo progetto di tipo Gruppo di risorse di Azure di Azure SDK 2.6 o versione successiva alla soluzione.

    1. Aprire la soluzione che include il progetto Distribuzione cloud di Azure SDK 2.5.

    1. Scegliere **Nuovo**, **Progetto** dal menu **File**.

    1. Nella finestra di dialogo **Nuovo progetto** trovare il progetto **Gruppo di risorse di Azure **in **Visual C#**/**Cloud** o **Visual Basic**/**Cloud**.

         Il nome del modello di progetto è stato cambiato da **Distribuzione cloud** a Gruppo di risorse di Azure.

    1. Assegnare un nome al progetto.

    1. Selezionare **Aggiungi a soluzione** dalla casella a discesa della soluzione.

    1. Verrà quindi richiesto di selezionare un modello. Poiché i modelli esistenti verranno spostati dal progetto di tipo Distribuzione cloud di Azure SDK 2.5, è possibile scegliere qualsiasi modello. In questo esempio verrà selezionato il modello vuoto alla fine dell'elenco.

    1. Fare clic su **OK**.

        Il nuovo progetto viene aggiunto alla soluzione.

1. Copiare i file di modello e dei parametri dal progetto di tipo Distribuzione cloud di Azure SDK 2.5 nel progetto di tipo Gruppo di risorse di Azure SDK 2.6 o versione successiva.

    1. In Esplora soluzioni trovare il modello e i file dei parametri da copiare nel progetto di distribuzione di Azure SDK 2.5, selezionarli e quindi copiarli.

    2. Incollare i file nella cartella **Modelli** del nuovo progetto di tipo Gruppo di risorse di Azure SDK 2.6 o versione successiva.

1. In Esplora soluzioni trovare il modello e i file dei parametri da copiare nel progetto di distribuzione di Azure SDK 2.5, selezionarli e quindi copiarli.

1. Incollare i file nella cartella Modelli del nuovo progetto di tipo Gruppo di risorse di Azure SDK 2.6 o versione successiva.

1. Se con il modello si distribuisce anche un'applicazione Web, sarà necessario creare un riferimento dal nuovo progetto di tipo Gruppo di risorse di Azure SDK 2.6 o versione successiva all'applicazione Web.

    1. Dal menu contestuale del nodo **Riferimenti** del nuovo progetto di tipo Gruppo di risorse di Azure SDK 2.6 o versione successiva in Esplora soluzioni scegliere **Aggiungi riferimento**.

    1. Selezionare la casella accanto all'applicazione Web nell'elenco di progetti e quindi fare clic su **OK**.

1. Dal menu contestuale del nodo Riferimenti del nuovo progetto di tipo Gruppo di risorse di Azure SDK 2.6 o versione successiva in Esplora soluzioni scegliere Aggiungi riferimento.

1. Selezionare la casella accanto all'applicazione Web nell'elenco di progetti e quindi fare clic su OK.

1. Rinominare tutte le occorrenze dei parametri *dropLocation* e *dropLocationSasToken* in *\_artifactsLocation* e *\_artifactsLocationSasToken*.

1. Se non si prevede di usarli, sarà possibile eliminare il modello vuoto e i file dei parametri aggiunti automaticamente al progetto di Azure SDK 2.6 o versione successiva durante la creazione.

    1. Eliminare il file DeployTemplate.json.

    1. Eliminare il file DeploymentTemplate.param.dev.json.

1. Se sono state apportate modifiche allo script Publish-AzureResourceGroup.ps1 nel progetto di Azure SDK 2.5, sarà necessario spostare tali modifiche nello script Deploy-AzureResourceGroup.ps1 del progetto di Azure SDK 2.6 o versione successiva.

    Ora è possibile distribuire il modello usando il comando di distribuzione nel progetto di tipo Gruppo di risorse di Azure di Azure SDK 2.6 o versione successiva e sfruttare i vantaggi offerti dalle nuove funzionalità per la modifica dei modelli in Azure SDK 2.6. Quando il progetto di Azure SDK 2.6 o versione successiva funziona correttamente, sarà possibile rimuovere il progetto di Azure SDK 2.5 dalla soluzione.

## Motivi che hanno richiesto l'aggiornamento del progetto

Sono state apportate alcune modifiche ai modelli distribuiti in Azure SDK 2.6 che provocano l'incompatibilità con lo script di distribuzione e i modelli di Azure SDK 2.5. La prima nonché principale modifica consiste nel fatto che la distribuzione viene avviata. In Azure SDK 2.5 è disponibile codice compilato che usa le API REST di Azure per caricare il modello e avviare la distribuzione. Gli sviluppatori hanno segnalato di preferire che Visual Studio avvii semplicemente lo script di PowerShell incluso nel progetto. In Azure SDK 2.6, quindi, il comando di distribuzione avvia lo script di PowerShell incluso nel progetto per distribuire il modello. Ciò permette di personalizzare la distribuzione e di eseguire sempre le personalizzazioni, indipendentemente dal fatto che si esegua la distribuzione dalla riga di comando usando Azure PowerShell o con il comando di distribuzione di Visual Studio. Per eseguire la distribuzione da Visual Studio, quando è installato Azure SDK 2.6 o versione successiva sarà necessario usare lo script di PowerShell di distribuzione di Azure SDK 2.6 (o versione successiva).

Sono state apportate modifiche anche ad alcuni nomi di variabile e attività di compilazione per un migliore allineamento con le convenzioni di denominazione nelle compilazioni automatizzate di TFS e altri progetti in Microsoft. Il codice di Visual Studio che raccoglie le variabili e i valori necessari per l'avvio dello script di PowerShell cercherà i nuovi nomi.

## Passaggi successivi

Per informazioni su come aggiungere risorse al gruppo di risorse di Azure in Visual Studio, vedere [Modifica dei modelli di Gestione risorse con Visual Studio](vs-azure-tools-resource-group-adding-resources.md).

<!---HONumber=AcomDC_1210_2015-->