<properties
	pageTitle="Gestire le risorsa di archiviazione di Azure con Storage Explorer (anteprima) | Microsoft Azure"
	description="Illustra come usare Microsoft Azure Storage Explorer (anteprima) per creare e gestire risorse di archiviazione di Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="05/08/2016"
	ms.author="tarcher" />

# Gestire le risorsa di archiviazione di Azure con Storage Explorer (anteprima)

Microsoft Azure Storage Explorer (anteprima) è uno strumento autonomo che semplifica la gestione degli account di archiviazione di Azure. Risulta utile nei casi in cui si vogliono gestire rapidamente le risorse di archiviazione all'esterno del portale di Azure, ad esempio quando si sviluppano app in Visual Studio. Questa versione in anteprima consente di usare con facilità l'archivio BLOB. È possibile creare ed eliminare contenitori, caricare, scaricare ed eliminare BLOB e infine eseguire ricerche in tutti i contenitori e BLOB. Le funzionalità avanzate consentono ai responsabili delle procedure DevOps di usare le chiavi e i criteri della firma di accesso condiviso. Gli sviluppatori Windows possono usare anche l'emulatore di archiviazione di Azure per testare il codice usando l'account di archiviazione di sviluppo locale.

Per visualizzare o gestire risorse di archiviazione in Storage Explorer, è necessario potere accedere a un account di archiviazione di Azure nella propria sottoscrizione o a un account di archiviazione esterno. Se non si ha un account di archiviazione, è possibile crearne uno in pochi minuti. Se si ha un abbonamento MSDN, vedere [Crediti mensili di Azure per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). In caso contrario, vedere [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## Gestire gli account e le sottoscrizioni di Azure

Per visualizzare le risorse di archiviazione di Azure in Storage Explorer, è necessario accedere a un account Azure con una o più sottoscrizioni attive. Se sono disponibili più account di Azure, sarà possibile aggiungerli in Storage Explorer, quindi scegliere le sottoscrizioni da includere nella visualizzazione delle risorse in Storage Explorer. Se si usa Azure per la prima volta o non sono stati aggiunti gli account necessari a Visual Studio, sarà necessario effettuare l'accesso a un account di Azure.

### Aggiungere un account di Azure a Storage Explorer

1.	Scegliere l'icona **Settings** (a forma di ingranaggi) sulla barra degli strumenti di Storage Explorer.
1.	Selezionare il collegamento **Add an account**. Accedere all'account di Azure di cui si vogliono esplorare le risorse di archiviazione. L'account appena aggiunto dovrebbe essere selezionato nell'elenco a discesa di selezione dell'account. Tutte le sottoscrizioni associate all'account vengono visualizzate sotto la voce relativa all'account.

	![][0]

1.	Selezionare le caselle di controllo relative alle sottoscrizioni di account da esplorare, quindi fare clic su **Apply**.

	![][1]

	Le risorse di archiviazione di Azure per le sottoscrizioni selezionate verranno visualizzate in Storage Explorer.

### Associare una risorsa di archiviazione esterna

1. Ottenere il nome dell'account e la chiave per l'account di archiviazione da collegare.
	1.	Nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) scegliere l'account di archiviazione da collegare.
	1.	Nella sezione **Gestisci** del riquadro **Impostazioni** fare clic sul pulsante **Chiavi**.
	1.	Copiare i valori di **Nome account di archiviazione** e **Chiave di accesso primaria**.

		![][2]

1.	Dal menu di scelta rapida del nodo **Storage Accounts** in Storage Explorer scegliere il comando **Attach External Storage**.

	![][3]

1. Immettere il nome dell'account di archiviazione nella casella **Account name** e il valore della chiave di accesso primaria nella casella **Account key**. Scegliere **OK** per continuare.

	![][4]

	La risorsa di archiviazione esterna viene visualizzata in Storage Explorer.

	![][5]

1. Per rimuovere la risorsa di archiviazione esterna, dal menu di scelta rapida della risorsa di archiviazione scegliere il comando Detach.

	![][6]

## Visualizzare ed esplorare le risorse di archiviazione

Per esplorare una risorsa di archiviazione di Azure e visualizzare le rispettive informazioni in Storage Explorer, espandere il tipo di archiviazione e quindi scegliere la risorsa. Le informazioni sulla risorsa selezionata vengono visualizzate nelle schede **Actions** e **Properties** nella parte inferiore di Storage Explorer.

![][7]

-	La scheda **Actions** mostra le azioni che possono essere eseguite in Storage Explorer per la risorsa di archiviazione selezionata, ad esempio apertura, copia o eliminazione della risorsa. Le azioni vengono visualizzata anche nel menu di scelta rapida della risorsa.

-	La scheda **Properties** mostra le proprietà della risorsa di archiviazione, ad esempio il tipo, le impostazioni locali, il gruppo di risorse associato e l'URL.

L'azione **Open in portal** è disponibile per tutti gli account di archiviazione. Quando si sceglie questa azione, Storage Explorer mostra l'account di archiviazione selezionato nel portale di anteprima di Azure.

Azioni aggiuntive e altri valori di proprietà vengono visualizzati in base alla risorsa selezionata. Ad esempio, per i nodi relativi a contenitori BLOB, code e tabelle è disponibile l'azione **Create**. Per i singoli elementi, ad esempio i contenitori BLOB, sono disponibili azioni quali **Open**, **Delete** e **Get Shared Access Signature**. Un'azione per l'apertura dell'editor di BLOB viene visualizzata quando si sceglie un BLOB dell'account di archiviazione.

## Cercare account di archiviazione e contenitori BLOB

Per trovare account di archiviazione e contenitori BLOB con un nome specifico nelle sottoscrizioni dell'account di Azure, immettere il nome nella casella **Search** in Storage Explorer.

![][8]

Quando si immettono caratteri nella casella **Search**, nell'albero delle risorse vengono visualizzati solo gli account di archiviazione o i contenitori BLOB i cui nomi corrispondono ai caratteri immessi. Per cancellare i criteri di ricerca, scegliere **x** nella casella **Search**.

## Modificare gli account di archiviazione

Per aggiungere o cambiare i contenuti di un account di archiviazione, scegliere il comando **Open Editor** per il tipo di archiviazione specifico. È possibile scegliere azioni dal menu di scelta rapida dell'elemento selezionato oppure selezionarle nella scheda **Actions** nella parte inferiore di Storage Explorer.

![][9]

È possibile creare o eliminare contenitori BLOB, code e tabelle. È anche possibile modificare i contenitori BLOB in Storage Explorer scegliendo l'azione **Open Blob Container Editor**.

### Modificare un contenitore BLOB

1.	Scegliere l'azione **Open Blob Container Editor**. L'editor di contenitori BLOB viene visualizzato nel riquadro destro.

	![][10]

1.	Fare clic su **Upload** e quindi scegliere il comando **Upload Files**.

	![][11]

	Se i file da caricare si trovano in una singola cartella, è possibile scegliere invece il comando Upload Folder.

1. Nella finestra di dialogo **Upload files** scegliere il pulsante con i puntini di sospensione (**…**) a destra della casella Files per selezionare i file da caricare. Scegliere quindi il tipo di contenitore BLOB da usare per il caricamento, ovvero blocco, pagina, aggiunta. Se si vuole, è possibile scegliere di caricare i file in una cartella nel contenitore BLOB. Immettere il nome della cartella nella casella **Upload to folder (optional)**. Se la cartella non esiste, verrà creata.

	![][12]

	Nella schermata seguente tre file di immagine sono stati caricati in una nuova cartella denominata **My New Files** nel contenitore BLOB **Images**.

	![][13]

	I pulsanti sulla barra degli strumenti dell'editor di BLOB consentono di selezionare, scaricare, aprire, copiare ed eliminare i file e altro ancora. Il riquadro **Activity** nella parte inferiore della finestra di dialogo indica se l'operazione ha avuto esito positivo e consente di rimuovere solo le attività riuscite dalla visualizzazione o di eliminare interamente i contenuti del riquadro. Scegliere l'icona **+** accanto ai file caricati per visualizzare un elenco dettagliato dei file caricati.

## Creare una firma di accesso condiviso

Per alcune operazioni potrebbe essere necessaria una firma di accesso condiviso (SAS, Shared Access Signature) per accedere a una risorsa di archiviazione. È possibile crearne una usando Storage Explorer.

1.	Selezionare l'elemento per cui si vuole creare una firma di accesso condiviso e quindi scegliere il comando **Get Shared Access Signature** nel riquadro **Actions** o dal menu di scelta rapida dell'elemento.

	![][14]

1.	Nella finestra di dialogo **Shared Access Signature** scegliere il criterio, le date di inizio e di scadenza e un fuso orario. Selezionare anche le caselle di controllo per i livelli di accesso desiderati per la risorsa, ad esempio sola lettura, lettura-scrittura e così via. Al termine, scegliere il pulsante **Create** per creare la firma di accesso condiviso.

	![][15]

1.	La finestra di dialogo **Shared Access Signature** elenca il contenitore insieme all'URL e alle stringhe di query che è possibile usare per accedere alla risorsa di archiviazione. Scegliere il pulsante **Copy** per copiare le stringhe.

	![][16]

## Gestire la firma di accesso condiviso e le autorizzazioni

Per controllare l'accesso ai contenitori BLOB, è possibile scegliere i comandi **Manage Access Control List** e **Set Public Access Level**.

-	Il comando Manage Access Control List consente di aggiungere, modificare e rimuovere i criteri di accesso, ovvero autorizzazioni di lettura, scrittura e così via per l'utente, nel contenitore BLOB selezionato.
-	Il comando Set Public Access Level consente di determinare il livello di accesso concesso agli utenti pubblici per la risorsa.  

-

1.	Scegliere il contenitore BLOB e quindi scegliere il comando **Manage Access Control List** dal menu di scelta rapida o nel riquadro **Actions**.

	![][17]

1.	Nella finestra di dialogo **Access Control List** scegliere il pulsante **Add** per aggiungere criteri di accesso. Scegliere un criterio di accesso e quindi selezionare le rispettive autorizzazioni. Al termine, scegliere **Save**.

	![][18]

1.	Per configurare un livello di accesso per un contenitore BLOB, selezionarlo in Storage Explorer e quindi scegliere il comando **Set Public Access Level** dal menu di scelta rapida o nel riquadro **Actions**.

	![][19]

1.	Nella finestra di dialogo **Set Container Public Access Level** selezionare il pulsante di opzione per il livello di accesso da concedere agli utenti pubblici, quindi scegliere **Apply**.

	![][20]

## Passaggi successivi
Per altre informazioni sulle funzionalità nei servizi di Archiviazione di Azure, vedere gli articoli in [Introduzione ad Archiviazione di Microsoft Azure](./storage/storage-introduction.md).

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_0511_2016-->