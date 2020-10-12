---
title: Gestire le risorse di Azure Cosmos DB tramite Azure Storage Explorer
description: Informazioni su come connettersi ad Azure Cosmos DB e gestire le relative risorse con Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 938968599f1824416666818a46cc73a1d33c5341
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987743"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Gestire le risorse di Azure Cosmos DB tramite Azure Storage Explorer

È possibile usare Azure Storage Explorer per connettersi a Azure Cosmos DB. Consente di connettersi a Azure Cosmos DB account ospitati in Azure e cloud sovrani da Windows, macOS o Linux.

Usare lo stesso strumento per gestire le diverse entità di Azure in un'unica posizione. È possibile gestire Azure Cosmos DB entità, manipolare i dati, aggiornare stored procedure e trigger insieme ad altre entità di Azure, ad esempio BLOB di archiviazione e code.

Azure Storage Explorer supporta gli account Cosmos configurati per le API SQL, MongoDB, Graph e Table. Per ulteriori informazioni, vedere [Azure Cosmos DB in Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) .

## <a name="prerequisites"></a>Prerequisiti

Un account Cosmos con un'API SQL o un'API Azure Cosmos DB per MongoDB. Se non si dispone di un account, è possibile crearne uno nel portale di Azure. Per altre informazioni, vedere [Azure Cosmos DB: creare un'app Web per le API SQL con .NET e il portale di Azure](create-sql-api-dotnet.md) .

## <a name="installation"></a>Installazione

Per installare i bit di Azure Storage Explorer più recenti, vedere [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Sono supportate le versioni di Windows, Linux e macOS.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

1. Dopo l'installazione di **Azure Storage Explorer**, selezionare l'icona del **plug-in** nel riquadro sinistro.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Selezionare **Add an Azure Account** (Aggiungi un account Azure) e quindi **Accedi**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Nella finestra **di dialogo di accesso ad Azure** selezionare **Accedi**, quindi immettere le credenziali di Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Selezionare la sottoscrizione nell'elenco e quindi **Applica**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

    Il riquadro di esplorazione viene aggiornato e Mostra gli account nella sottoscrizione selezionata.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

    L' **account Cosmos DB** è connesso alla sottoscrizione di Azure.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Usare una stringa di connessione per connettersi a Azure Cosmos DB

È possibile usare una stringa di connessione per connettersi a un Azure Cosmos DB. Questo metodo supporta solo le API SQL e Table. Per connettersi con una stringa di connessione, seguire questa procedura:

1. Trovare **locale e collegato** nell'albero a sinistra, fare clic con il pulsante destro del mouse su **account Cosmos DB**, quindi selezionare **Connetti a Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

2. Nella finestra **Connetti a Cosmos DB** :
   1. Selezionare l'API dal menu a discesa.
   1. Incollare la stringa di connessione nella casella **stringa di connessione** . Per informazioni su come recuperare la stringa di connessione primaria, vedere [ottenere la stringa di connessione](manage-with-powershell.md#list-keys).
   1. Immettere un' **etichetta di account**, quindi selezionare **Avanti** per verificare il riepilogo.
   1. Selezionare **Connetti** per connettere l'account Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Usare un emulatore locale per connettersi a Azure Cosmos DB

Usare la procedura seguente per connettersi a un Azure Cosmos DB con un emulatore. Questo metodo supporta solo gli account SQL.

1. Installare Cosmos DB Emulator, quindi aprirlo. Per informazioni sull'installazione dell'emulatore, vedere [Cosmos DB Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

1. Trovare **locale e collegato** nell'albero a sinistra, fare clic con il pulsante destro del mouse su **account Cosmos DB**, quindi selezionare **Connetti a Cosmos DB emulatore**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Nella finestra **Connetti a Cosmos DB** :
   1. Incollare la stringa di connessione nella casella **stringa di connessione** . Per informazioni sul recupero della stringa di connessione primaria, vedere [ottenere la stringa di connessione](manage-with-powershell.md#list-keys).
   1. Immettere un' **etichetta di account**, quindi selezionare **Avanti** per verificare il riepilogo.
   1. Selezionare **Connetti** per connettere l'account Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

## <a name="azure-cosmos-db-resource-management"></a>Gestione risorse di Azure Cosmos DB

Per gestire un account Azure Cosmos DB, utilizzare le operazioni seguenti:

* Aprire l'account nel portale di Azure.
* Aggiungere la risorsa all'elenco di accesso rapido.
* Eseguire ricerche e aggiornare le risorse.
* Creare ed eliminare database.
* Creazione ed eliminazione di raccolte.
* Creare, modificare, eliminare e filtrare documenti.
* Gestire stored procedure, trigger e funzioni definite dall'utente.

### <a name="quick-access-tasks"></a>Attività di accesso rapido

È possibile fare clic con il pulsante destro del mouse su una sottoscrizione nel riquadro di esplorazione per eseguire numerose attività di azione rapida, ad esempio:

* Fare clic con il pulsante destro del mouse su un account o database di Azure Cosmos DB, quindi scegliere **Apri nel portale** per gestire la risorsa nel browser nel portale di Azure.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

* Fare clic con il pulsante destro del mouse su un account Azure Cosmos DB, un database o una raccolta, quindi scegliere **Aggiungi ad accesso rapido** per aggiungerlo al menu di accesso rapido.

* Selezionare **Cerca da qui** per abilitare la ricerca di parole chiave nel percorso selezionato.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="database-and-collection-management"></a>Gestione di un database e di una raccolta

#### <a name="create-a-database"></a>Creazione di un database

1. Fare clic con il pulsante destro del mouse sull'account Azure Cosmos DB, quindi scegliere **Crea database**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Immettere il nome del database, quindi premere **invio** per completare l'operazione.

#### <a name="delete-a-database"></a>Eliminare un database

1. Fare clic con il pulsante destro del mouse sul database, quindi scegliere **Elimina database**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Selezionare **Sì** nella finestra popup. Il nodo del database viene eliminato e l'account di Azure Cosmos DB viene aggiornato automaticamente.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="create-a-collection"></a>Creare una raccolta

1. Fare clic con il pulsante destro del mouse sul database e quindi scegliere **Crea raccolta**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Nella finestra Crea raccolta immettere le informazioni richieste, ad esempio **ID raccolta** e **capacità di archiviazione**e così via. Al termine selezionare **OK**.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

1. Selezionare **illimitata** per poter specificare una chiave di partizione e quindi fare clic su **OK** per terminare.

   > [!NOTE]
   > Se viene usata una chiave di partizione quando si crea una raccolta, una volta completata la creazione, non è possibile modificare il valore della chiave di partizione nella raccolta.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="delete-a-collection"></a>Eliminare una raccolta

- Fare clic con il pulsante destro del mouse sulla raccolta, scegliere **Elimina raccolta**, quindi scegliere **Sì** nella finestra popup.

    Il nodo della raccolta viene eliminato e il database viene aggiornato automaticamente.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="document-management"></a>Gestione dei documenti

#### <a name="create-and-modify-documents"></a>Creare e modificare un documento

- Aprire **documenti** nel riquadro sinistro, selezionare **nuovo documento**, modificare il contenuto nel riquadro destro e quindi selezionare **Salva**.
- È anche possibile aggiornare un documento esistente e quindi selezionare **Salva**. Per annullare le modifiche, selezionare **Ignora**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="delete-a-document"></a>Eliminare un documento

* Selezionare il pulsante **Elimina** per eliminare il documento selezionato.

#### <a name="query-for-documents"></a>Eseguire query per documenti

* Per modificare il filtro del documento, immettere una [query SQL](how-to-sql-query.md)e quindi selezionare **applica**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="graph-management"></a>Gestione dei grafi

#### <a name="create-and-modify-a-vertex"></a>Creare e modificare un vertice

* Per creare un nuovo vertice, aprire il **grafico** dal riquadro a sinistra, selezionare **nuovo vertice**, modificare il contenuto e quindi fare clic su **OK**.
* Per modificare un vertice esistente, selezionare l'icona della penna nel riquadro di destra.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="delete-a-graph"></a>Eliminare un grafo

* Per eliminare un vertice, selezionare l'icona del cestino accanto al nome del vertice.

#### <a name="filter-for-graph"></a>Filtrare un grafo

* Per modificare il filtro del grafico, immettere una [query Gremlin](gremlin-support.md)e quindi selezionare **Applica filtro**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="table-management"></a>Gestione delle tabelle

#### <a name="create-and-modify-a-table"></a>Creare e modificare una tabella

* Per creare una nuova tabella:
   1. Nel riquadro sinistro aprire **entità**, quindi selezionare **Aggiungi**.
   1. Nella finestra di dialogo **Aggiungi entità** modificare il contenuto.
   1. Selezionare il pulsante **Aggiungi proprietà** per aggiungere una proprietà.
   1. Selezionare **Inserisci**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

* Per modificare una tabella, selezionare **modifica**, modificare il contenuto e quindi fare clic su **Aggiorna**.

   

#### <a name="import-and-export-table"></a>Importare ed esportare una tabella

* Per importare, selezionare il pulsante **Importa** , quindi scegliere una tabella esistente.
* Per esportare, selezionare il pulsante **Esporta** , quindi scegliere una destinazione.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="delete-entities"></a>Eliminare entità

* Selezionare le entità, quindi fare clic sul pulsante **Elimina** .

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="query-a-table"></a>Eseguire query su una tabella

- Selezionare il pulsante **query** , immettere una condizione di query, quindi selezionare il pulsante **Esegui query** . Per chiudere il riquadro query, selezionare il pulsante **Chiudi query** .

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gestire stored procedure, trigger e UDF

* Per creare un stored procedure:
  1. Nell'albero sinistro fare clic con il pulsante destro del mouse su **stored procedure**e quindi scegliere **Crea stored procedure**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::
  
  1. Immettere un nome a sinistra, immettere il stored procedure Scripts nel riquadro destro e quindi selezionare **Crea**.
  
* Per modificare un stored procedure esistente, fare doppio clic sulla procedura, eseguire l'aggiornamento e quindi selezionare **Aggiorna** per salvarlo. È anche possibile selezionare **Ignora** per annullare la modifica.

* Le operazioni per **trigger** e **UDF** sono simili alle **stored procedure**.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito sono riportate le soluzioni ai problemi comuni che si verificano quando si usa Azure Cosmos DB in Storage Explorer.

### <a name="sign-in-issues"></a>Problemi relativi all'accesso

Riavviare innanzitutto l'applicazione per verificare se il problema è stato risolto. Se il problema persiste, continuare la risoluzione dei problemi.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificato autofirmato nella catena di certificati

Esistono alcuni motivi per cui è possibile che venga visualizzato questo errore, i due più comuni sono:

* Si è protetti da un *proxy trasparente*. Un utente, ad esempio il reparto IT, intercetta il traffico HTTPS, lo decrittografa e quindi lo crittografa usando un certificato autofirmato.

* Si sta eseguendo software, ad esempio un software antivirus. Il software inserisce un certificato TLS/SSL autofirmato nei messaggi HTTPS ricevuti.

Quando Storage Explorer trova un certificato autofirmato, non sa se il messaggio HTTPS ricevuto viene manomesso. Se si dispone di una copia del certificato autofirmato, è possibile indicare Storage Explorer di considerarla attendibile. Se non si è certi di chi ha inserito il certificato, è possibile seguire questi passaggi per provare a scoprire:

1. Installare OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): le versioni Light sono OK.
     - macOS e Linux: devono essere inclusi nel sistema operativo.

1. Eseguire OpenSSL:
    * Windows: passare alla directory di installazione, quindi **/bin/**, quindi fare doppio clic su **openssl.exe**.
    * Mac e Linux: eseguire **openssl** da un terminale.
1. Eseguire `s_client -showcerts -connect microsoft.com:443`.
1. Cercare i certificati autofirmati. Se non si è certi, che sono autofirmati, cercare ovunque che l'oggetto ("s:") e l'autorità emittente ("i:") siano uguali.
1. Se si individuano certificati autofirmati, copiare e incollare tutti gli elementi da e includere **-----inizio certificato-----** per **-----certificato di fine-----** a un nuovo. File CER per ciascuno di essi.
1. Aprire Storage Explorer e quindi fare clic su **modifica**  >  **certificati SSL**  >  **Importa certificati**. Usare la selezione file per trovare, selezionare e aprire. File CER creati.

Se non si trovano certificati autofirmati, è possibile inviare commenti e suggerimenti per ulteriori informazioni.

#### <a name="unable-to-retrieve-subscriptions"></a>Impossibile recuperare le sottoscrizioni

Se non si è in grado di recuperare le sottoscrizioni dopo l'accesso, provare i seguenti suggerimenti:

* Verificare che l'account disponga dell'accesso alle sottoscrizioni. A tale scopo, accedere al [portale di Azure](https://portal.azure.com/).
* Assicurarsi di aver effettuato l'accesso all'ambiente corretto:
  * [Azure](https://portal.azure.com/)
  * [Azure per la Cina](https://portal.azure.cn/)
  * [Azure Germania](https://portal.microsoftazure.de/)
  * [Azure US Gov](https://portal.azure.us/)
  * Ambiente/Azure Stack personalizzato
* Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia configurato correttamente.
* Rimuovere l'account, quindi aggiungerlo di nuovo.
* Eliminare i file seguenti dalla home directory (ad esempio: ovvero c:\utenti\contosouser) e quindi aggiungere di nuovo l'account:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Premere il tasto F12 per aprire la console per sviluppatori. Quando si accede, controllare la console per individuare eventuali messaggi di errore.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

#### <a name="unable-to-see-the-authentication-page"></a>Impossibile visualizzare la pagina di autenticazione

Se non è possibile visualizzare la pagina di autenticazione:

* A seconda della velocità della connessione, il caricamento della pagina di accesso potrebbe richiedere del tempo. Attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione.
* Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia configurato correttamente.
* Nella console degli strumenti di sviluppo (F12), osservare le risposte per verificare se è possibile individuare il motivo per cui l'autenticazione non funziona.

#### <a name="cant-remove-an-account"></a>Non è possibile rimuovere un account

Se non si è in grado di rimuovere un account o se il collegamento di riautenticazione non esegue alcuna operazione:

* Eliminare i file seguenti dalla home directory e quindi aggiungere di nuovo l'account:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Se si desidera rimuovere le risorse di archiviazione associate alla firma di accesso condiviso, eliminare:
  * la cartella %AppData%/StorageExplorer per Windows
  * /Utenti/<your_name>supporto/Libreria/Application/StorageExplorer per macOS
  * ~/.config/StorageExplorer per Linux
  
  > [!NOTE]
  > Se si eliminano questi file, **è necessario immettere nuovamente tutte le credenziali**.

### <a name="httphttps-proxy-issue"></a>Problema del proxy HTTP/HTTPS

Non è possibile elencare i nodi Azure Cosmos DB nell'albero a sinistra quando si configura un proxy HTTP/HTTPS nell'ambiente del servizio app. È possibile utilizzare Azure Cosmos DB Esplora dati nel portale di Azure come soluzione per la soluzione.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema relativo al nodo "Sviluppo" in "Local and Attached" (Locale e collegato)

Non ci sono risposte dopo aver selezionato il nodo **sviluppo** nel nodo **locale e collegato** nell'albero a sinistra. Si tratta di un comportamento previsto.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Associare un account Azure Cosmos DB nell'errore del nodo **locale e collegato**

Se viene visualizzato l'errore seguente dopo avere collegato un account Azure Cosmos DB nel nodo **locale e collegato** , assicurarsi di usare la stringa di connessione corretta.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Errore di espansione del nodo di Azure Cosmos DB

È possibile che venga visualizzato l'errore seguente quando si tenta di espandere i nodi nell'albero di sinistra.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Screenshot che mostra l'icona del plug-in nel riquadro sinistro.":::

Provare i suggerimenti seguenti:

* Controllare se l'account Azure Cosmos DB è in stato di provisioning. Riprovare quando l'account viene creato correttamente.
* Se l'account è sotto i nodi **accesso rapido** o **locale e collegato** , controllare se l'account è stato eliminato. In tal caso, è necessario rimuovere manualmente il nodo.

## <a name="next-steps"></a>Passaggi successivi

* Guardare questo video per informazioni su come usare Azure Cosmos DB in Azure Storage Explorer: [usare Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Per altre informazioni su Storage Explorer e per connettersi ad altri servizi, vedere [Guida introduttiva a Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
