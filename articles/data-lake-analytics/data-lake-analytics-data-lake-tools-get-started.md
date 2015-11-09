<properties 
   pageTitle="Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio | Azure" 
   description="Informazioni su come installare Strumenti di Data Lake per Visual Studio e come sviluppare e testare script U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informazioni su come installare Strumenti di Data Lake per Visual Studio e usarlo per scrivere e testare script U-SQL.

U-SQL è un linguaggio estremamente scalabile e facilmente estendibile per la preparazione, la trasformazione e l'analisi di tutti i dati presenti nel data lake e non solo. Per altre informazioni, vedere la pagina di [riferimento su U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).


**Prerequisiti**

- **Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012 con Visual C++ installato** 
- **Microsoft Azure SDK per .NET versione 2.5 o successiva**. Installare il prodotto usando il [programma di installazione della piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Strumenti di Data Lake per Visual Studio](http://aka.ms/adltoolsvs)**. 

    Dopo aver installato Strumenti di Data Lake per Visual Studio, in Visual Studio verrà visualizzato il menu Data Lake:
    
    ![Menu Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Eseguire le procedure descritte nelle due sezioni seguenti, disponibili nell'articolo [Introduzione ad Analisi Data Lake di Azure tramite il portale di anteprima di Azure](data-lake-analytics-get-started-portal.md)**.

	- [Creare un account di Analisi Data Lake di Azure](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
	- [Caricare SearchLog.tsv nell'account di archiviazione predefinito di Data Lake](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

	Strumenti di Data Lake non supporta la creazione di account di Analisi Data Lake. Sarà pertanto necessario creare tali account tramite il portale di anteprima di Azure, Azure PowerShell, .NET SDK o l'interfaccia della riga di comando di Azure. Per eseguire un processo di Analisi Data Lake, sanno necessari alcuni dati. Anche se Strumenti di Data Lake supporta il caricamento di dati, si ricorrerà al portale per caricare i dati di esempio e seguire più facilmente lo sviluppo di questa esercitazione.

## Connettersi ad Azure

**Per connettersi ad Analisi Data Lake**

1. Aprire Visual Studio.
2. Nel menu **U-SQL** fare clic su **Opzioni e impostazioni**.
4. Fare clic su **Accedi** o **Cambia utente** se un altro utente ha già eseguito l'accesso e seguire le istruzioni per l'accesso.
5. Fare clic su **OK** per chiudere la finestra di dialogo Opzioni e impostazioni.

**Per accedere agli account di Analisi Data Lake**

1. In Visual Studio aprire **Esplora server** premendo i tasti **CTRL + ALT + S**.
2. Da **Esplora server** espandere **Azure** e quindi **Analisi Data Lake**. Verrà visualizzato l'elenco degli account di Analisi Data Lake personali, se disponibili. Non è possibile creare account di Analisi Data Lake da Visual Studio. Per creare un account, vedere [Introduzione ad Analisi Data Lake di Azure tramite il portale di anteprima di Azure](data-lake-analytics-get-started-portal.md) o [Introduzione ad Analisi Data Lake di Azure mediante Azure PowerShell](knoa-get-started-powershell.md).

## Caricare i file dei dati di origine

Alcuni dati sono già stati caricati nell'ambito della sezione **Prerequisiti** di questa esercitazione.

Nel caso in cui si preferisca usare dati personali, di seguito sono riportate le procedure da seguire per caricare dati da Strumenti di Data Lake.

**Per caricare il file nell'account di Azure Data Lake dipendente**

1. Da **Esplora server** espandere **Azure**, quindi **Analisi Data Lake**, l'account di Analisi Data Lake personale e infine **Account di archiviazione**. Verrà visualizzato l'account di archiviazione predefinito di Data Lake, con gli account di archiviazione di Data Lake e gli account di Archiviazione di Azure collegati. L'account di archiviazione predefinito di Data Lake è contraddistinto dall'etichetta "Account di archiviazione predefinito".
2. Fare doppio clic sull'account di archiviazione predefinito di Data Lake e quindi fare clic su **Esplora**. Viene visualizzato il pannello di esplorazione relativo a Strumenti di Data Lake per Visual Studio. Nella parte sinistra è presente una visualizzazione albero, mentre la visualizzazione contenuto è disponibile sulla destra.
3. Selezionare la cartella in cui si desidera caricare i file. 
4. Fare clic con il pulsante destro del mouse su uno spazio vuoto e fare clic su **Carica**. 

	![Progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Per caricare un file in un account di archiviazione BLOB di Azure**

1. Da **Esplora server** espandere **Azure**, quindi **Analisi Data Lake**, l'account di Analisi Data Lake personale e infine **Account di archiviazione**. Verrà visualizzato l'account di archiviazione predefinito di Data Lake, con gli account di archiviazione di Data Lake e gli account di Archiviazione di Azure collegati. 
2. Espandere l'account di Archiviazione di Azure.
3. Fare clic con il pulsante destro del mouse sul contenitore in cui si desidera caricare i file e quindi fare clic su **Esplora**.
4. Selezionare la cartella in cui si desidera caricare i file. 
5. Fare clic con il pulsante destro del mouse su uno spazio vuoto e fare clic su **Carica**. 

## Sviluppare e testare script U-SQL 

I processi di Analisi Data Lake vengono scritti nel linguaggio U-SQL. Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Per creare e inviare un processo di Analisi Data Lake**

1. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
2. Selezionare il tipo **Progetto U-SQL**.

	![Nuovo progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Fare clic su **OK**. Visual Studio crea una soluzione con un file **Script.usql**.
4. Immettere lo script seguente in **Script.usql**:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Questo script U-SQL legge il file dei dati di origine mediante **Extractors.Tsv()** e quindi crea un file con estensione csv mediante **Outputters.Csv()**.
    
    Non modificare i due percorsi, a meno che il file di origine non sia stato copiato in una posizione diversa. Analisi Data Lake creerà la cartella di output, se non esiste già.
	
	Per i file archiviati negli account predefiniti di Data Lake risulta più semplice usare percorsi relativi, ma è possibile usare anche percorsi assoluti. Ad esempio
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati. La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Non sono attualmente supportate autorizzazioni di accesso a contenitori pubblici o a contenitori BLOB di Azure con BLOB pubblici.

	Prestare attenzione alle funzionalità seguenti:

	- **IntelliSense**
	 
		Per le entità seguenti vengono visualizzati il nome completato in modo automatico e i membri: Rowset, Classes, Databases, Schemas e User Defined Objects (UDO).
		 
		IntelliSense per le entità di catalogo (Databases, Schemas, Tables, UDO, e così via) è correlato all'account di calcolo personale. È possibile controllare l'account di calcolo, il database e lo schema attualmente attivi nella barra degli strumenti superiore e sostituirli tramite gli elenchi a discesa.
 
	- **Formattazione automatica**
	
		Gli utenti possono modificare il rientro dello script di ambito in base alla struttura di codice definita in Modifica->Avanzate:
  
		- Formatta documento (Ctrl + E, D): formatta l'intero documento   
		- Formatta selezione (Ctrl+K, Ctrl+F): formatta la selezione. Se non è stata effettuata alcuna selezione, questo collegamento formatta la riga in cui si trova il cursore.  
		
		Tutte le regole di formattazione possono essere configurate in Strumenti->Opzioni->Editor di testo->SIP->Formattazione.  
	- **Rientro automatico**
	 
		Strumenti di Data Lake per Visual Studio è in grado di impostare automaticamente il rientro delle espressioni durante la scrittura degli script. Per attivare questa funzionalità, disabilitata per impostazione predefinita, è necessario selezionare U-SQL->Opzioni e impostazioni->Opzione->Abilita rientro automatico.

	- **Vai a definizione e Trova tutti i riferimenti**
	
		Fare clic con il pulsante destro del mouse sul nome di un set di righe/parametro/colonna/UDO e fare clic su Vai a definizione (F12) per accedere alla relativa definizione. Facendo clic su Trova tutti i riferimenti (Shift+F12) verranno invece visualizzati tutti i riferimenti.

	- **Inserimento di percorsi di Azure**
		
		Con Strumenti di Data Lake per Visual Studio non sarà più necessario ricordare il percorso del file di Azure e digitarlo manualmente, ma sarà sufficiente fare clic con il pulsante destro del mouse sull'editor e selezionare Inserisci percorso di Azure. Selezionare il file nella finestra di dialogo del browser relativo al BLOB di Azure. Fare clic su OK. Il percorso del file verrà inserito nel codice personale.

5. Specificare l'account di Analisi Data Lake, il database e lo schema:

	![Invio progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Per altre informazioni, vedere [Usare il catalogo di U-SQL](data-lake-analytics-use-u-sql-catalog.md).

5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi fare clic su **Compila script**. Verificare il risultato nel riquadro di output.
6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi fare clic su **Invia script**. In alternativa, è possibile fare clic su **Invia** nel pannello Script.usql. Vedere l'immagine sopra riportata. Fare clic sulla freccia rivolta verso il basso accanto al pulsante di invio per accedere alle opzioni avanzate.
7. Specificare **Nome processo**, verificare il nome presente in **Account di Analisi** e quindi fare doppio clic su **Invia**. Al termine della procedura di invio, nella finestra dei risultati di Strumenti di Data Lake per Visual Studio saranno disponibili i risultati dell'operazione di invio e il collegamento al processo.

	![Invio progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Per visualizzare lo stato attuale del processo, è necessario fare clic sul pulsante Aggiorna per aggiornare la schermata. Al termine del processo, verranno visualizzate schede relative a: **Grafico del processo**, **Operazioni sui metadati**, **Cronologia dello stato** e **Diagnostica**:

	![Grafico delle prestazioni del processo di Analisi Data Lake per Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* Riepilogo del processo. Mostra le informazioni di riepilogo del processo corrente, tra cui lo stato, lo stato di avanzamento, il tempo di esecuzione, il nome di runtime, l'autore dell'invio e così via.   
	* Dettagli del processo. Visualizza informazioni dettagliate sul processo corrente, tra cui lo script, le risorse e la vista relativa all'esecuzione del vertice.
	* Grafico del processo. Contiene quattro grafici in cui vengono visualizzate graficamente numerose informazioni sul processo, tra cui lo stato di avanzamento, i dati letti, i dati scritti, il tempo di esecuzione, il tempo di esecuzione medio per ogni nodo, la velocità effettiva di input e la velocità effettiva di output.
	* Operazioni sui metadati. Mostra tutte le operazioni eseguite sui metadati.
	* Cronologia dello stato. 
	* Diagnostica. Strumenti di Data Lake per Visual Studio diagnosticherà l'esecuzione dei processi in modo automatico e invierà avvisi in caso di errori o problemi di prestazioni nei processi. Per altre informazioni, vedere la sezione relativa alle funzioni di diagnostica del processo (collegamento da definire). 
	
**Per controllare lo stato del processo**

1. Da Esplora server espandere **Azure**, quindi **Analisi Data Lake** e il nome dell'account di Analisi Data Lake.
2. Fare doppio clic su **Processi** per ottenere un elenco dei processi.
2. Fare clic su un processo per visualizzarne lo stato.

**Per visualizzare l'output del processo**

1. Da **Esplora server** espandere **Azure**, quindi **Analisi Data Lake**, l'account di Analisi Data Lake personale e infine **Account di archiviazione**. Fare doppio clic sull'account di archiviazione predefinito di Data Lake e quindi fare clic su **Esplora**. 
2.  Fare doppio clic su **output** per aprire la cartella.
3.  Fare doppio clic su **SearchLog-From-adltools.csv**.


###Riproduzione del processo 

Con la riproduzione del processo è possibile controllare lo stato di avanzamento del processo e rilevare visivamente eventuali colli di bottiglia o anomalie delle prestazioni. Questa funzionalità può essere usata sia prima del completamento del processo (ad esempio mentre il processo è ancora in esecuzione), sia dopo il termine dell'esecuzione. Se si esegue la riproduzione durante l'esecuzione del processo, sarà possibile riprodurre lo stato di avanzamento fino all'ora corrente.

**Per visualizzare lo stato di esecuzione del processo**

1. Fare clic su **Carica profilo** nell'angolo superiore destro. Vedere l'immagine sopra riportata.
2. Fare clic sul pulsante di riproduzione nell'angolo inferiore sinistro per rivedere lo stato di esecuzione del processo. 
3. Durante la riproduzione, è possibile fare clic su **Pausa** per arrestarla oppure trascinare direttamente la barra di avanzamento in posizioni specifiche. 


###Mappa termica 

Strumenti di Data Lake per Visual Studio applica alla visualizzazione del processo sovrapposizioni di colore selezionabili dall'utente per indicare, per ogni fase, lo stato di avanzamento, l'input/output di dati, il tempo di esecuzione e la velocità effettiva di input/output. In questo modo, gli utenti possono individuare in modo diretto e intuitivo eventuali problemi potenziali e la distribuzione delle proprietà del processo. Dall'elenco a discesa è possibile scegliere un'origine dati da visualizzare.



##Vedere anche

Per iniziare a usare Analisi Data Lake usando vari tipi di strumenti, vedere:

- [Introduzione ad Analisi Data Lake tramite il portale di anteprima di Azure](data-lake-analytics-get-started-portal.md)
- [Introduzione ad Analisi Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introduzione ad Analisi Data Lake mediante .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Per visualizzare altri argomenti relativi allo sviluppo:

- [Analizzare blog mediante Analisi Data Lake](data-lake-analytics-analyze-weblogs.md)
- [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Introduzione al linguaggio U-SQL di Analisi Data Lake di Azure](data-lake-analytics-u-sql-get-started.md)
- [Sviluppare operatori U-SQL definiti dall'utente per i processi di Analisi Data Lake](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO1-->