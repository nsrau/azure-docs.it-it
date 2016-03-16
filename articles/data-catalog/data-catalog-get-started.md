<properties
   pageTitle="Introduzione al Catalogo dati di Azure con catalogo dati"
   description="Esercitazione end-to-end degli scenari e delle funzionalità del Catalogo dati di Azure."
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/03/2016"
   ms.author="derrickv"/>

# Introduzione al Catalogo dati di Azure

Questo articolo contiene un'esercitazione end-to-end degli scenari e delle funzionalità dell'anteprima pubblica di **Catalogo dati di Azure**. Quando si effettua l'iscrizione per l'anteprima, seguire questi passaggi per creare un catalogo di dati e per registrare, annotare e individuare le origini dati.

## Prerequisiti per l'esercitazione

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Sottoscrizione di Azure**: se non si dispone di una sottoscrizione, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Azure Active Directory** - Catalogo dati di Azure usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per la gestione delle identità e degli accessi.
- **Origini dati**: il Catalogo dati di Azure include funzionalità per l'individuazione delle origini dati. Questa esercitazione usa il database di esempio Adventure Works, ma è possibile usare qualsiasi origine dati supportata se si preferisce lavorare con dati familiari e più rilevanti per il proprio ruolo. Per un elenco di origini dati supportate, vedere [Origini dati supportate](data-catalog-dsr.md).

Per prima cosa, si installa il database di esempio Adventure Works.

## Esercizio 1: installare il database di esempio Adventure Works

In questo esercizio viene installato il database di esempio Adventure Works per il motore di database di SQL Server che viene usato negli esercizi successivi.

### Installare il database OLTP Adventure Works 2014

Il database Adventure Works supporta gli scenari di elaborazione delle transazioni online standard per un produttore di biciclette fittizio (Adventure Works Cycles) che include i settori Prodotti, Vendite e Acquisti. In questa esercitazione vengono registrate le informazioni sui prodotti nel **Catalogo dati di Azure**.

Ecco come installare il database di esempio Adventure Works.

Per installare il database di esempio Adventure Works, è possibile ripristinare il backup AdventureWorks2014 che si trova in [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) in CodePlex. Per ripristinare il database è possibile eseguire uno script T-SQL in SQL Server Management Studio.

**Installare il database di esempio Adventure Works con uno script T-SQL**

1.	Creare una cartella denominata C:\\DataCatalog\_GetStarted. Se si usa un altro nome di cartella, assicurarsi di modificare il percorso dello script T-SQL riportato di seguito.
2.	Scaricare [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661).
3.	Estrarre Adventure Works 2014 Full Database Backup.zip in C:\\DataCatalog\_GetStarted. Lo script seguente presuppone che il file di backup si trovi in C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak.
4.	Da **SQL Server Management Studio** fare clic su **Nuova query** nella barra degli strumenti **Standard**.
5.	Eseguire il codice T-SQL seguente nella finestra della query.

**Eseguire questo script per installare il database Adventure Works 2014**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Come alternativa allo script T-SQL, è possibile ripristinare il database usando SQL Server Management Studio. Vedere [Ripristino di un backup del database (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

In questo esercizio è stato installato il database di esempio Adventure Works che verrà usato negli esercizi rimanenti. Nel prossimo esercizio verranno fornite informazioni su come registrare le origini dati del **Catalogo dati di Azure** dalle tabelle a un database di esempio Adventure Works.

## Esercizio 2: registrazione delle origini dati

In questo esercizio viene usato lo strumento di registrazione del **Catalogo dati di Azure** per registrare le origini dati di Adventure Works con il catalogo. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e dall’asset che lo contiene e di copia dei metadati nel catalogo. Le origini dati e i relativi dati rimangono dove sono, ma i metadati vengono usati dal catalogo per renderli più facilmente individuabili e comprensibili.

### Di seguito viene illustrato come registrare un'origine dati

1.	Passare a https://azure.microsoft.com/services/data-catalog e fare clic su **Introduzione**.
2.	Accedere al portale del **Catalogo dati di Azure** e fare clic su **Pubblica dati**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Fare clic su **Avvia applicazione**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Nella pagina **iniziale** fare clic su **Accedi** e immettere le credenziali.
5. Nella pagina **Catalogo dati di Microsoft Azure** fare doppio clic su **SQL Server** oppure scegliere **SQL Server** e **Avanti**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Immettere le proprietà di connessione di SQL Server per AdventureWorks2014 (vedere l'esempio seguente) e fare clic su **CONNETTI**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	Nella pagina successiva è possibile registrare i metadati dell'origine dati. In questo esempio vengono registrati gli oggetti **Produzione/Prodotto** dallo spazio dei nomi di produzione di AdventureWorks. Ecco come fare:

    a. Nell'albero **Gerarchia server** fare clic su **Produzione**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. CTRL + clic su Product, ProductCategory, ProductDescription e ProductPhoto.

    c. Fare clic sulla freccia per lo spostamento dell’elemento selezionato (**>**). In tal modo si spostano tutti gli oggetti Product selezionati nell'elenco **Oggetti da registrare**.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. In **Aggiungi tag** immettere la descrizione e la foto. Verrà aggiunto il tag di ricerca per questi asset di dati. I tag sono un modo eccezionale per consentire agli utenti di trovare un'origine dati registrata.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e. **Facoltativo**: è possibile **includere un'anteprima** e **aggiungere un esperto di origine dati**.

    f. Fare clic su **REGISTRA**. Il **Catalogo dati di Azure** registra gli oggetti selezionati. In questo esercizio, vengono registrati gli oggetti selezionati di Adventure Works.

    g. Per visualizzare gli oggetti origine dati registrati, fare clic su **Visualizza portale**. Nel portale del **Catalogo dati di Azure** è possibile visualizzare gli oggetti dell'origine dati in **Riquadri** o in un **Elenco**.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

In questo esercizio sono stati registrati gli oggetti dal database di esempio Adventure Works in modo che possano essere facilmente individuati dagli utenti dell'organizzazione. Nel prossimo esercizio si apprende come individuare gli asset di dati registrati.

## Esercizio 3: individuazione di asset di dati registrati

In questo esercizio verrà usato il portale del **Catalogo dati di Azure** per individuare gli asset di dati registrati e visualizzare i relativi metadati. Il **Catalogo dati di Azure** fornisce diversi strumenti per l'individuazione degli asset di dati, tra cui la ricerca semplice per parola chiave, i filtri interattivi e una sintassi di ricerca avanzata per gli utenti "avanzati".

### Ecco come individuare gli asset di dati registrati

Il **Catalogo dati di Azure** ha una sintassi semplice ma efficace che consente di creare con facilità le query che restituiscono i dati necessari agli utenti. Per informazioni dettagliate sulla ricerca nel **Catalogo dati di Azure**, vedere il [riferimento alla sintassi di ricerca per il catalogo dati](https://msdn.microsoft.com/library/azure/mt267594.aspx).

Il **Catalogo dati di Azure** ha le seguenti opzioni di ricerca:

- Ricerca per parola chiave
- Filtro
- Ricerca avanzata

È inoltre possibile definire gli asset di dati da visualizzare. Il **Catalogo dati di Azure** ha le seguenti opzioni di visualizzazione:

- Visualizza proprietà
- Visualizza colonne
- Visualizza anteprima

Per questo esempio, si userà una ricerca per parola chiave. La ricerca del **Catalogo dati di Azure** dispone di diverse tecniche di query. In questo esempio viene usata una query di ricerca **raggruppamento**.

**Tecniche di query**

|Tecnica|Uso|Esempio
|---|---|---
|Ambito della proprietà|Restituire solo le origini dati in cui il termine di ricerca corrisponde alla proprietà specificata|name:product
|Operatori logici|Ampliare o restringere la ricerca usando le operazioni booleane, come descritto nella sezione relativa agli operatori booleani in questa pagina|finanza NON aziendale
|Raggruppamento con parentesi|Utilizzare le parentesi per raggruppare parti della query per ottenere l'isolamento logico, soprattutto in combinazione con gli operatori booleani|name:product AND (tags:illustration OR tags:photo)
|Operatori di confronto|Utilizzare confronti invece di uguaglianze per le proprietà che hanno dati di tipo numero e data|creationTime:>11/05/14

In questo esempio, eseguire una ricerca nel **raggruppamento** degli asset di dati dove nome è prodotto e tags è illustrazione o foto.

1. Passare a https://azure.microsoft.com/services/data-catalog, fare clic su **Introduzione**, quindi accedere al portale del **Catalogo dati di Azure**.
2. Nella casella **Cerca nel catalogo dati** immettere un **Raggruppamento** come: query: (tags:description OR tags:photo).
3. Fare clic sull'icona di ricerca o premere INVIO. Nel **Catalogo dati di Azure** vengono visualizzati gli asset di dati per questa query di ricerca.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

In questo esercizio è stato usato il portale del **Catalogo dati di Azure** per individuare e visualizzare le risorse di dati di Adventure Works registrate con il catalogo.

<a name="annotating"/>
## Esercizio 4: annotazione delle origini dati registrate

In questo esercizio si usa il portale del **Catalogo dati di Azure** per annotare gli asset di dati registrati in precedenza nel catalogo. Le annotazioni immesse andranno a integrare e migliorare i metadati strutturali estratti dall'origine dati durante la registrazione e sarà ancora più semplice individuare e comprendere gli asset di dati. Poiché ogni utente del **Catalogo dati** può fornire proprie annotazioni, è facile condividerle per tutti gli utenti con una prospettiva dei dati.

### Ecco come è possibile annotare gli asset di dati

1. Passare a https://azure.microsoft.com/services/data-catalog, fare clic su **Introduzione**, quindi accedere al portale del **Catalogo dati di Azure**.
2. Fare clic su **Individua**.
3. Scegliere uno o più **Asset di dati**. In questo esempio, scegliere **ProductPhoto** e immettere "Foto di prodotti per materiale di marketing".
4. Immettere una **Descrizione** che consenta ad altri di individuare e comprendere perché e come usare l'asset di dati selezionato. Ad esempio, immettere "Immagini di prodotto". È inoltre possibile aggiungere ulteriori tag e visualizzare le colonne.
5. Ora è possibile provare a usare la ricerca e il filtro per individuare gli asset di dati mediante i metadati descrittivi aggiunti al catalogo.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

In questo esercizio sono state aggiunte informazioni descrittive agli asset di dati registrati in modo che gli utenti del catalogo possano individuare l'origine dati usando termini comprensibili.

## Esercizio 5: crowdsourcing di metadati

In questo esercizio si collabora con un altro utente per aggiungere i metadati agli asset di dati nel catalogo. L'approccio di crowdsourcing relativo alle annotazioni del **Catalogo dati di Azure** consente agli utenti di aggiungere tag, descrizioni e altri metadati in modo che qualsiasi utente con una prospettiva su un asset di dati e sul suo utilizzo possa acquisire tale prospettiva e renderla disponibile agli altri utenti.

> [AZURE.NOTE] Se un altro utente non è disponibile per questa esercitazione, non è un problema. Qualsiasi utente che accede al catalogo dati può aggiungere la propria prospettiva quando sceglie di eseguire questa operazione. Questo approccio crowdsourcing per i metadati consente di incrementare nel tempo il contenuto del catalogo e la quantità di metadati del catalogo.

### Ecco come è possibile eseguire il crowdsourcing dei metadati relativi ad asset di dati

Chiedere a un collega di ripetere l’esercizio precedente [Annotazione delle origini dati registrate](#annotating). Dopo che i colleghi hanno aggiunto le descrizioni a un asset di dati, ad esempio ProductPhoto, verranno visualizzate più annotazioni.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

In questo esercizio sono state analizzate le funzionalità del **Catalogo dati di Azure** per i metadati di crowdsourcing, in cui qualsiasi utente del catalogo può annotare gli asset di dati individuati.

## Esercizio 6: Connessione alle origini dati

In questo esercizio verrà usato il portale del **Catalogo dati di Azure** per la connessione a un'origine dati con Microsoft Excel.

> [AZURE.NOTE] È importante ricordare che il **Catalogo dati di Azure** non consente agli utenti di accedere all'origine dati effettiva, ma ne semplifica per gli utenti l’individuazione e la comprensione. Quando gli utenti si connettono a un'origine dati, l'applicazione client scelta usa le credenziali di Windows o richiede le credenziali necessarie. Se l'utente non ha già ottenuto l'accesso all'origine dati, dovrà essere concesso l'accesso prima che possa connettersi.

### Di seguito viene illustrato come connettersi a un'origine dati da Excel

1. Passare a https://azure.microsoft.com/services/data-catalog, fare clic su **Introduzione**, quindi accedere al portale del **Catalogo dati di Azure**.
2. Fare clic su **Individua**.
3. Scegliere un asset di dati. In questo esempio, scegliere ProductCategory.
4. Scegliere **Apri in** > **Excel**.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. Nella finestra **Avviso di sicurezza di Microsoft Excel**, fare clic su **Abilita**.
6. Aprire il file **ProductCategory.odc**.
7. L'origine dati viene aperta in Excel.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

In questo esercizio ci si connette alle origini dati individuate tramite il **Catalogo dati di Azure**. Il portale del **Catalogo dati di Azure** consente agli utenti di connettersi direttamente tramite le applicazioni client integrate nel menu **Apri in** e consente agli utenti di connettersi mediante qualsiasi applicazione scelta usando le informazioni sul percorso di connessione incluse nei metadati dell'asset.

## Esercizio 7: rimozione dei metadati dell'origine dati

In questo esercizio si userà il portale del **Catalogo dati di Azure** per rimuovere i dati di anteprima dagli asset di dati registrati ed eliminare gli asset di dati dal catalogo.

> [AZURE.NOTE] Il comportamento predefinito del catalogo consente a tutti gli utenti di registrare qualsiasi origine dati e consente a qualsiasi utente di eliminare qualsiasi asset di dati registrato. Le funzionalità di gestione incluse nell'**Edizione Standard del Catalogo dati di Azure** forniscono opzioni aggiuntive per acquisire la proprietà degli asset, limitando gli utenti che possono individuare ed eliminare gli asset.

Nel **Catalogo dati di Azure** è possibile eliminare un singolo asset o più asset contemporaneamente.

### Per eliminare più asset di dati

1. Passare a https://azure.microsoft.com/services/data-catalog, fare clic su **Introduzione**, quindi accedere al portale del **Catalogo dati di Azure**.
2. Fare clic su **Individua**.
3. Scegliere uno o più asset di dati.
4. Fare clic su **Elimina**.

In questo esercizio sono stati rimossi asset di dati registrati dal catalogo.

## Esercizio 8: gestione delle origini dati registrate

In questo esercizio verranno usate le funzionalità di gestione del **Catalogo dati di Azure** per assumere la proprietà degli asset di dati e per controllare quali asset possono individuare gli utenti e come possono gestirli.

> [AZURE.NOTE] Le funzionalità di gestione descritte in questo esercizio sono disponibili solo nell'**edizione standard del Catalogo dati di Azure** e non nell'**edizione gratuita**. Nel **Catalogo dati di Azure** è possibile assumere la proprietà di asset di dati, aggiungere co-proprietari ad asset di dati e impostare la visibilità degli asset di dati.

### Ecco come si assume la proprietà di asset di dati si limita la visibilità

1. Passare a https://azure.microsoft.com/services/data-catalog, fare clic su **Introduzione**, quindi accedere al portale del **Catalogo dati di Azure**.
2. Fare clic su **Individua**.
3. Scegliere uno o più asset di dati.
4. Nel pannello **Proprietà**, sezione **Gestione**, fare clic su **Assumi proprietà**.
5. Per limitare la visibilità, fare clic su **Proprietari e utenti**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

In questo esercizio sono state analizzate le funzionalità di gestione del **Catalogo dati di Azure** e si è limitata la visibilità degli asset di dati selezionati.

## Riepilogo

In questa esercitazione sono state analizzate le funzionalità essenziali dell'anteprima del **Catalogo dati di Azure**, compresa la registrazione, l'annotazione, l'individuazione e la gestione di origini dati aziendali. Ora che è stata completata l'esercitazione, è possibile iniziare. È possibile iniziare subito registrando le origini dati usate personalmente o dal team e invitando i colleghi a usare il catalogo.

<!---HONumber=AcomDC_0309_2016-->