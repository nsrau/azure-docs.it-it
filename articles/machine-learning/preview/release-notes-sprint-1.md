---
title: Note sulla versione sprint 1 di novembre 2017 di Azure ML Workbench
description: Questo documento illustra in modo dettagliato gli aggiornamenti per la versione sprint 1 di Azure ML
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-1---november-2017"></a>Sprint 1 - Novembre 2017 

**Numero di versione: 0.1.1710.31013**

Benvenuti al secondo aggiornamento di Azure Machine Learning Workbench. Vengono apportati continuamente miglioramenti alla sicurezza, la stabilità e la manutenibilità nell'app Workbench, l'interfaccia della riga di comando e il livello di servizi back-end. Si ringrazia per i commenti inviati. Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. Si prega di continuare a inviare i propri commenti!

## <a name="notable-new-features"></a>Nuove funzionalità importanti
- Azure ML è ora disponibile in due nuove aree di Azure: **Europa occidentale** e **Asia sud-orientale**. Queste vanno a unirsi alle precedenti aree **Stati Uniti orientali 2**, **Stati Uniti centro-occidentali** e **Australia orientale**, portando a cinque il numero totale di aree di distribuzione.
- È stata abilitata l'evidenziazione della sintassi del codice Python nell'app Workbench per facilitare la lettura e la modifica del codice sorgente Python. 
- Ora è possibile avviare l'IDE preferito direttamente da un file, anziché dall'intero progetto.  Aprendo un file in Workbench e facendo clic su "Modifica" si avvia l'IDE (attualmente sono supportati VS Code e PyCharm ) con il file e il progetto correnti aperti.  È anche possibile fare clic sulla freccia accanto al pulsante Modifica per modificare il file nell'editor di testo di Workbench.  I file sono di sola lettura fino a quando non si fa clic su Modifica, impedendo modifiche accidentali.
- La diffusa libreria di tracciati `matplotlib` versione 2.1.0 viene ora fornita con l'app Workbench.
- È stato eseguito l'aggiornamento di .NET Core alla versione 2.0 per il motore di preparazione dei dati. Questo ha eliminato il requisito di eseguire il brew-install di openssl durante l'installazione dell'app su macOS. Inoltre apre la strada all'aggiunta futura di funzionalità di preparazione dei dati più interessanti. 
- È stata abilitata una home page dell'app specifica della versione, perciò sarà possibile ottenere note sulla versione e suggerimenti di aggiornamento più pertinenti in base alla versione corrente dell'app.
- Se il nome utente locale ha uno spazio, l'applicazione può ora essere installata correttamente. 

## <a name="detailed-updates"></a>Aggiornamenti dettagliati
Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

### <a name="installer"></a>Programma di installazione
- Il programma di installazione dell'app ora pulisce la directory di installazione creata dalla versione precedente dell'app.
- È stato corretto un bug che bloccava il programma di installazione al 100% in macOS High Sierra.
- Ora c'è un collegamento diretto alla directory del programma di installazione con cui l'utente può vedere i log del programma di installazione in caso di errori di installazione.
- L'installazione ora funziona per gli utenti che hanno spazi nel nome utente.

### <a name="workbench-authentication"></a>Autenticazione di Workbench
- Supporto per l'autenticazione in gestione proxy.
- Ora l'accesso riesce se l'utente si trova dietro un firewall. 
- Se l'utente dispone di account Sperimentazione in più aree di Azure e se succede che un'area non è disponibile, l'app non verrà più interrotta.
- Quando l'autenticazione non è completata e la finestra di dialogo di autenticazione è ancora visibile, l'app non tenta più di caricare l'area di lavoro dalla cache locale.

### <a name="workbench-app"></a>App Workbench
- L'evidenziazione della sintassi del codice Python è abilitata nell'editor di testo.
- Il pulsante Modifica nell'editor di testo consente di modificare il file o in un IDE (sono supportati VS Code e PyCharm) o nell'editor di testo integrato.
- L'editor di testo è in modalità di sola lettura per impostazione predefinita. 
- Il pulsante Salva diventa disabilitato dopo che il file corrente è stato salvato in seguito a modifiche.
- Quando si avvia un'esecuzione, Workbench salva _tutti_ i file non salvati.
- Workbench ricorda l'ultima area di lavoro utilizzata nel computer locale, che si apre automaticamente.
- Ora può essere eseguita una sola istanza di Workbench. In precedenza era possibile avviare più istanze, il che causava problemi quando si operava sullo stesso progetto.
- Il comando del menu File "Apri progetto …" è stato rinominato in "Add Existing Folder as Project..." ("Aggiungi cartella esistente come progetto...") 
- Il passaggio da una scheda all'altra è ora molto più veloce.
- Collegamenti di guida sono stati aggiunti alla finestra di dialogo di configurazione dell'IDE.
- Il modulo di feedback ora ricorda l'indirizzo di posta elettronica immesso l'ultima volta.
- L'area di testo del modulo dei commenti adesso è più grande, perciò è possibile inviare più commenti e suggerimenti. 
- Il testo di guida del parametro `--owner` in `az ml workspace create` è stato corretto.
- È stata aggiunta la finestra di dialogo "Informazioni su" per consentire all'utente di visualizzare e copiare facilmente il numero di versione dell'app.
- È stata aggiunta la voce di menu "Suggerire una funzionalità" al menu Guida.
- Il nome dell'account Sperimentazione è ora visibile sulla barra del titolo dell'app prima del nome dell'app "Azure Machine Learning Workbench".
- Ora viene visualizzata una home page dell'app specifica della versione in base alla versione dell'app rilevata.

### <a name="data-preparation"></a>Preparazione dei dati 
- Il sito Web esterno non può più essere caricato dal controllo mappa per evitare potenziali problemi di sicurezza.
- I controlli istogramma e conteggio dei valori hanno ora un'opzione per visualizzare un grafico in scala logaritmica.
- Quando un calcolo è in corso, la barra della qualità dei dati mostra un colore diverso per segnalare lo stato "calcolo in corso".
- Le metriche delle colonne ora mostrano le statistiche per le colonna di valori di categoria.
- L'ultimo carattere nel nome dell'origine dati non viene troncato.
- Il pacchetto di preparazione dei dati adesso rimane aperto quando si cambia scheda, con un conseguente notevole miglioramento delle prestazioni.
- Nell'origine dati, quando si passa fra la visualizzazione dei dati e quella delle metriche, l'ordine delle colonne non viene più cambiato.
- L'apertura di un file `.dprep` o `.dsource` non valido non causa più un arresto anomalo di Workbench.
- Il pacchetto di preparazione dei dati adesso può usare un percorso relativo per l'output nella trasformazione _scrivi in CSV_.
- La trasformazione _mantieni colonna_ consente ora agli utenti di aggiungere altre colonne in fase di modifica.
- Il menu _Replace this_ (Sostituisci) ora apre effettivamente la finestra di dialogo _Sostituisci valore_.
- La trasformazione _sostituisci valore_ adesso funziona come previsto anziché generare un errore.
- Il pacchetto di preparazione dei dati adesso usa il percorso assoluto quando fa riferimento a file di dati esterni alla cartella del progetto, consentendo di eseguire il pacchetto nel contesto locale con un percorso assoluto al file di dati.
- Ora è supportata la strategia di campionamento _file completo_ quando si usa il BLOB di Azure come origine dati.
- Il codice Python generato (dal pacchetto di preparazione dei dati) ora mantiene sia CR che LF, per una maggiore facilità di comprensione in Windows.
- L'elenco a discesa _Scegli metriche_ adesso nasconde le proprietà quando si passa alla visualizzazione Dati.
- Workbench può ora elaborare i file parquet anche quando usa il runtime di Python. In precedenza solo Spark poteva essere usato durante l'elaborazione di file parquet. 
- Il filtraggio dei valori in una colonna con il tipo di dati _data_ non causa più il blocco del motore di preparazione dei dati.
- La vista metriche adesso rispetta gli aggiornamenti della strategia di campionamento.
- Ora i processi di campionamento remoto funzioni correttamente.

### <a name="job-execution"></a>Esecuzione dei processi
- L'argomento è ora incluso nei record della cronologia di esecuzione.
- I processi avviati nell'interfaccia della riga di comando adesso sono mostrati nel pannello del processo della cronologia di esecuzione.
- Il pannello del processo ora mostra i processi creati dagli utenti guest aggiunti al tenant di AAD.
- Le azioni di annullamento ed eliminazione del pannello del processo sono più stabili.
- Quando si fa clic sul pulsante Esegui, adesso viene attivato un messaggio di errore se i file di configurazione si trovano in un formato non valido.
- La terminazione dell'app non interferisce più con i processi avviati nell'interfaccia della riga di comando.
- I processi avviati nell'interfaccia della riga di comando continuano a emettere output standard anche dopo un'ora di esecuzione.
- Adesso sono visualizzati messaggi di errore migliori quando si verifica un errore di esecuzione del pacchetto di preparazione dei dati in Python/PySpark.
- `az ml experiment clean` ora pulisce le immagini Docker anche nella VM remota.
- `az ml experiment clean` ora funziona correttamente per la destinazione locale in macOS.
- I messaggi di errore quando la destinazione è costituita da esecuzioni locali o remote di Docker sono più puliti e facili da leggere.
- Viene visualizzato un messaggio di errore migliore se il nome del nodo head del cluster HDInsight non è formattato correttamente quando è collegato come destinazione di esecuzione.
- Viene visualizzato un messaggio di errore migliore quando il segreto non viene trovato nel servizio di credenziali. 
- La libreria MMLSpark è stata aggiornata per supportate Apache Spark 2.2.
- MMLSpark ora include la trasformazione di codifica del soggetto (codifica Mesh) per i documenti di tipo medico.
- `matplotlib` versione 2.1.0 è ora accluso a Workbench.

### <a name="jupyter-notebook"></a>Notebook Jupyter
- La ricerca dei nomi di Notebook ora funziona correttamente nella visualizzazione Notebook.
- È ora possibile eliminare un Notebook nella visualizzazione Notebook.
- Un nuovo e magico `%upload_artifact` è stato aggiunto per caricare i file prodotti nell'ambiente di esecuzione Notebook nell'archivio dati della cronologia di esecuzione.
- Gli errori del kernel sono ora rilevati nello stato del processo Notebook per semplificare il debug.
- Il server Jupyter adesso si arresta correttamente quando l'utente si disconnette dall'app.

### <a name="azure-portal"></a>Portale di Azure
- L'account di sperimentazione e l'account Gestione modelli ora possono essere creati in due nuove aree di Azure: Europa occidentale e Asia sud-orientale.
- Il piano DevTest dell'account Gestione modelli è ora disponibile solo quando è il primo a essere creato nella sottoscrizione. 
- Il collegamento alla guida nel portale di Azure è stato aggiornato in modo da puntare alla pagina di documentazione corretta.
- Il campo della descrizione è stato rimosso dalla pagina dei dettagli dell'immagine Docker perché non è pertinente.
- Informazioni dettagliate, fra cui AppInsights e le impostazioni di scalabilità automatica, sono state aggiunte alla pagina dei dettagli del servizio Web.
- La pagina di gestione modelli ora viene visualizzata anche se i cookie di terze parti sono disabilitati nel browser. 

### <a name="operationalization"></a>Operazionalizzazione
- I servizi Web con il "trattino" nel nome non si bloccano più.
- L'utente può creare ora un ambiente di distribuzione con il solo accesso come collaboratore a un gruppo di risorse di Azure o alla sottoscrizione. L'accesso come proprietario all'intera sottoscrizione non è più necessario.
- L'interfaccia della riga di comando di operazionalizzazione ora dispone del completamento automatico con il tasto Tab in Linux.
- Il servizio di creazione di immagini adesso supporta la creazione di immagini per i servizi e dispositivi IoT di Azure.

### <a name="sample-projects"></a>Progetti di esempio
- Progetto di esempio [_Classificazione iris_](./tutorial-classifying-iris-part-1.md):
    - `iris_pyspark.py` viene rinominata come `iris_spark.py`.
    - `iris_score.py` viene rinominata come `score_iris.py`.
    - `iris.dprep` e `iris.dsource` sono stati aggiornati per riflettere gli aggiornamenti più recenti del motore di preparazione dei dati.
    - `iris.ipynb` Notebook è stato modificato per funzionare nel cluster HDInsight.
    - La cronologia di esecuzione è attivata nella cella `iris.ipynb` di Notebook.
- Il passaggio "Gestione dei valori di errore" del progetto di esempio [_Preparazione di dati avanzata con dati di bike sharing_](./tutorial-bikeshare-dataprep.md) è stato corretto.
- Il formato di `docker.runconfig` del progetto di esempio [_MMLSpark on Adult Census Data_](https://github.com/Azure/MachineLearningSamples-mmlspark) (MMLSpark su dati di censimento di adulti) è stato aggiornato da JSON a YAML.
- Il formato di `docker.runconfig` del progetto di esempio [_Ottimizzazione distribuita di iperparametri_](./scenario-distributed-tuning-of-hyperparameters.md) è stato aggiornato da JSON a YAML.
- Nuovo progetto di esempio [_Classificazione delle immagini con CNTK_](./scenario-image-classification-using-cntk.md).
