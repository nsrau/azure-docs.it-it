---
title: Connettersi ad Azure Cosmos DB con gli strumenti di analisi BI | Microsoft Docs
description: Informazioni su come usare il driver ODBC di Azure Cosmos DB per creare tabelle e viste in modo che i dati normalizzati possano essere visualizzati in BI e nel software di analisi dei dati.
keywords: ODBC, driver ODBC
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 2df792c00b7a789dbefa64bfe0245f1ad73c3faa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Connettersi ad Azure Cosmos DB usando gli strumenti di analisi BI con il driver ODBC

Il driver ODBC di Azure Cosmos DB consente di connettersi ad Azure Cosmos DB usando gli strumenti di analisi BI, ad esempio SQL Server Integration Services, Power BI Desktop e Tableau, in modo da analizzare e creare visualizzazioni dei dati di Azure Cosmos DB in tali soluzioni.

Il driver ODBC di Azure Cosmos DB è conforme a ODBC 3.8 e supporta la sintassi ANSI SQL-92. Il driver offre funzionalità avanzate che consentono di rinormalizzare i dati in Azure Cosmos DB. Usando il driver è possibile rappresentare i dati in Azure Cosmos DB come tabelle e viste. Il driver consente di eseguire operazioni SQL su tabelle e viste, ad esempio raggruppamenti in base alle query, inserimenti, aggiornamenti ed eliminazioni.

## <a name="why-do-i-need-to-normalize-my-data"></a>Perché è necessario normalizzare i dati?
Azure Cosmos DB è un database senza schema, che consente quindi un rapido sviluppo di app permettendo a queste ultime di eseguire in tempo reale l'iterazione del modello di dati senza limitarle a uno schema rigoroso. Un singolo database di Azure Cosmos DB può contenere documenti JSON con varie strutture. Questa novità è ideale per lo sviluppo rapido delle applicazioni, ma quando si desidera analizzare e creare report dei dati usando gli strumenti di analisi e BI, è spesso necessario adattare e conformare tali dati a uno schema specifico.

È qui che entra in gioco il driver ODBC. Tramite questo driver è ora possibile organizzare i dati rinormalizzati in Azure Cosmos DB in tabelle e visualizzazione in base alle proprie esigenze di analisi e creazione di report. Gli schemi rinormalizzati non hanno effetto sui dati sottostanti e non costringono gli sviluppatori a conformarli a essi, ma permettono semplicemente di sfruttare gli strumenti conformi a ODBC per accedere ai dati. A questo punto il database di Azure Cosmos DB non sarà apprezzato solo dal team di sviluppo, ma anche dagli analisti dei dati.

Verranno illustrate ora le procedure iniziali riguardanti il driver ODBC.

## <a id="install"></a>Passaggio 1: Installare il driver ODBC di Azure Cosmos DB

1. Scaricare i driver per l'ambiente:

    * [Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) per Windows a 64 bit
    * [Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64) per Windows a 32 bit in Windows a 64 bit
    * [Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) per Windows a 32 bit

    Eseguire il file msi in locale, avviando così l'**installazione guidata del driver ODBC di Microsoft Azure Cosmos DB**. 
2. Completare l'installazione guidata usando l'input predefinito per installare il driver ODBC.
3. Aprire l'app **Amministrazione origine dati ODBC** nel computer. Per farlo è possibile digitare **ODBC Data sources** nella casella di ricerca di Windows. 
    È possibile verificare la corretta installazione del driver facendo clic sulla scheda **Driver** e assicurandosi che sia elencato **Microsoft Azure Cosmos DB ODBC Driver**.

    ![Amministrazione origine dati ODBC di Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Passaggio 2: Connettersi al database Azure Cosmos DB

1. Dopo aver [installato il driver ODBC di Azure Cosmos DB](#install), nella finestra **Amministrazione origine dati ODBC** fare clic su **Aggiungi**. È possibile creare un DSN utente o di sistema. In questo esempio viene creato un DSN utente.
2. Nella finestra **Crea origine dati** selezionare **Microsoft Azure Cosmos DB ODBC Driver** e fare clic su **Fine**.
3. Nella finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB) inserire le informazioni seguenti: 

    ![Finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB)](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nome origine dati**: nome descrittivo per il DSN di ODBC. Questo nome è univoco per l'account Azure Cosmos DB. Si consiglia quindi di assegnarne uno appropriato se si hanno più account.
    - **Descrizione**: breve descrizione dell'origine dati.
    - **Host**: URI dell'account Azure Cosmos DB. È possibile recuperarlo dal pannello Chiavi di Azure Cosmos DB del portale di Azure, come illustrato nello screenshot seguente. 
    - **Chiave di accesso**: la chiave di lettura/scrittura o di sola lettura primaria e secondaria indicata nel pannello Chiavi di Azure Cosmos DB del portale di Azure, come illustrato nello screenshot seguente. È consigliabile usare la chiave di sola lettura se il DSN viene usato per l'elaborazione e la creazione di report su dati di sola lettura.
    ![Pannello Chiavi di Azure Cosmos DB](./media/odbc-driver/odbc-driver-keys.png)
    - **Encrypt Access Key for** (Crittografa chiave di accesso per): selezionare la scelta migliore in base agli utenti del computer. 
4. Fare clic sul pulsante **Test** per assicurarsi che sia possibile connettersi all'account di Azure Cosmos DB. 
5. Fare clic su **Opzioni avanzate** e impostare i valori seguenti:
    - **Coerenza delle query**: selezionare il [livello di coerenza](consistency-levels.md) per le operazioni. Il valore predefinito è Session.
    - **Numero di tentativi**: immettere il numero di tentativi di un'operazione se la richiesta iniziale non viene completata a causa della limitazione del servizio.
    - **File di schema**: si dispone di una serie di opzioni.
        - Per impostazione predefinita, lasciando vuota questa voce, il driver analizzerà i dati nella prima pagina per tutte le raccolte per individuare lo schema di ciascuna raccolta. L'operazione è definita Mapping raccolta. Senza un file di schema definito, il driver deve eseguire l'analisi per ogni sessione di driver, il che potrebbe comportare in un tempo di avvio più elevato per un'applicazione che usa DSN. È consigliabile associare sempre un file di schema per un DSN.
        - Se si dispone già di un file di schema, probabilmente creato con l'[Editor schema](#schema-editor), è possibile fare clic su **Sfoglia**, selezionare il file, fare clic su **Salva** e quindi su **OK**.
        - Per creare un nuovo schema, fare clic su **OK** e quindi su **Editor schema** nella finestra principale. Procedere quindi alle informazioni di [Editor schema](#schema-editor). Dopo la creazione, assicurarsi di tornare alla finestra **Opzioni avanzate** per includere il file di schema appena creato.

6. Una volta completato e chiusa la finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB), il nuovo DSN utente verrà aggiunto alla scheda User DSN (DSN utente).

    ![Nuovo DSN ODBC di Azure Cosmos DB nella scheda User DSN (DSN utente)](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Passaggio 3: Creare una definizione dello schema usando il metodo di mapping raccolta

Esistono due tipi di metodi di campionamento da utilizzare: il **mapping raccolta** o i **delimitatori di tabella**. Una sessione di campionamento può usare entrambi i metodi, ma ogni raccolta può usarne solo uno specifico. La procedura seguente crea uno schema per i dati in una o più raccolte mediante il metodo di mapping raccolta. Questo metodo di campionamento recupera i dati nella pagina di una raccolta per determinare la struttura degli stessi e trasforma la raccolta in una tabella in ODBC. Questo metodo di campionamento è rapido ed efficiente quando i dati in una raccolta sono omogenei. Se una raccolta contiene tipi eterogenei di dati, è consigliabile usare il metodo di [mapping con delimitatori di tabelle](#table-mapping) in quanto consente un campionamento più efficiente per determinare le strutture di dati nella raccolta. 

1. Dopo aver completato i passaggi 1-4 in [Connettersi al database Azure Cosmos DB](#connect), fare clic su **Editor schemi** nella finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB).

    ![Pulsante Editor schemi nella finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB)](./media/odbc-driver/odbc-driver-schema-editor.png)
2. Nella finestra **Editor schema**, fare clic su **Crea nuovo**.
    La finestra **Generate Schema** (Genera schema) mostra tutte le raccolte nell'account di Azure Cosmos DB. 
3. Selezionare uno o più raccolte da campionare e fare clic su **Campiona**. 
4. Nella scheda **Visualizzazione progettazione** vengono rappresentati il database, lo schema e la tabella. Nella visualizzazione tabella, l'analisi mostra il set di proprietà associate ai nomi delle colonne: SQL Name, Source Name e così via.
    Per ogni colonna è possibile modificare i valori nome SQL della colonna, tipo SQL, lunghezza SQL (se applicabile) e le proprietà Scale (se applicabile), Precision (se applicabile) e Nullable.
    - È possibile impostare **Nascondi colonna** su **true** se si desidera escludere la colonna dai risultati delle query. Le colonne contrassegnate con Nascondi colonna = true non vengono incluse nei risultati di selezione e proiezione, anche se fanno comunque parte dello schema. È ad esempio possibile nascondere tutte le proprietà di sistema richieste di Azure Cosmos DB che iniziano con "_".
    - La colonna **id** è l'unico campo che non è possibile nascondere, poiché è usato come chiave primaria nello schema normalizzato. 
5. Dopo aver completato la definizione dello schema, fare clic su **File** | **Salva**, passare alla directory in cui salvare lo schema e fare clic su **Salva**.

    Se in futuro si vuole usare questo schema con un DSN, aprire la finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB) tramite Amministrazione origine dati ODBC, fare clic su Opzioni avanzate e nel riquadro File di schema passare allo schema salvato. Salvare un file di schema in un DSN esistente modifica l'ambito della connessione DSN includendo i dati e le strutture definiti dallo schema.

## <a id="table-mapping"></a>Passaggio 4: Creare una definizione dello schema usando il metodo di delimitatori di tabella

Esistono due tipi di metodi di campionamento da utilizzare: il **mapping raccolta** o i **delimitatori di tabella**. Una sessione di campionamento può usare entrambi i metodi, ma ogni raccolta può usarne solo uno specifico. 

La procedura seguente crea uno schema per i dati in una o più raccolte mediante il metodo di **delimitatori di tabella**. È consigliabile usare questo metodo di campionamento quando le raccolte contengono tipi eterogenei di dati. È possibile usare questo metodo per definire come ambito di campionamento un set di attributi e i valori corrispondenti. Ad esempio, se un documento contiene una proprietà "Tipo", è possibile definire il campionamento per i valori di questa proprietà. Il risultato finale del campionamento sarà un set di tabelle per ciascun valore della proprietà Tipo specificata. Ad esempio, Tipo = Car produrrà una tabella Car mentre Tipo = Plane produrrà una tabella Plane.

1. Dopo aver completato i passaggi 1-4 in [Connettersi al database Azure Cosmos DB](#connect), fare clic su **Editor schemi** nella finestra Azure Cosmos DB ODBC Driver DSN Setup (Configurazione DSN driver ODBC di Azure Cosmos DB).
2. Nella finestra **Editor schema**, fare clic su **Crea nuovo**.
    La finestra **Generate Schema** (Genera schema) mostra tutte le raccolte nell'account di Azure Cosmos DB. 
3. Selezionare una raccolta nella scheda **Visualizzazione di esempio**. Nella colonna **Mapping Definition** (Definizione mapping) della raccolta fare clic su **Modifica**. A quel punto, nella finestra **Mapping Definition** (Definizione mapping) selezionare il metodo **Table Delimiters** (Delimitatori di tabella). Eseguire quindi le operazioni seguenti:

    a. Nel riquadro **Attributi** digitare il nome di una proprietà delimitatore. Si tratta di una proprietà nel documento per cui si desidera eseguire il campionamento. Digitare ad esempio City e premere INVIO. 

    b. Se si desidera definire limitare il campionamento a determinati valori per l'attributo appena immesso, selezionare l'attributo nel riquadro di selezione, quindi immettere un valore nel riquadro **Valore**, ad esempio Seattle, e premere INVIO. È possibile continuare ad aggiungere più valori per gli attributi. Assicurarsi che sia selezionato l'attributo corretto quando si immettono i valori.

    Ad esempio, se si include un valore City per gli **Attributi** e si desidera limitare la tabella in modo che includa solamente le righe con un valore City di New York e Dubai, si inserirà City nel riquadro Attributi e New York e Dubai nel riquadro **Valori**.
4. Fare clic su **OK**. 
5. Dopo aver completato le definizioni di mapping per le raccolte da campionare, nella finestra **Editor schemi** fare clic su **Campiona**.
     Per ogni colonna è possibile modificare i valori nome SQL della colonna, tipo SQL, lunghezza SQL (se applicabile) e le proprietà Scale (se applicabile), Precision (se applicabile) e Nullable.
    - È possibile impostare **Nascondi colonna** su **true** se si desidera escludere la colonna dai risultati delle query. Le colonne contrassegnate con Nascondi colonna = true non vengono incluse nei risultati di selezione e proiezione, anche se fanno comunque parte dello schema. È ad esempio possibile nascondere tutte le proprietà di sistema richieste di Azure Cosmos DB che iniziano con "_".
    - La colonna **id** è l'unico campo che non è possibile nascondere, poiché è usato come chiave primaria nello schema normalizzato. 
6. Dopo aver completato la definizione dello schema, fare clic su **File** | **Salva**, passare alla directory in cui salvare lo schema e fare clic su **Salva**.
7. Nella finestra **Azure Cosmos DB ODBC Driver DSN Setup** (Configurazione DSN driver ODBC di Azure Cosmos DB) fare clic su **Opzioni avanzate**. Quindi, nel riquadro **File di schema**, passare al file di schema salvato e fare clic su **OK**. Fare nuovamente clic su **OK** e salvare il DSN. In questo modo lo schema creato viene salvato nel DSN. 

## <a name="optional-creating-views"></a>(Facoltativo) Creazione di visualizzazioni
È possibile definire e creare visualizzazioni come parte del processo di campionamento. Le visualizzazioni equivalgono alle visualizzazioni SQL. Sono di sola lettura e corrispondono alle selezioni e proiezioni definite in SQL di Azure Cosmos DB. 

Per creare una visualizzazione dei dati, nella finestra **Editor schemi** passare alla colonna **Definizioni visualizzazioni** e fare clic su **Aggiungi** nella riga delle raccolte da campionare. Quindi eseguire le operazioni seguenti nella finestra **Definizioni visualizzazioni**:
1. Fare clic su **Nuova**, immettere un nome per la visualizzazione, ad esempio EmployeesfromSeattleView, e fare clic su **OK**.
2. Nella finestra **Modifica visualizzazione** inserire una query di Azure Cosmos DB. Deve trattarsi di una query SQL di Azure Cosmos DB, ad esempio `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`. Fare clic su **OK**.

È possibile creare un numero illimitato di visualizzazioni. Dopo aver definito le visualizzazioni, è possibile campionare i dati. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Passaggio 5: Visualizzare i dati in strumenti di BI quali Power BI Desktop

È possibile usare il nuovo DSN per connettere DocumentADB con qualsiasi strumento conforme a ODBC. Questo passaggio illustra semplicemente come connettersi a Power BI Desktop e creare una visualizzazione di Power BI.

1. Aprire Power BI Desktop.
2. Fare clic su **Recupera dati**.
3. Nella finestra **Recupera dati** fare clic su **Altri** | **ODBC** | **Connetti**.
4. Nella finestra **Da ODBC**, selezionare il nome dell'origine dati creato, quindi fare clic su **OK**. È possibile lasciare vuote le voci **Opzioni avanzate**.
5. Nella finestra **Accedere a un'origine dati tramite un driver ODBC**, selezionare **Predefinito o personalizzato**, quindi fare clic su **Connetti**. Non è necessario includere le **proprietà stringa di connessione delle credenziali**.
6. Nella finestra **Strumento di spostamento**, espandere il database e lo schema nel riquadro a sinistra, quindi selezionare la tabella. Il riquadro dei risultati include i dati che usano lo schema creato.
7. Per visualizzare i dati in Power BI Desktop, selezionare la casella davanti al nome di tabella e fare clic su **Carica**.
8. In Power BI Desktop selezionare la scheda Dati a sinistra ![Scheda dati in Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) per confermare l'importazione dei dati.
9. È ora possibile creare oggetti visivi usando Power BI e facendo clic sulla scheda Report ![Scheda Report in Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), quindi su **Nuovo oggetto visivo** e personalizzando il riquadro. Per altre informazioni sulla creazione di visualizzazioni in Power BI Desktop, vedere [Tipi di visualizzazioni in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se viene visualizzato il seguente errore, verificare che i valori **Host** e **Chiave di accesso** copiati nel portale di Azure nel [passaggio 2](#connect) siano corretti, quindi riprovare. Usare i pulsanti di copia a destra dei valori **Host** e **Chiave di accesso** nel portale di Azure per evitare errori.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB](introduction.md).
