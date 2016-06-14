<properties
	pageTitle="Guida introduttiva a Storage Explorer (anteprima) | Microsoft Azure"
	description="Gestire le risorsa di archiviazione di Azure con Storage Explorer (anteprima)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# Guida introduttiva a Storage Explorer (anteprima)

## Panoramica 

Microsoft Azure Storage Explorer (anteprima), è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, OSX e Linux. Questo articolo illustra diversi modi per connettersi agli account di archiviazione di Azure e per gestirli.

## Prerequisiti

- [Scaricare e installare Storage Explorer (anteprima)](http://go.microsoft.com/fwlink/?LinkId=708343)

## Connettersi a un account o a un servizio di archiviazione

Storage Explorer (anteprima) offre numerosi modi per connettersi agli account di archiviazione, tra cui la connessione agli account di archiviazione associati alle sottoscrizioni di Azure, la connessione agli account e ai servizi di archiviazione condivisi da altre sottoscrizioni di Azure e anche la connessione e la gestione della risorsa di archiviazione locale con Emulatore di archiviazione di Azure:

- [Connettersi a una sottoscrizione di Azure.](#connect-to-an-azure-subscription): gestire le risorse di archiviazione appartenenti alla sottoscrizione di Azure.
- [Connettersi alla risorsa di archiviazione locale](#connect-to-local-storage): gestire la risorsa di archiviazione locale usando Emulatore di archiviazione di Azure. 
- [Collegarsi a una risorsa di archiviazione esterna](#attach-or-detach-an-external-storage-account): gestire le risorse di archiviazione appartenenti a un'altra sottoscrizione di Azure usando il nome e la chiave dell'account di archiviazione.
- [Collegare l'account usando la firma di accesso condiviso](#attach-account-using-sas): gestire le risorse di archiviazione appartenenti a un'altra sottoscrizione di Azure usando la firma di accesso condiviso.
- [Collegare il servizio usando la firma di accesso condiviso](#attach-service-using-sas): gestire un servizio di archiviazione specifico (contenitore BLOB, coda o tabella) appartenente a un'altra sottoscrizione di Azure usando una firma di accesso condiviso.

## Connettersi a una sottoscrizione di Azure.

> [AZURE.NOTE] Se non si ha un account Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i benefici della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Avviare Storage Explorer (anteprima). 

1. Se si esegue Storage Explorer (anteprima) per la prima volta o se si è già eseguito Storage Explorer (anteprima), ma non ci si è connessi a un account Azure, verrà visualizzata una barra informazioni che consente di connettersi a un account Azure.

	![][0]
	
1. Selezionare **Connetti a Microsoft Azure** e seguire le finestre di dialogo per accedere con un account Microsoft associato ad almeno una sottoscrizione di Azure attiva.

Una volta effettuato l'accesso con un account Microsoft, il riquadro sinistro di Storage Explorer (anteprima) verrà popolato con tutti gli account di archiviazione associati a tutte le sottoscrizioni di Azure associate all'account Microsoft.

### Filtrare le sottoscrizioni di Azure

Storage Explorer (anteprima) consente di filtrare, tra le sottoscrizioni di Azure associate agli account Microsoft connessi, quelle che avranno gli account di archiviazione elencati nel riquadro sinistro.

1. Selezionare l'icona **Impostazioni** (ingranaggi).

	![][1]

1. 	Nella parte superiore del riquadro sinistro verrà visualizzato un elenco a discesa contenente tutti gli account Microsoft a cui si è effettuato l'accesso.

	![][3]
	 
1.	Selezionare la freccia verso il basso accanto all'elenco a discesa per visualizzare tutti gli account Microsoft connessi, oltre a un collegamento per aggiungere (accedere) altri account Microsoft.

	![][4]

1.	Selezionare l'account Microsoft desiderato nell'elenco a discesa.

1. 	Il riquadro sinistro visualizzerà tutte le sottoscrizioni di Azure associate all'account Microsoft selezionato. La casella di controllo a sinistra di ogni sottoscrizione di Azure consente di specificare se si vuole che Storage Explorer (anteprima) elenchi o meno tutti gli account di archiviazione associati a tale sottoscrizione di Azure. Selezionando/Deselezionando **Tutte le sottoscrizioni**, viene alternata la selezione di tutte o di nessuna delle sottoscrizioni di Azure elencate.

	![][2]

1.	Dopo avere completato la selezione delle sottoscrizioni di Azure che si vuole gestire, selezionare **Applica**. Il riquadro sinistro verrà aggiornato con l'elenco di tutti gli account di archiviazione per ogni sottoscrizione di Azure selezionata per l'account Microsoft corrente.

### Aggiunta di altri account Microsoft

I passaggi seguenti illustrano la connessione ad altri account Microsoft per visualizzare gli account di archiviazione e le sottoscrizione di Azure di ogni account.

1.	Selezionare l'icona **Impostazioni** (ingranaggi).

	![][1]

1. 	Nella parte superiore del riquadro sinistro verrà visualizzato un elenco a discesa contenente tutti gli account Microsoft attualmente connessi.

	![][3]
	 
1.	Selezionare la freccia verso il basso accanto all'elenco a discesa per visualizzare tutti gli account Microsoft connessi, oltre a un collegamento per aggiungere (accedere) altri account Microsoft.

	![][4]

1.	Selezionare **Aggiungi un account** e segui le finestre di dialogo per accedere a un account associato ad almeno una sottoscrizione di Azure attiva.

1.	Selezionare le caselle di controllo per le sottoscrizioni di Azure che si vuole esplorare.

	![][2]

1.	Selezionare **Applica**.

### Passare da un account Microsoft all'altro

Anche se è possibile connettersi a più account Microsoft, il riquadro sinistro mostra solo gli account di archiviazione associati alle sottoscrizioni per un account di archiviazione Microsoft singolo (corrente). Se ci si connette a più account Microsoft, è possibile passare da uno all'altro seguendo questa procedura:

1.	Selezionare l'icona **Impostazioni** (ingranaggi).

	![][1]

1. 	Nella parte superiore del riquadro sinistro verrà visualizzato un elenco a discesa contenente tutti gli account Microsoft attualmente connessi.

	![][3]
	 
1.	Selezionare la freccia verso il basso accanto all'elenco a discesa per visualizzare tutti gli account Microsoft connessi, oltre a un collegamento per aggiungere (accedere) altri account Microsoft.

	![][4]

1.	Selezionare l'account Microsoft desiderato.

1.	Selezionare le caselle di controllo per le sottoscrizioni di Azure che si vuole esplorare.

	![][2]

1.	Selezionare **Applica**.
  
## Connettersi alla risorsa di archiviazione locale

Storage Explorer (anteprima) consente di lavorare con la risorsa di archiviazione locale usando Emulatore di archiviazione di Azure che consente di scrivere codice per la risorsa di archiviazione e di testarla senza necessariamente avere un account di archiviazione distribuito in Azure, perché l'account di archiviazione viene emulato da Emulatore di archiviazione di Azure.

>[AZURE.NOTE] Emulatore di archiviazione di Azure è attualmente supportato solo per Windows.

1. Avviare Storage Explorer (anteprima). 

1. Nel riquadro sinistro espandere il nodo **(Sviluppo)**.

	![][21]

1. Se Emulatore di archiviazione di Azure non è ancora stato installato, verrà chiesto di farlo tramite una barra informazioni. Se la barra informazioni viene visualizzata, selezionare **Scarica la versione più recente** e installare l'emulatore.

	![][22]

1. Una volta installato l'emulatore, sarà possibile creare e usare BLOB, code e tabelle locali. Per informazioni su come usare ogni tipo di account di archiviazione, selezionare sotto il collegamento appropriato:

	- [Manage Azure blob storage resources](./vs-azure-tools-storage-explorer-blobs.md) (Gestire risorse di archiviazione BLOB di Azure)
	- Manage Azure queue storage resources (Gestire risorse di archiviazione code di Azure): *presto disponibile*
	- Manage Azure table storage resources (Gestire risorse di archiviazione tabelle di Azure): *presto disponibile*

## Collegare o scollegare un account di archiviazione esterno

Storage Explorer (anteprima) consente di collegarsi agli account di archiviazione esterni per poter condividere facilmente gli account di archiviazione. Questa sezione illustra come collegarsi (e scollegarsi) agli account di archiviazione esterni.

### Ottenere le credenziali dell'account di archiviazione

Per condividere un account di archiviazione esterno, il proprietario di tale account deve ottenere prima le credenziali (nome e chiave dell'account) per l'account e quindi condividere tali informazioni con la persona che vuole collegarsi a tale account (esterno). Per ottenere le credenziali dell'account di archiviazione, è possibile seguire questa procedura nel portale di Azure:

1.	Accedere al [portale di Azure](https://portal.azure.com).
1.	Selezionare **Esplora**.
1.	Selezionare **Account di archiviazione**.
1.	Nel pannello **Account di archiviazione** selezionare l'account di archiviazione desiderato.
1.	Nel pannello **Impostazioni** per l'account di archiviazione selezionato selezionare **Chiavi di accesso**.

	![][5]
	
1.	Nel pannello **Chiavi di accesso** copiare i valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **KEY1** da usare quando ci si collega all'account di archiviazione.

	![][6]

### Collegarsi a un account di archiviazione esterno

1.	In Storage Explorer (anteprima) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Associa archiviazione esterna** dal menu di scelta rapida.

	![][7]
	
1.	La sezione *Ottenere le credenziali dell'account di archiviazione* illustra come ottenere i valori del nome dell'account di archiviazione e della chiave 1. Tali valori verranno usati in questo passaggio. Nella finestra di dialogo **Associa archiviazione esterna** immettere il nome dell'account di archiviazione nella casella **Nome account** e il valore della chiave 1 nella casella **Chiave dell'account**. Fare clic su **OK** al termine dell'operazione.

	![][8]

	Una volta collegato, l'account di archiviazione esterno verrà visualizzato con il testo **(Esterno)** aggiunto al nome dell'account di archiviazione.

	![][9]

### Scollegarsi da un account di archiviazione esterno

1. 	Fare clic con il pulsante destro del mouse sull'account di archiviazione esterno che si vuole scollegare e scegliere **Scollega** dal menu di scelta rapida.

	![][10]

1.	Quando viene visualizzata la finestra di dialogo del messaggio di conferma, selezionare **Sì** per confermare lo scollegamento dall'account di archiviazione esterno.

	![][12]

## Collegare l'account usando la firma di accesso condiviso

Una firma di accesso condiviso consente all'amministratore di una sottoscrizione di Azure di concedere temporaneamente l'accesso a un account di archiviazione senza dover fornire le credenziali della sottoscrizione di Azure.

Per illustrare questo concetto, si supponga che l'utente A sia un amministratore di una sottoscrizione di Azure e che l'utente A voglia consentire all'utente B di accedere a un account di archiviazione per un periodo limitato con determinate autorizzazioni:

1. L'utente A genera una firma di accesso condiviso (costituita dalla stringa di connessione per l'account di archiviazione) per un periodo di tempo specifico e con le autorizzazioni desiderate.
1. L'utente A condivide la firma di accesso condiviso con la persona che vuole accedere all'account di archiviazione, nel nostro esempio l'utente B.  
1. L'utente B usa Storage Explorer (anteprima) per collegarsi all'account appartenente all'utente A usando la firma di accesso condiviso fornita. 

### Ottenere una firma di accesso condiviso per l'account che si vuole condividere

1.	Aprire Storage Explorer (anteprima).
1.	Nel riquadro sinistro fare clic con il pulsante destro del mouse sull'account di archiviazione che si vuole condividere e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso) dal menu di scelta rapida.

	![][13]

1. Nella finestra di dialogo **Shared Access Signature** (Firma di accesso condiviso) specificare l'intervallo di tempo e le autorizzazioni per l'account e selezionare **Crea**.

	![][14]
 
1. Verrà visualizzata una seconda finestra di dialogo **Shared Access Signature** (Firma di accesso condiviso) contenente la firma di accesso condiviso. Selezionare **Copia** accanto alla **Stringa di connessione** per copiarla negli Appunti. Selezionare **Chiudi** per chiudere la finestra di dialogo.

### Collegarsi all'account condiviso usando la firma di accesso condiviso

1.	Aprire Storage Explorer (anteprima).
1.	Nel riquadro sinistro fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Attach account using SAS** (Collega account usando la firma di accesso condiviso). ![][15]

1. Nella finestra di dialogo **Attach Account using SAS** (Collega account usando la firma di accesso condiviso):

	- **Nome account**: immettere il nome che si vuole associare a questo account. **NOTA:** il nome dell'account non deve corrispondere al nome dell'account di archiviazione originale per cui è stata generata la firma di accesso condiviso. 
 	- **Stringa di connessione**: incollare la stringa di connessione copiata prima.
 	- Fare clic su **OK** al termine dell'operazione.
	
	![][16]

Una volta collegato, l'account di archiviazione verrà visualizzato con il testo (SAS) ((Firma di accesso condiviso)) aggiunto al nome dell'account fornito.

![][17]

## Collegare il servizio usando la firma di accesso condiviso

La sezione [Collegare l'account usando la firma di accesso condiviso](#attach-account-using-sas) illustra come l'amministratore di una sottoscrizione di Azure può concedere l'accesso temporaneo a un account di archiviazione generando (e condividendo) una firma di accesso condiviso per l'account di archiviazione. Analogamente, una firma di accesso condiviso può essere generata per un servizio specifico (contenitore BLOB, coda o tabella) in un account di archiviazione.

### Generare una firma di accesso condiviso per il servizio che si vuole condividere

In questo contesto, un servizio può essere un contenitore BLOB, una coda o una tabella. Le sezioni seguenti illustrano come generare la firma di accesso condiviso per il servizio elencato:

- [Get the SAS for a blob container](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) (Ottenere la firma di accesso condiviso per un contenitore BLOB)
- Get the SAS for a queue (Ottenere la firma di accesso condiviso per una coda): *presto disponibile*
- Get the SAS for a table (Ottenere la firma di accesso condiviso per una tabella): *presto disponibile*

### Collegarsi al servizio account condiviso usando la firma di accesso condiviso

1.	Aprire Storage Explorer (anteprima).
1.	Nel riquadro sinistro fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Attach service using SAS** (Collega servizio usando la firma di accesso condiviso). ![][18]

1. Nella finestra di dialogo **Attach Account using SAS** (Collega servizio usando la firma di accesso condiviso) incollare l'URI della firma di accesso condiviso copiato prima e fare clic su **OK**.

	![][19]

Una volta collegato, il nuovo servizio collegato verrà visualizzato nel nodo **(Service SAS)** ((Firma di accesso condiviso servizio)).

![][20]

## Cercare gli account di archiviazione

Se l'elenco di account di archiviazione è lungo, è possibile trovare rapidamente un determinato account di archiviazione usando la casella di ricerca nella parte superiore del riquadro sinistro.

Mentre si digita nella casella di ricerca, il riquadro sinistro visualizzerà solo gli account di archiviazione che corrispondono al valore di ricerca immesso fino a quel momento. Lo screenshot seguente illustra un esempio in cui sono stati cercati tutti gli account di archiviazione il cui nome contiene il testo "tarcher".

![][11]
	
Per cancellare i criteri di ricerca, selezionare **x** nella casella di ricerca.

## Passaggi successivi
- [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer (anteprima)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0608_2016-->