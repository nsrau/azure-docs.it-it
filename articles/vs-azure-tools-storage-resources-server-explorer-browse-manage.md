---
title: Esplorazione e gestione delle risorse di archiviazione con Esplora server | Documentazione Microsoft
description: Esplorazione e gestione delle risorse di archiviazione con Esplora server
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 43ab501c69c0c1e3271dbfcf08e5342a3507ab82
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="browsing-and-managing-storage-resources-with-server-explorer"></a>Esplorazione e gestione delle risorse di archiviazione con Esplora server
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Panoramica
Se sono stati installati gli Strumenti di Azure per Microsoft Visual Studio, è possibile visualizzare i dati BLOB, della coda e della tabella dagli account di archiviazione per Azure. Il nodo Archiviazione di Azure in Esplora server mostra i dati presenti nell'account dell'emulatore di archiviazione locale e negli altri account di archiviazione di Azure.

Per visualizzare Esplora Server in Visual Studio, nella barra dei menu scegliere **Visualizza**, **Esplora server**. Il nodo di archiviazione mostra tutti gli account di archiviazione esistenti in ogni sottoscrizione/certificato di Azure a cui si è connessi. Se l'account di archiviazione non è visualizzato, è possibile aggiungerlo seguendo le istruzioni riportate [più avanti in questo argomento](#add-storage-accounts-by-using-server-explorer).

A partire da Azure SDK 2.7, è anche possibile usare il nuovo Cloud Explorer per visualizzare e gestire le risorse di Azure. Per altre informazioni, vedere [Gestione delle risorse di Azure con Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md) .

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Visualizzare e gestire le risorse di archiviazione in Visual Studio
Esplora server mostra automaticamente un elenco di BLOB, code e tabelle nell'account dell'emulatore di archiviazione. L'account dell'emulatore di archiviazione viene visualizzato come nodo denominato **Sviluppo** sotto il nodo Archiviazione in Esplora server.

Per visualizzare le risorse dell'account dell'emulatore di archiviazione, espandere il nodo **Sviluppo** . Se l'emulatore di archiviazione non è stato avviato, verrà avviato automaticamente quando si espande il nodo **Sviluppo** . Ciò può richiedere alcuni secondi. Durante l'avvio dell'emulatore di archiviazione è possibile continuare a lavorare in altre aree di Visual Studio.

Per visualizzare le risorse in un account di archiviazione, espandere il nodo dell'account di archiviazione in Esplora server. Verranno visualizzati i sottonodi seguenti:

* Blobs
* Code
* Tables

## <a name="work-with-blob-resources"></a>Usare le risorse BLOB
Il nodo BLOB visualizza un elenco di contenitori per l'account di archiviazione selezionato. I contenitori BLOB contengono file BLOB che possono essere organizzati in cartelle e sottocartelle. Per altre informazioni, vedere [Come utilizzare l'archiviazione BLOB da .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md) .

### <a name="to-create-a-blob-container"></a>Per creare un contenitore BLOB
1. Aprire il menu di scelta rapida per il nodo **BLOB** e quindi scegliere **Crea contenitore BLOB**.
2. Immettere il nome del nuovo contenitore nella finestra di dialogo **Crea contenitore BLOB**.  
3. Premere **Invio** sulla tastiera, oppure fare clic o toccare all'esterno del campo nome per salvare il contenitore BLOB.
   
   > [!NOTE]
   > Il nome del contenitore BLOB deve iniziare con un numero (0-9) o una lettera minuscola (a-z).
   > 
   > 

### <a name="to-delete-a-blob-container"></a>Per eliminare un contenitore BLOB
* Aprire il menu di scelta rapida per il contenitore BLOB da rimuovere e quindi scegliere **Elimina**.

### <a name="to-display-a-list-of-the-items-contained-in-a-blob-container"></a>Per visualizzare un elenco degli elementi contenuti in un contenitore BLOB
* Aprire il menu di scelta rapida per un nome di contenitore BLOB nell'elenco, quindi scegliere **Apri**.
  
    Il contenuto di un contenitore BLOB viene visualizzato in una scheda nota come la visualizzazione del contenitore BLOB.
  
    ![VST_SE_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)
  
    È possibile eseguire le operazioni seguenti sui BLOB usando i pulsanti nell'angolo superiore destro della visualizzazione del contenitore BLOB:
  
  * Immettere un valore di filtro e applicarlo
  * Aggiornare l'elenco di BLOB nel contenitore
  * Caricare un file
  * Eliminare un BLOB
    
    > [!NOTE]
    > L'eliminazione di un file da un contenitore BLOB non comporta l'eliminazione del file sottostante che viene solo rimosso dal contenitore BLOB.
    > 
    > 
  * Aprire un BLOB
  * Salvare un BLOB nel computer locale

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Per creare una cartella o una sottocartella in un contenitore BLOB
1. Scegliere il contenitore BLOB in Cloud Explorer. Nella finestra del contenitore scegliere il pulsante **Carica BLOB** .
   
    ![Caricamento di un file in una cartella BLOB](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)
2. Nella finestra di dialogo **Carica nuovo file** scegliere il pulsante **Sfoglia** per specificare il file da caricare e quindi immettere un nome di cartella nella casella **Cartella (facoltativa)**.
   
    Per aggiungere sottocartelle nelle cartelle del contenitore, seguire la stessa procedura. Se non si specifica un nome di cartella, il file verrà caricato nel livello superiore del contenitore BLOB. Il file verrà visualizzato nella cartella specificata nel contenitore.
   
    ![Cartella aggiunta a un contenitore BLOB](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)
3. Fare doppio clic sulla cartella o premere INVIO per visualizzare il contenuto della cartella. Dalla cartella del contenitore è possibile tornare alla radice del contenitore scegliendo il pulsante **Apri directory padre** (freccia su).

### <a name="to-delete-a-container-folder"></a>Per eliminare un cartella del contenitore
* Eliminare tutti i file nella cartella.
  
  > [!NOTE]
  > Poiché le cartelle nei contenitori BLOB sono cartelle virtuali, non è possibile creare una cartella vuota né eliminare una cartella per eliminare i file al suo interno. È necessario eliminare l'intero contenuto di una cartella per eliminare la cartella.
  > 
  > 

### <a name="to-filter-blobs-in-a-container"></a>Per filtrare i BLOB in un contenitore
È possibile filtrare i BLOB visualizzati specificando un prefisso comune.

Ad esempio, se si immette il prefisso `hello` nella casella di testo del filtro e quindi si sceglie il pulsante **Esegui** (**!**), vengono visualizzati solo i BLOB che iniziano con "hello".

![VST_SE_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

> [!NOTE]
> Il campo del filtro rileva la differenza tra maiuscole e minuscole e non supporta l'applicazione di filtri con caratteri jolly. I BLOB possono essere filtrati solo in base al prefisso. Il prefisso può includere un delimitatore se ne viene usato uno per organizzare i BLOB in una gerarchia virtuale. Ad esempio, impostando come filtro il prefisso HelloFabric/ vengono restituiti tutti i BLOB che iniziano con tale stringa.
> 
> 

### <a name="to-download-blob-data"></a>Per scaricare i dati BLOB
* In **Cloud Explorer** aprire il menu di scelta rapida per uno o più BLOB e quindi scegliere **Apri** oppure scegliere il nome del BLOB e quindi scegliere il pulsante **Apri** oppure fare doppio clic sul nome del BLOB.
  
    Lo stato di avanzamento di un download di BLOB viene visualizzato nella finestra **Log attività di Azure** .
  
    Il BLOB viene aperto nell'editor predefinito per il tipo di file. Se il sistema operativo riconosce il tipo di file, il file viene aperto in un'applicazione installata in locale. In caso contrario, viene richiesto di scegliere un'applicazione appropriata per il tipo di file del BLOB. Il file locale creato durante il download di un BLOB è contrassegnato come di sola lettura.
  
    I dati BLOB vengono memorizzati nella cache in locale e vengono confrontati con l'ora dell'ultima modifica del BLOB nel servizio BLOB. Se il BLOB è stato aggiornato dopo l'ultimo download, verrà scaricato nuovamente. In caso contrario, verrà caricato dal disco locale. Per impostazione predefinita, un BLOB viene scaricato in una directory temporanea. Per scaricare i BLOB in una directory specifica, aprire il menu di scelta rapida per i nomi di BLOB selezionati e scegliere **Salva con nome**. Quando si salva un BLOB in questo modo, il file del BLOB non viene aperto e il file locale viene creato con gli attributi di lettura/scrittura.

### <a name="to-upload-blobs"></a>Per caricare i BLOB
* Scegliere il pulsante **Carica BLOB** quando il contenitore BLOB viene aperto nella relativa visualizzazione.
  
    È possibile scegliere di caricare uno o più file ed è possibile caricare file di qualsiasi tipo. Il **Log attività di Azure** mostra lo stato di avanzamento del caricamento. Per altre informazioni su come usare i dati BLOB, vedere [Come usare il servizio di archiviazione BLOB di Azure in .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Per visualizzare i log trasferiti nei BLOB
* Se si usa Diagnostica di Azure per registrare dati dall'applicazione Azure e si sono trasferiti log nell'account di archiviazione, verranno visualizzati i contenitori creati da Azure per questi log. Visualizzare questi log in Esplora server è un modo semplice per identificare i problemi relativi all'applicazione, soprattutto se è stata distribuita in Azure. Per altre informazioni su Diagnostica di Azure, vedere [Raccogliere dati di registrazione usando Diagnostica di Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Per ottenere l'URL per un BLOB
* Aprire il menu di scelta rapida del BLOB e quindi scegliere **Copia URL**.

### <a name="to-edit-a-blob"></a>Per modificare un BLOB
* Selezionare il BLOB e quindi scegliere il pulsante **Apri BLOB** .
  
    Il file viene scaricato in un percorso temporaneo e aperto nel computer locale. È necessario caricare nuovamente il BLOB dopo avere apportato le modifiche.

## <a name="work-with-queue-resources"></a>Usare le risorse di accodamento
Le code di servizi di archiviazione sono ospitate in un account di archiviazione di Azure e possono essere usate per consentire ai ruoli del servizio cloud di comunicare tra loro e con altri servizi con un meccanismo di trasferimento dei messaggi. È possibile accedere alla coda a livello di programmazione con un servizio cloud e con un servizio Web per i client esterni. È anche possibile accedere alla coda direttamente usando Esplora server in Visual Studio.

Quando si sviluppa un servizio cloud che usa le code, è possibile usare Visual Studio per creare le code e usarle in modo interattivo durante lo sviluppo e il test del codice.

In Esplora server è possibile visualizzare le code in un account di archiviazione, creare ed eliminare code, aprire una coda per visualizzarne i messaggi e aggiungere messaggi a una coda. Quando si apre una coda per la visualizzazione, è possibile visualizzare i singoli messaggi ed eseguire le azioni seguenti sulla coda usando i pulsanti disponibili nell'angolo superiore sinistro:

* Aggiornare la visualizzazione della coda
* Inviare un messaggio alla coda
* Rimuovere dalla coda il messaggio di livello superiore
* Cancellare l'intera coda

La figura seguente mostra una coda contenente due messaggi.

![Visualizzazione di una coda](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Per altre informazioni sulle code dei servizi di archiviazione, vedere [Procedura: Usare il servizio di archiviazione di accodamento](http://go.microsoft.com/fwlink/?LinkID=264702). Per informazioni sul servizio Web per le code dei servizi di archiviazione, vedere [Concetti relativi al servizio di accodamento](http://go.microsoft.com/fwlink/?LinkId=264788). Per informazioni su come inviare messaggi a una coda dei servizi di archiviazione usando Visual Studio, vedere [Invio di messaggi a una coda di servizi di archiviazione](https://msdn.microsoft.com/library/azure/jj649344.aspx).

> [!NOTE]
> Le code di servizi di archiviazione sono diverse dalle code del bus di servizio. Per altre informazioni sulle code del bus di servizio, vedere Code, argomenti e sottoscrizioni del bus di servizio.
> 
> 

## <a name="work-with-table-resources"></a>Usare le risorse di una tabella
Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.

### <a name="to-create-a-table"></a>Per creare una tabella
1. In Cloud Explorer selezionare il nodo **Tabelle** dell'account di archiviazione, quindi scegliere **Crea tabella**.
2. Nella finestra di dialogo **Crea tabella** immettere un nome per la tabella.

### <a name="to-view-table-data"></a>Per visualizzare i dati in una tabella
1. In Cloud Explorer aprire il nodo **Azure**, quindi aprire il nodo **Archiviazione**.
2. Aprire il nodo dell'account di archiviazione che interessa e quindi aprire il nodo **Tabelle** per visualizzare un elenco di tabelle per l'account di archiviazione.
3. Aprire il menu di scelta rapida per una tabella e quindi scegliere **Visualizza tabella**.
   
    ![Tabella di Azure in Esplora soluzioni](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

La tabella è organizzata in entità (mostrate nelle righe) e proprietà (mostrate nelle colonne). Ad esempio, la figura seguente mostra le entità elencate in **Progettazione tabelle**:

### <a name="to-edit-table-data"></a>Per modificare i dati della tabella
1. In **Progettazione tabelle** aprire il menu di scelta rapida per un'entità (una singola riga) o una proprietà (una singola cella) e quindi scegliere **Modifica**.
   
    ![Aggiunta o modifica di un'entità di tabella](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)
   
    Le entità di una tabella non devono avere lo stesso set di proprietà (colonne). Tenere presenti le limitazioni seguenti relative alla visualizzazione e alla modifica dei dati della tabella.
   
   * I dati binari (tipo byte[]) non possono essere visualizzati o modificati, ma possono essere archiviati in una tabella.
   * Non è possibile modificare i valori **PartitionKey** o **RowKey**, perché l'archivio tabelle in Azure non supporta tale operazione.
   * Una proprietà chiamata Timestamp non può essere creata perché una proprietà con quel nome è usata dai servizi di archiviazione di Azure.
   * Se si immette un valore DateTime, è necessario applicare un formato appropriato alle impostazioni di area geografica e lingua del computer (ad esempio, MM/GG/AAAA HH:MM:SS [AM|PM] per l'inglese, Stati Uniti).

### <a name="to-add-entities"></a>Per aggiungere le entità
1. In **Progettazione tabelle**, selezionare il pulsante **Aggiungi entità**.
   
    ![Aggiungi entità](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)
2. Nella finestra di dialogo **Aggiungi entità** immettere i valori delle proprietà **PartitionKey** e **RowKey**.
   
    ![Finestra di dialogo Aggiungi entità](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)
   
    Immettere i valori con attenzione perché non è possibile cambiarli dopo aver chiuso la finestra di dialogo, a meno che l'entità non venga eliminata e quindi riaggiunta.

### <a name="to-filter-entities"></a>Per filtrare le entità
Se si usa il generatore di query, è possibile personalizzare il set di entità visualizzato in una tabella.

1. Per aprire il generatore di query, visualizzare prima una tabella.
2. Scegliere il pulsante Generatore di query nella barra degli strumenti della visualizzazione tabella.
   
    Viene visualizzata la finestra di dialogo **Generatore di query** . La figura seguente mostra una query compilata nel generatore di query.
   
    ![Generatore di query](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
3. Una volta compilata la query, chiudere la finestra di dialogo. La query risultante in formato testo viene visualizzata in una casella di testo come filtro di WCF Data Services.
4. Per eseguire la query, scegliere l'icona a forma di triangolo di colore verde.
   
    È anche possibile filtrare i dati delle entità visualizzati in **Progettazione tabelle** se si immette una stringa di filtro di WCF Data Services direttamente nel campo del filtro. Una stringa di questo tipo è simile a una clausola SQL WHERE, ma viene inviata al server come richiesta HTTP. Per altre informazioni su come costruire stringhe di filtro, vedere [Creazione di stringhe di filtro per Progettazione tabelle](https://msdn.microsoft.com/library/azure/ff683669.aspx).
   
    La figura seguente mostra un esempio di una stringa di filtro valida:
   
    ![VST_SE_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### <a name="refresh-storage-data"></a>Aggiornare i dati di archiviazione
Quando Esplora server si connette o riceve dati da un account di archiviazione, il completamento dell'operazione potrebbe richiedere fino a un minuto. Se non è possibile stabilire una connessione, potrebbe verificarsi il timeout dell'operazione. Durante il recupero dei dati è possibile continuare a lavorare in altre aree di Visual Studio. Per annullare l'operazione se sta richiedendo troppo tempo, scegliere il pulsante **Interrompi aggiornamento** sulla barra degli strumenti di Esplora server.

#### <a name="to-refresh-blob-container-data"></a>Per aggiornare i dati del contenitore BLOB
* Selezionare il nodo **BLOB** sotto **Archiviazione** e scegliere il pulsante **Aggiorna** sulla barra degli strumenti di Esplora server.
* Per aggiornare l'elenco di BLOB visualizzato, scegliere il pulsante **Esegui** .

#### <a name="to-refresh-table-data"></a>Per aggiornare i dati della tabella
* Selezionare il nodo **Tabelle** sotto **Archiviazione** e scegliere il pulsante **Aggiorna**.
* Per aggiornare l'elenco di entità visualizzato in **Progettazione tabelle**, scegliere il pulsante **Esegui** in **Progettazione tabelle**.

#### <a name="to-refresh-queue-data"></a>Per aggiornare i dati della coda
* Selezionare il nodo **Code**, quindi scegliere il pulsante **Aggiorna**.

#### <a name="to-refresh-all-items-in-a-storage-account"></a>Per aggiornare tutti gli elementi in un account di archiviazione
* Scegliere il nome account e quindi scegliere il pulsante **Aggiorna** sulla barra degli strumenti di Esplora server.

### <a name="add-storage-accounts-by-using-server-explorer"></a>Aggiungere account di archiviazione usando Esplora server
Sono disponibili due modi per aggiungere gli account di archiviazione usando Esplora server. È possibile creare un nuovo account di archiviazione nella sottoscrizione di Azure o è possibile collegare un account di archiviazione esistente.

#### <a name="to-create-a-new-storage-account-by-using-server-explorer"></a>Per creare un nuovo account di archiviazione usando Esplora server
1. In Esplora server aprire il menu di scelta rapida per il nodo Archiviazione, quindi scegliere Crea account di archiviazione.
   
    ![Creare un nuovo account di archiviazione di Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)
2. Selezionare o immettere le informazioni seguenti per il nuovo account di archiviazione nella finestra di dialogo **Crea account di archiviazione** .
   
   * Sottoscrizione di Azure a cui aggiungere l'account di archiviazione.
   * Nome da usare per il nuovo account di archiviazione.
   * Area o set di affinità (ad esempio Stati Uniti occidentali o Asia orientale).
   * Tipo di replica da usare per l'account di archiviazione, ad esempio Con ridondanza geografica.
3. Scegliere **Create**.
   
    Il nuovo account di archiviazione viene visualizzato nell'elenco **Archiviazione** di Esplora soluzioni.

#### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Per collegare un account di archiviazione esistente usando Esplora server
1. In Esplora server aprire il menu di scelta rapida per il nodo Archiviazione di Azure, quindi scegliere **Associa archiviazione esterna**.
   
    ![Aggiunta di un account di archiviazione esistente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
2. Selezionare o immettere le informazioni seguenti per il nuovo account di archiviazione nella finestra di dialogo **Crea account di archiviazione** .
   
   * Nome dell'account di archiviazione esistente da collegare. È possibile immettere un nome o selezionarne uno dall'elenco.
   * Chiave per l'account di archiviazione selezionato. Questo valore viene in genere immesso automaticamente quando si seleziona un account di archiviazione. Per memorizzare la chiave dell'account di archiviazione in Visual Studio, selezionare la casella Memorizza chiave dell'account.
   * Protocollo da usare per connettersi all'account di archiviazione, ad esempio HTTP, HTTPS o un endpoint personalizzato. Per altre informazioni sugli endpoint personalizzati, vedere [Come configurare le stringhe di connessione](https://msdn.microsoft.com/library/azure/ee758697.aspx) .

### <a name="to-view-the-secondary-endpoints"></a>Per visualizzare gli endpoint secondari
* Se è stato creato un account di archiviazione con l'opzione di replica **Archiviazione con ridondanza geografica e accesso in lettura** , è possibile visualizzare gli endpoint secondari. Aprire il menu di scelta rapida per il nome account e quindi scegliere **Proprietà**.
  
    ![Endpoint secondari di archiviazione](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Per rimuovere un account di archiviazione da Esplora server
* In Esplora server aprire il menu di scelta rapida per il nome account e quindi scegliere **Elimina**. Se si elimina un account di archiviazione, anche le informazioni sulla chiave salvate per tale account verranno rimosse.
  
  > [!NOTE]
  > Se si elimina un account di archiviazione da Esplora server, tale operazione non influisce sull'account di archiviazione né sui dati che contiene, ma rimuove semplicemente il riferimento da Esplora server. Per eliminare definitivamente un account di archiviazione, usare il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).
  > 
  > 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui servizi di archiviazione di Azure, vedere [Uso dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).


