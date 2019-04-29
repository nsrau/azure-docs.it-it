---
title: Connettersi ad Azure Cosmos DB con gli strumenti di analisi BI
description: Informazioni su come usare il driver ODBC di Azure Cosmos DB per creare tabelle e viste in modo che i dati normalizzati possano essere visualizzati in BI e nel software di analisi dei dati.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: a4cf3a9c19497057d7799be3bec78ad0de97e71a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932012"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Connettersi ad Azure Cosmos DB usando gli strumenti di analisi BI con il driver ODBC

Il driver ODBC di Azure Cosmos DB consente di connettersi ad Azure Cosmos DB usando gli strumenti di analisi BI, ad esempio SQL Server Integration Services, Power BI Desktop e Tableau, in modo da analizzare e creare visualizzazioni dei dati di Azure Cosmos DB in tali soluzioni.

Il driver ODBC di Azure Cosmos DB è conforme a ODBC 3.8 e supporta la sintassi ANSI SQL-92. Il driver offre funzionalità avanzate che consentono di rinormalizzare i dati in Azure Cosmos DB. Usando il driver è possibile rappresentare i dati in Azure Cosmos DB come tabelle e viste. Il driver consente di eseguire operazioni SQL su tabelle e viste, ad esempio raggruppamenti in base alle query, inserimenti, aggiornamenti ed eliminazioni.

> [!NOTE]
> La connessione ad Azure Cosmos DB con il driver ODBC è attualmente supportata solo per gli account dell'API SQL di Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Perché è necessario normalizzare i dati?
Azure Cosmos DB è un database senza schema, che consente lo sviluppo rapido di applicazioni e la possibilità di eseguire l'iterazione sui modelli di dati senza essere limitati da uno schema rigoroso. Un singolo database di Azure Cosmos DB può contenere documenti JSON con varie strutture. Questa novità è ideale per lo sviluppo rapido delle applicazioni, ma quando si desidera analizzare e creare report dei dati usando gli strumenti di analisi e BI, è spesso necessario adattare e conformare tali dati a uno schema specifico.

È qui che entra in gioco il driver ODBC. Tramite il driver ODBC è ora possibile rinormalizzare i dati in Azure Cosmos DB in tabelle e visualizzazioni in base alle proprie esigenze di analisi dei dati e creazione di report. Gli schemi rinormalizzati non hanno effetto sui dati sottostanti e non costringono gli sviluppatori a conformarsi a essi. Permettono invece di sfruttare gli strumenti conformi a ODBC per accedere ai dati. Ora il database di Azure Cosmos DB non sarà apprezzato solo dal team di sviluppo, ma anche dagli analisti dei dati.

Attività iniziali con il driver ODBC.

## <a id="install"></a>Passaggio 1: Installare il driver ODBC di Azure Cosmos DB

1. Scaricare i driver per l'ambiente:

    | Programma di installazione | Sistemi operativi supportati| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) per Windows a 64 bit| Versioni a 64 bit di Windows 8.1 o successive, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) per Windows a 32 bit in Windows a 64 bit| Versioni a 64 bit di Windows 8.1 o successiva, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 e Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) per Windows a 32 bit|Versioni a 32 bit di Windows 8.1 o successiva, Windows 8, Windows 7, Windows XP e Windows Vista.|

    Eseguire il file msi in locale, avviando così l'**installazione guidata del driver ODBC di Microsoft Azure Cosmos DB**. 

1. Completare l'installazione guidata usando l'input predefinito per installare il driver ODBC.

1. Aprire l'app **Amministrazione origine dati ODBC** sul computer. È possibile eseguire questa operazione digitando **Origine dati ODBC** nella casella di ricerca di Windows. 
    È possibile verificare la corretta installazione del driver facendo clic sulla scheda **Driver** e assicurandosi che sia elencato **Microsoft Azure Cosmos DB ODBC Driver**.

    ![Amministrazione origine dati ODBC di Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Passaggio 2: Connettersi al database Azure Cosmos DB

1. Dopo aver [installato il driver ODBC di Azure Cosmos DB](#install), nella finestra **Amministrazione origine dati ODBC** fare clic su **Aggiungi**. È possibile creare un DSN utente o di sistema. In questo esempio viene creato un DSN utente.

1. Nella finestra **Crea origine dati** selezionare **Microsoft Azure Cosmos DB ODBC Driver** e fare clic su **Fine**.

1. Nella finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB) inserire le informazioni seguenti: 

    ![Finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB)](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nome origine dati**: nome descrittivo per il DSN di ODBC. Questo nome è univoco per l'account Azure Cosmos DB. Si consiglia quindi di assegnarne uno appropriato se si hanno più account.
    - **Descrizione**: breve descrizione dell'origine dati.
    - **Host**: URI dell'account Azure Cosmos DB. È possibile recuperarlo dalla pagina Chiavi di Azure Cosmos DB del portale di Azure, come illustrato nello screenshot seguente. 
    - **Chiave di accesso**: chiave di lettura/scrittura o di sola lettura primaria o secondaria indicata nella pagina Chiavi di Azure Cosmos DB del portale di Azure, come illustrato nello screenshot seguente. È consigliabile usare la chiave di sola lettura se il DSN viene usato per l'elaborazione e la creazione di report su dati di sola lettura.
    ![Pagina Chiavi di Azure Cosmos DB](./media/odbc-driver/odbc-driver-keys.png)
    - **Encrypt Access Key for** (Crittografa chiave di accesso per): selezionare la scelta migliore in base agli utenti del computer. 
    
1. Fare clic sul pulsante **Test** per assicurarsi che sia possibile connettersi all'account di Azure Cosmos DB. 

1. Fare clic su **Opzioni avanzate** e impostare i valori seguenti:
    - **Query Consistency** (Coerenza query): Selezionare il [livello di coerenza](consistency-levels.md) per le operazioni. Il valore predefinito è Session.
    - **Number of Retries** (Numero di tentativi): immettere il numero di tentativi di un'operazione se la richiesta iniziale non viene completata a causa della limitazione di velocità del servizio.
    - **File di schema**: si dispone di una serie di opzioni.
        - Per impostazione predefinita, lasciando vuota questa voce, il driver analizzerà la prima pagina di dati di tutte le raccolte per individuare lo schema di ciascuna raccolta. L'operazione è definita Mapping raccolta. Senza un file di schema definito, il driver deve eseguire l'analisi per ogni sessione di driver. Possono quindi verificarsi tempi di avvio più elevati per un'applicazione che usa DSN. È consigliabile associare sempre un file di schema per un DSN.
        - Se si dispone già di un file di schema, probabilmente creato con l'Editor schema, è possibile fare clic su **Sfoglia**, selezionare il file, fare clic su **Salva** e quindi su **OK**.
        - Per creare un nuovo schema, fare clic su **OK** e quindi su **Editor schema** nella finestra principale. Andare quindi alle informazioni di Editor schema. Dopo la creazione del nuovo file di schema, ricordarsi di tornare alla finestra **Opzioni avanzate** per includere il file di schema appena creato.

1. Una volta completato e chiusa la finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB), il nuovo DSN utente verrà aggiunto alla scheda User DSN (DSN utente).

    ![Nuovo DSN ODBC di Azure Cosmos DB nella scheda User DSN (DSN utente)](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Passaggio 3: Creare una definizione dello schema usando il metodo di mapping raccolta

Esistono due tipi di metodi di campionamento da utilizzare: il **mapping raccolta** o i **delimitatori di tabella**. Una sessione di campionamento può usare entrambi i metodi, ma ogni raccolta può usarne solo uno specifico. La procedura seguente crea uno schema per i dati in una o più raccolte mediante il metodo di mapping raccolta. Questo metodo di campionamento recupera i dati nella pagina di una raccolta per determinare la struttura degli stessi e trasforma la raccolta in una tabella in ODBC. Questo metodo di campionamento è rapido ed efficiente quando i dati in una raccolta sono omogenei. Se una raccolta contiene tipi eterogenei di dati, è consigliabile usare il metodo di [mapping con delimitatori di tabelle](#table-mapping) in quanto consente un campionamento più efficiente per determinare le strutture di dati nella raccolta. 

1. Dopo aver completato i passaggi 1-4 in [Connettersi al database Azure Cosmos DB](#connect), fare clic su **Editor schemi** nella finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB).

    ![Pulsante Editor schemi nella finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB)](./media/odbc-driver/odbc-driver-schema-editor.png)
1. Nella finestra **Editor schema**, fare clic su **Crea nuovo**.
    La finestra **Generate Schema** (Genera schema) mostra tutte le raccolte nell'account di Azure Cosmos DB. 

1. Selezionare uno o più raccolte da campionare e fare clic su **Campiona**. 

1. Nella scheda **Visualizzazione progettazione** vengono rappresentati il database, lo schema e la tabella. Nella visualizzazione tabella, l'analisi mostra il set di proprietà associate ai nomi delle colonne: SQL Name, Source Name e così via.
    Per ogni colonna è possibile modificare i valori nome SQL della colonna, tipo SQL, lunghezza SQL (se applicabile) e le proprietà Scale (se applicabile), Precision (se applicabile) e Nullable.
    - È possibile impostare **Nascondi colonna** su **true** se si desidera escludere la colonna dai risultati delle query. Le colonne contrassegnate con Nascondi colonna = true non vengono incluse nei risultati di selezione e proiezione, anche se fanno comunque parte dello schema. È ad esempio possibile nascondere tutte le proprietà di sistema richieste di Azure Cosmos DB che iniziano con "_".
    - La colonna **id** è l'unico campo che non è possibile nascondere, poiché è usato come chiave primaria nello schema normalizzato. 

1. Dopo aver completato la definizione dello schema, fare clic su **File** | **Salva**, passare alla directory in cui salvare lo schema e fare clic su **Salva**.

1. Per usare questo schema con un DSN, aprire il **finestra Azure Cosmos DB ODBC Driver DSN Setup** (tramite l'amministrazione origine dati ODBC), fare clic su **opzioni avanzate**e quindi il **FilediSchema** finestra, passare allo schema salvato. Salvare un file di schema in un DSN esistente modifica l'ambito della connessione DSN includendo i dati e le strutture definiti dallo schema.

## <a id="table-mapping"></a>Passaggio 4: Creare una definizione dello schema usando il metodo di mapping con delimitatori di tabella

Esistono due tipi di metodi di campionamento da utilizzare: il **mapping raccolta** o i **delimitatori di tabella**. Una sessione di campionamento può usare entrambi i metodi, ma ogni raccolta può usarne solo uno specifico. 

La procedura seguente crea uno schema per i dati in una o più raccolte mediante il metodo di **delimitatori di tabella**. È consigliabile usare questo metodo di campionamento quando le raccolte contengono tipi eterogenei di dati. È possibile usare questo metodo per definire come ambito di campionamento un set di attributi e i valori corrispondenti. Ad esempio, se un documento contiene una proprietà "Tipo", è possibile definire il campionamento per i valori di questa proprietà. Il risultato finale del campionamento sarà un set di tabelle per ciascun valore della proprietà Tipo specificata. Ad esempio, Tipo = Car produrrà una tabella Car mentre Tipo = Plane produrrà una tabella Plane.

1. Dopo aver completato i passaggi 1-4 in [Connettersi al database Azure Cosmos DB](#connect), fare clic su **Editor schemi** nella finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB).

1. Nella finestra **Editor schema**, fare clic su **Crea nuovo**.
    La finestra **Generate Schema** (Genera schema) mostra tutte le raccolte nell'account di Azure Cosmos DB. 

1. Selezionare una raccolta nella scheda **Visualizzazione di esempio**. Nella colonna **Mapping Definition** (Definizione mapping) della raccolta fare clic su **Modifica**. A quel punto, nella finestra **Mapping Definition** (Definizione mapping) selezionare il metodo **Table Delimiters** (Delimitatori di tabella). Eseguire quindi le operazioni seguenti:

    a. Nel riquadro **Attributi** digitare il nome di una proprietà delimitatore. Si tratta di una proprietà nel documento per cui si desidera eseguire il campionamento. Digitare ad esempio City e premere INVIO. 

    b. Se si desidera definire limitare il campionamento a determinati valori per l'attributo immesso, selezionare l'attributo nel riquadro di selezione, immettere un valore nel riquadro **Valore**, ad esempio Seattle, e premere INVIO. È possibile continuare ad aggiungere più valori per gli attributi. Assicurarsi che sia selezionato l'attributo corretto quando si immettono i valori.

    Ad esempio, se si include un valore City per gli **Attributi** e si desidera limitare la tabella in modo che includa solamente le righe con un valore City di New York e Dubai, si inserirà City nel riquadro Attributi e New York e Dubai nel riquadro **Valori**.

1. Fare clic su **OK**. 

1. Dopo aver completato le definizioni di mapping per le raccolte da campionare, nella finestra **Editor schemi** fare clic su **Campiona**.
     Per ogni colonna è possibile modificare i valori nome SQL della colonna, tipo SQL, lunghezza SQL (se applicabile) e le proprietà Scale (se applicabile), Precision (se applicabile) e Nullable.
    - È possibile impostare **Nascondi colonna** su **true** se si desidera escludere la colonna dai risultati delle query. Le colonne contrassegnate con Nascondi colonna = true non vengono incluse nei risultati di selezione e proiezione, anche se fanno comunque parte dello schema. È ad esempio possibile nascondere tutte le proprietà di sistema richieste di Azure Cosmos DB che iniziano con `_`.
    - La colonna **id** è l'unico campo che non è possibile nascondere, poiché è usato come chiave primaria nello schema normalizzato. 

1. Dopo aver completato la definizione dello schema, fare clic su **File** | **Salva**, passare alla directory in cui salvare lo schema e fare clic su **Salva**.

1. Nella finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB) fare clic su **Opzioni avanzate**. Quindi, nel riquadro **File di schema**, passare al file di schema salvato e fare clic su **OK**. Fare nuovamente clic su **OK** e salvare il DSN. In questo modo lo schema creato viene salvato nel DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Facoltativo) Configurare la connessione al server collegato

È possibile eseguire una query in Azure Cosmos DB da SQL Server Management Studio (SSMS) configurando una connessione del server collegato.

1. Creare un'origine dati del sistema come descritto nel [Passaggio 2](#connect), denominata, ad esempio `SDS Name`.

1. [Installare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e connettersi al server. 

1. Nell'editor di query di SSMS creare un oggetto server collegato `DEMOCOSMOS` per l'origine dati con i comandi seguenti. Sostituire `DEMOCOSMOS` con il nome del server collegato e `SDS Name` con il nome dell'origine dati del sistema.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Per visualizzare il nuovo nome del server collegato, aggiornare l'elenco di server collegati.

![Server collegati in SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Eseguire una query nel database collegato

Per eseguire una query nel database collegato, immettere una query SSMS. In questo esempio la query seleziona dalla tabella della raccolta denominata `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Eseguire la query. Il risultato sarà simile al seguente:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Il server collegato di Cosmos DB non supporta la denominazione in quattro parti. Viene restituito un errore con un messaggio simile al seguente:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Facoltativo) Creazione di visualizzazioni
È possibile definire e creare visualizzazioni come parte del processo di campionamento. Le visualizzazioni equivalgono alle visualizzazioni SQL. Sono di sola lettura e corrispondono alle selezioni e proiezioni definite nella query SQL di Azure Cosmos DB. 

Per creare una visualizzazione dei dati, nella finestra **Editor schemi** passare alla colonna **Definizioni visualizzazioni** e fare clic su **Aggiungi** nella riga delle raccolte da campionare. 
    ![Creare una vista dei dati](./media/odbc-driver/odbc-driver-create-view.png)


Quindi eseguire le operazioni seguenti nella finestra **Definizioni visualizzazioni**:

1. Fare clic su **Nuova**, immettere un nome per la visualizzazione, ad esempio EmployeesfromSeattleView, e fare clic su **OK**.

1. Nella finestra **Modifica visualizzazione** inserire una query di Azure Cosmos DB. Deve essere una [query SQL di Azure Cosmos DB](how-to-sql-query.md), ad esempio `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, quindi fare clic su **OK**.

    ![Aggiungere query durante la creazione di una vista](./media/odbc-driver/odbc-driver-create-view-2.png)


È possibile creare un numero illimitato di visualizzazioni. Dopo aver definito le visualizzazioni, è possibile campionare i dati. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Passaggio 5: Visualizzare i dati in strumenti di BI come Power BI Desktop

È possibile usare il nuovo DSN per la connessione ad Azure Cosmos DB con qualsiasi strumento conforme a ODBC. Questo passaggio illustra semplicemente come connettersi a Power BI Desktop e creare una vista di Power BI.

1. Aprire Power BI Desktop.

1. Fare clic su **Recupera dati**.

    ![Ottenere i dati in Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Nella finestra **Recupera dati** fare clic su **Altri** | **ODBC** | **Connetti**.

    ![Scegliere origine dati ODBC in Power BI > Recupera dati](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. Nella finestra **Da ODBC**, selezionare il nome dell'origine dati creato, quindi fare clic su **OK**. È possibile lasciare vuote le voci **Opzioni avanzate**.

    ![Scegliere il nome di origine dati (DSN) in Power BI > Recupera dati](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. Nella finestra **Accedere a un'origine dati tramite un driver ODBC**, selezionare **Predefinito o personalizzato**, quindi fare clic su **Connetti**. Non è necessario includere le **proprietà stringa di connessione delle credenziali**.

1. Nella finestra **Strumento di spostamento**, espandere il database e lo schema nel riquadro a sinistra, quindi selezionare la tabella. Il riquadro dei risultati include i dati che usano lo schema creato.

    ![Selezionare una tabella in Power BI > Recupera dati](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Per visualizzare i dati in Power BI Desktop, selezionare la casella davanti al nome di tabella e fare clic su **Carica**.

1. In Power BI Desktop selezionare la scheda Dati a sinistra ![Scheda dati in Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) per confermare l'importazione dei dati.

1. È ora possibile creare oggetti visivi usando Power BI e facendo clic sulla scheda Report ![Scheda Report in Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), quindi su **Nuovo oggetto visivo** e personalizzando il riquadro. Per altre informazioni sulla creazione di visualizzazioni in Power BI Desktop, vedere [Tipi di visualizzazioni in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>risoluzione dei problemi

Se viene visualizzato il seguente errore, verificare che i valori **Host** e **Chiave di accesso** copiati nel portale di Azure nel [passaggio 2](#connect) siano corretti, quindi riprovare. Usare i pulsanti di copia a destra dei valori **Host** e **Chiave di accesso** nel portale di Azure per evitare errori.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB](introduction.md).
