---
title: Novità in Azure Machine Learning
description: Questo documento illustra in dettaglio gli aggiornamenti di Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: e30943426ad68171e1464f828a9c8672b06c975a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Novità in Azure Machine Learning

In questo articolo vengono fornite maggiori informazioni sulle nuove versioni di [Machine Learning Services di Azure](../service/overview-what-is-azure-ml.md). 

## <a name="2018-03-sprint-4"></a>Sprint 4 03-2018
**Numero di versione**: 0.1.1801.24353 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Benvenuti al quinto aggiornamento di Azure Machine Learning Workbench. Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. Si prega di continuare a inviare i propri commenti!

**Nuove funzionalità e modifiche importanti**

- Supporto per l'esecuzione degli script su VM Ubuntu remote in modo nativo nel proprio ambiente oltre all'esecuzione basata su Docker remoto.
- Le funzionalità dell'ambiente nell'app Workbench consentono ora di creare destinazioni di calcolo e configurazioni di esecuzione, oltre all'esperienza basata sull'interfaccia della riga di comando.
![Scheda degli ambienti](media/azure-machine-learning-release-notes/environment-page.png)
- Report sulla cronologia di esecuzione personalizzabili ![Immagine dei nuovi report sulla cronologia di esecuzione](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Aggiornamenti dettagliati**

Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

### <a name="workbench-ui"></a>Interfaccia utente di Workbench
- Report sulla cronologia di esecuzione personalizzabili
  - Configurazione dei grafici migliorata per i report sulla cronologia di esecuzione
    - I punti di ingresso usati possono essere modificati
    - I filtri di primo livello possono essere aggiunti e modificati ![Aggiungere i filtri](media/azure-machine-learning-release-notes/add-filters.jpg)
    - I grafici e le statistiche possono essere aggiunti o modificati e il trascinamento della selezione può essere ridisposto.
    ![Creazione di nuovi grafici](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD per i report sulla cronologia di esecuzione
  - Tutte le configurazioni della visualizzazione elenco della cronologia di esecuzione esistenti sono state spostate nei report lato server, che funzionano come pipeline nelle esecuzioni dai punti di ingresso selezionati.

- Scheda degli ambienti
  - Aggiungere facilmente la nuova destinazione di calcolo e i file di configurazione di esecuzione al progetto ![Nuova destinazione di calcolo](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Gestire a aggiornare i file di configurazione usando una semplice esperienza utente basata su form
  - Nuovo pulsante per preparare gli ambienti per l'esecuzione

- Miglioramenti delle prestazioni apportati all'elenco di file nella barra laterale

### <a name="data-preparation"></a>Preparazione dei dati 
- Azure Machine Learning Workbench ora consente di cercare una colonna usando il nome di una colonna nota.


### <a name="experimentation"></a>Sperimentazione
- Azure Machine Learning Workbench ora supporta l'esecuzione degli script in modo nativo nell'ambiente python o pyspark. Grazie a questa funzionalità, l'utente crea e gestisce l'ambiente nella VM remota e usa Azure Machine Learning Workbench per eseguire gli script in tale destinazione. Vedere [Configurazione del servizio Sperimentazione di Azure Machine Learning](experimentation-service-configuration.md) 

### <a name="model-management"></a>Gestione modelli
- Supporto per la personalizzazione dei contenitori distribuiti: consente di personalizzare l'immagine del contenitore permettendo l'installazione di librerie esterne con apt-get e così via. Non è più limitato alle librerie installabili con pip. Per altre informazioni, vedere la [documentazione](model-management-custom-container.md).
  - Usare il flag `--docker-file myDockerStepsFilename` e il nome file con i comandi per la creazione del manifesto, dell'immagine o del servizio.
  - Tenere presente che l'immagine di base è Ubuntu e non può essere modificata.
  - Comando di esempio: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>Sprint 3 01-2018 
**Numero di versione**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Trovare la versione](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Di seguito è riportato un elenco degli aggiornamenti e miglioramenti in questo sprint. Molti degli aggiornamenti sono una conseguenza diretta del feedback ricevuto. 


Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

- Gli aggiornamenti allo stack di autenticazione forzano la selezione di login e account all'avvio

### <a name="workbench"></a>Workbench
- Possibilità di installare o disinstallare l'app da Installazione applicazioni
- Gli aggiornamenti allo stack di autenticazione forzano la selezione di login e account all'avvio
- Esperienza di Single Sign-On (SSO) migliorata in Windows
- Gli utenti che appartengono a più tenant con credenziali diverse ora saranno in grado di accedere a Workbench

### <a name="ui"></a>UI
- Correzioni di bug e miglioramenti generali

### <a name="notebooks"></a>Notebook
- Correzioni di bug e miglioramenti generali

### <a name="data-preparation"></a>Preparazione dei dati 
- Miglioramento dei suggerimenti automatici migliorata durante l'esecuzione di trasformazioni in base a esempi
- Algoritmo migliorato per il controllo della frequenza
- Possibilità di inviare dati di esempio e feedback durante l'esecuzione di trasformazioni in base a esempi ![Immagine del collegamento per l'invio di feedback nella trasformazione di derivazione colonna](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Miglioramenti del runtime Spark
- Scala ha sostituito Pyspark
- Risolta l'impossibilità di chiudere i dati non applicabili per la funzione di controllo delle serie temporali 
- Risolto il tempo di blocco per l'esecuzione di preparazione dei dati per HDI

### <a name="model-management-cli-updates"></a>Aggiornamenti all'interfaccia della riga di comando di Gestione modelli 
  - Per il provisioning di risorse non è più necessaria la proprietà della sottoscrizione. Per configurare l'ambiente di distribuzione sarà sufficiente l'accesso al gruppo di risorse di tipo Collaboratore.
  - Abilitata la configurazione dell'ambiente locale per le sottoscrizioni gratuite 

## <a name="2017-12-sprint-2-qfe"></a>Sprint 2 QFE 12-2017 
**Numero di versione**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Trovare la versione](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Questa è la versione QFE (Quick Fix Engineering), una versione minore. In questa versione sono stati risolti alcuni problemi di telemetria e per il team di prodotto risulta più semplice comprendere meglio l'uso del prodotto. Le conoscenze acquisite potranno essere quindi sfruttate nell'impegno futuro per il miglioramento dell'esperienza del prodotto. 

Sono inoltre disponibili due aggiornamenti importanti:

- Correzione di un bug nella preparazione dei dati che ha impedito la visualizzazione della funzione di controllo delle serie temporali nei pacchetti di preparazione dei dati.
- Nello strumento della riga di comando non è più necessario essere un proprietario della sottoscrizione di Azure per eseguire il provisioning di cluster ACS di calcolo di Machine Learning. 

## <a name="2017-12-sprint-2"></a>Sprint 2 12-2017
**Numero di versione**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Benvenuti al terzo aggiornamento di Azure Machine Learning, che include miglioramenti all'app Workbench, all'interfaccia della riga di comando e ai servizi back-end. Si ringrazia per i commenti inviati. Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. 

**Nuove funzionalità importanti**
- [Supporto per SQL Server e il database SQL di Azure come origine dati](data-prep-appendix2-supported-data-sources.md#types). 
- [Apprendimento avanzato su Spark con supporto GPU tramite MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Compatibilità di tutti i contenitori AML con i dispositivi Azure IoT Edge quando vengono distribuiti (non sono necessarie operazioni aggiuntive)](http://aka.ms/aml-iot-edge-blog)
- Elenco modelli registrati e visualizzazioni Dettagli disponibili nel portale di Azure
- Accesso alle destinazioni di calcolo tramite l'autenticazione basata su chiave SSH oltre all'accesso basato su nome utente/password 
- Nuovo controllo della frequenza dei modelli nell'esperienza di preparazione dati 

**Aggiornamenti dettagliati** Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

### <a name="installer"></a>Programma di installazione
- Il programma di installazione può aggiornarsi automaticamente in modo da supportare le correzioni di bug e le nuove funzionalità senza che l'utente debba rieseguire l'installazione.

### <a name="workbench-authentication"></a>Autenticazione di Workbench
- Più correzioni al sistema di autenticazione. Inviare un commento nel caso in cui si verifichino ancora problemi di accesso.
- Modifiche all'interfaccia utente che rendono più semplice trovare le impostazioni di gestione proxy.

### <a name="workbench"></a>Workbench
- La visualizzazione dei file di sola lettura è ora su sfondo azzurro.
- Il pulsante Modifica è stato spostato a destra per renderlo più facilmente individuabile.
- È ora possibile eseguire il rendering dei formati di file "dsource", "dprep" e "ipynb" in un formato di testo non elaborato.
- È ora disponibile una nuova esperienza di modifica nel Workbench che guida gli utenti all'uso di ambienti IDE esterni per modificare gli script e all'uso del Workbench solo per modificare i tipi di file con un'esperienza di modifica avanzata (ad esempio notebook, origini dati, pacchetti di preparazione dei dati).
- Il caricamento dell'elenco delle aree di lavoro e dei progetti a cui l'utente ha accesso è ora notevolmente più veloce.

### <a name="data-preparation"></a>Preparazione dei dati 
- È disponibile un controllo della frequenza dei modelli per visualizzare i modelli di stringa in una colonna. È anche possibile filtrare i dati tramite questi modelli. Comparirà una visualizzazione simile a quella del controllo dei conteggi di valore. La differenza è che la frequenza dei modelli mostra i conteggi dei modelli univoci dei dati, anziché i conteggi dei dati univoci. È anche possibile applicare filtri per visualizzare o escludere tutte le righe che soddisfano un determinato modello.

![Immagine del controllo della frequenza dei modelli sul numero del prodotto](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Miglioramenti delle prestazioni mentre si consigliano i casi limite da rivedere nella trasformazione "deriva colonna dall'esempio".

- [Supporto per SQL Server e il database SQL di Azure come origine dati](data-prep-appendix2-supported-data-sources.md#types). 

![Immagine della creazione di una nuova origine dati SQL server](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- È stata abilitata la visualizzazione "Riepilogo" dei conteggi di righe e colonne.

![Immagine del riepilogo dei conteggi di righe e colonne](media/azure-machine-learning-release-notes/row-col-count.png)

- La preparazione dei dati è abilitata in tutti i contesti di calcolo.
- Le origini dati che usano un database SQL Server sono abilitate in tutti i contesti di calcolo.
- Le colonne della griglia di preparazione dei dati possono essere filtrate per tipo di dati.
- È stato risolto il problema relativo alla conversione di più colonne in data.
- È stato risolto il problema per cui un utente poteva selezionare una colonna di output come origine in Deriva colonna dall'esempio dopo avere modificato il nome della colonna di output nella modalità avanzata.

### <a name="job-execution"></a>Esecuzione dei processi
È ora possibile creare e accedere a una destinazione di calcolo di tipo cluster o remotedocker tramite l'autenticazione basata su chiave SSH eseguendo la procedura riportata di seguito:
- Collegare una destinazione di calcolo usando il comando seguente dell'interfaccia della riga di comando.

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>L'opzione -k (o --use-azureml-ssh-key) nel comando specifica di generare e usare la chiave SSH.

- Azure Machine Learning Workbench genererà una chiave pubblica e l'output nella console. Accedere alla destinazione di calcolo usando lo stesso nome utente e accodare questa chiave pubblica al file ~/.ssh/authorized_keys.

- È possibile preparare questa destinazione di calcolo e usarla per l'esecuzione. Azure Machine Learning Workbench userà questa chiave per l'autenticazione.  

Per altre informazioni sulla creazione di destinazioni di calcolo, vedere [Configurazione del servizio Sperimentazione di Azure Machine Learning](experimentation-service-configuration.md).

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- È stato aggiunto il supporto per [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interfaccia della riga di comando
- È stato aggiunto il comando `az ml datasource create` che consente di creare un file di origine dati dalla riga di comando.

### <a name="model-management-and-operationalization"></a>Gestione modelli e operazionalizzazione
- [Compatibilità di tutti i contenitori AML con i dispositivi Azure IoT Edge quando vengono resi operativi (non sono necessarie operazioni aggiuntive)](http://aka.ms/aml-iot-edge-blog). 
- Miglioramenti dei messaggi di errore nell'interfaccia della riga di comando o16n.
- Correzioni di bug nel portale di gestione modelli UX.  
- Combinazione coerente di maiuscole/minuscole per gli attributi di gestione modelli nella pagina di dettaglio.
- Timeout delle chiamate del punteggio in tempo reale impostato su 60 secondi.
- Elenco modelli registrati e visualizzazioni Dettagli disponibili nel portale di Azure.

![dettagli modello nel portale](media/azure-machine-learning-release-notes/model-list.jpg)

![panoramica modello nel portale](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Apprendimento avanzato su Spark con [supporto GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md).
- Supporto per modelli di Resource Manager per la distribuzione semplificata delle risorse.
- Supporto per l'ecosistema SparklyR.
- [Integrazione di AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark).

### <a name="sample-projects"></a>Progetti di esempio
- Gli esempi [Iris](https://github.com/Azure/MachineLearningSamples-Iris) e [MMLSpark](https://github.com/Azure/mmlspark) sono stati aggiornati con la nuova versione SDK di Azure Machine Learning.

### <a name="breaking-changes"></a>Modifiche di rilievo
- L'opzione `--type` in `az ml computetarget attach` è stata alzata di livello a sottocomando. 

    - `az ml computetarget attach --type remotedocker` è ora `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` è ora `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>Sprint 1 11-2017 
**Numero di versione**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

In questa versione sono stati apportati miglioramenti alla sicurezza, alla stabilità e alla manutenibilità nell'app Workbench, dell'interfaccia della riga di comando e del livello di servizi back-end. Si ringrazia per i commenti inviati. Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. Si prega di continuare a inviarli!

### <a name="notable-new-features"></a>Nuove funzionalità importanti
- Azure ML è ora disponibile in due nuove aree di Azure: **Europa occidentale** e **Asia sud-orientale**. Queste vanno a unirsi alle precedenti aree **Stati Uniti orientali 2**, **Stati Uniti centro-occidentali** e **Australia orientale**, portando a cinque il numero totale di aree di distribuzione.
- È stata abilitata l'evidenziazione della sintassi del codice Python nell'app Workbench per facilitare la lettura e la modifica del codice sorgente Python. 
- Ora è possibile avviare l'IDE preferito direttamente da un file, anziché dall'intero progetto.  Aprendo un file in Workbench e facendo clic su "Modifica" si avvia l'IDE (attualmente sono supportati VS Code e PyCharm ) con il file e il progetto correnti aperti.  È anche possibile fare clic sulla freccia accanto al pulsante Modifica per modificare il file nell'editor di testo di Workbench.  I file sono di sola lettura fino a quando non si fa clic su Modifica, impedendo modifiche accidentali.
- La diffusa libreria di tracciati `matplotlib` versione 2.1.0 viene ora fornita con l'app Workbench.
- È stato eseguito l'aggiornamento di .NET Core alla versione 2.0 per il motore di preparazione dei dati. Questo ha eliminato il requisito di eseguire il brew-install di openssl durante l'installazione dell'app su macOS. Inoltre apre la strada all'aggiunta futura di funzionalità di preparazione dei dati più interessanti. 
- È stata abilitata una home page dell'app specifica della versione, perciò sarà possibile ottenere note sulla versione e suggerimenti di aggiornamento più pertinenti in base alla versione corrente dell'app.
- Se il nome utente locale ha uno spazio, l'applicazione può ora essere installata correttamente. 

### <a name="detailed-updates"></a>Aggiornamenti dettagliati
Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

#### <a name="installer"></a>Programma di installazione
- Il programma di installazione dell'app ora pulisce la directory di installazione creata dalla versione precedente dell'app.
- È stato corretto un bug che bloccava il programma di installazione al 100% in macOS High Sierra.
- Ora c'è un collegamento diretto alla directory del programma di installazione con cui l'utente può vedere i log del programma di installazione in caso di errori di installazione.
- L'installazione ora funziona per gli utenti che hanno spazi nel nome utente.

#### <a name="workbench-authentication"></a>Autenticazione di Workbench
- Supporto per l'autenticazione in gestione proxy.
- Ora l'accesso riesce se l'utente si trova dietro un firewall. 
- Se l'utente dispone di account Sperimentazione in più aree di Azure e se succede che un'area non è disponibile, l'app non verrà più interrotta.
- Quando l'autenticazione non è completata e la finestra di dialogo di autenticazione è ancora visibile, l'app non tenta più di caricare l'area di lavoro dalla cache locale.

#### <a name="workbench-app"></a>App Workbench
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

#### <a name="data-preparation"></a>Preparazione dei dati 
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

#### <a name="job-execution"></a>Esecuzione dei processi
- L'argomento è ora incluso nei record della cronologia di esecuzione.
- I processi avviati nell'interfaccia della riga di comando adesso sono mostrati nel pannello del processo della cronologia di esecuzione.
- Il pannello del processo ora mostra i processi creati dagli utenti guest aggiunti al tenant di Azure AD.
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

#### <a name="jupyter-notebook"></a>Notebook Jupyter
- La ricerca dei nomi di Notebook ora funziona correttamente nella visualizzazione Notebook.
- È ora possibile eliminare un Notebook nella visualizzazione Notebook.
- Un nuovo e magico `%upload_artifact` è stato aggiunto per caricare i file prodotti nell'ambiente di esecuzione Notebook nell'archivio dati della cronologia di esecuzione.
- Gli errori del kernel sono ora rilevati nello stato del processo Notebook per semplificare il debug.
- Il server Jupyter adesso si arresta correttamente quando l'utente si disconnette dall'app.

#### <a name="azure-portal"></a>Portale di Azure
- L'account di sperimentazione e l'account Gestione modelli ora possono essere creati in due nuove aree di Azure: Europa occidentale e Asia sud-orientale.
- Il piano DevTest dell'account Gestione modelli è ora disponibile solo quando è il primo a essere creato nella sottoscrizione. 
- Il collegamento alla guida nel portale di Azure è stato aggiornato in modo da puntare alla pagina di documentazione corretta.
- Il campo della descrizione è stato rimosso dalla pagina dei dettagli dell'immagine Docker perché non è pertinente.
- Informazioni dettagliate, fra cui AppInsights e le impostazioni di scalabilità automatica, sono state aggiunte alla pagina dei dettagli del servizio Web.
- La pagina di gestione modelli ora viene visualizzata anche se i cookie di terze parti sono disabilitati nel browser. 

#### <a name="operationalization"></a>Operazionalizzazione
- I servizi Web con il "trattino" nel nome non si bloccano più.
- L'utente può creare ora un ambiente di distribuzione con il solo accesso come collaboratore a un gruppo di risorse di Azure o alla sottoscrizione. L'accesso come proprietario all'intera sottoscrizione non è più necessario.
- L'interfaccia della riga di comando di operazionalizzazione ora dispone del completamento automatico con il tasto Tab in Linux.
- Il servizio di creazione di immagini adesso supporta la creazione di immagini per i servizi e dispositivi IoT di Azure.

#### <a name="sample-projects"></a>Progetti di esempio
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


## <a name="2017-10-sprint-0"></a>Sprint 0 10-2017 
**Numero di versione**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

È uscito il primo aggiornamento di Azure Machine Learning Workbench in seguito alla versione di anteprima pubblica iniziale in occasione della conferenza Microsoft Ignite 2017. Gli aggiornamenti principali di questa versione sono costituiti da correzioni relative all'affidabilità e alla stabilizzazione.  Ecco alcuni dei problemi critici risolti:

### <a name="new-features"></a>Nuove funzionalità
- macOS High Sierra è ora supportato

### <a name="bug-fixes"></a>Correzioni di bug
#### <a name="workbench-experience"></a>Esperienza in Workbench
- Un'operazione di trascinamento e rilascio di un file in Workbench causa l'arresto anomalo di Workbench.
- La finestra del terminale in VS Code configurata come IDE per Workbench non riconosce i comandi _az ml_.

#### <a name="workbench-authentication"></a>Autenticazione di Workbench
Sono stati apportati numerosi aggiornamenti per migliorare i vari problemi di accesso e autenticazione segnalati.
- La finestra di autenticazione viene continuamente visualizzata, in particolare quando la connessione Internet non è stabile.
- Sono stati apportati miglioramenti per i problemi di affidabilità relativi alla scadenza del token di autenticazione.
- In alcuni casi, la finestra di autenticazione viene visualizzata due volte.
- La finestra principale di Workbench continua a visualizzare un messaggio che indica che è in corso l'autenticazione anche quando il processo di autenticazione è terminato e la finestra di dialogo popup è già stata chiusa.
- Se non c'è la connessione Internet, la finestra di dialogo di autenticazione viene visualizzata con una schermata vuota.

#### <a name="data-preparation"></a>Preparazione dei dati 
- Quando viene filtrato un valore specifico, vengono filtrati anche gli errori e i valori mancanti.
- La modifica di una strategia di campionamento comporta la rimozione delle successive operazioni di join esistenti.
- La trasformazione di sostituzione dei valori mancanti non prende in considerazione i valori NaN (Not a Number, non un numero).
- L'inferenza del tipo di dati genera un'eccezione quando viene rilevato un valore Null.

#### <a name="job-execution"></a>Esecuzione dei processi
- Non c'è un chiaro messaggio di errore quando, durante l'esecuzione dei processi, non è possibile caricare la cartella di progetto perché è stato superato il limite di dimensioni.
- Se lo script Python dell'utente cambia la directory di lavoro, non viene tenuta traccia dei file scritti nelle cartelle di output. 
- Se la sottoscrizione di Azure attiva è diversa da quella a cui appartiene il progetto corrente, l'invio dei processi provoca un errore 403.
- Quando Docker non è presente, non viene restituito un chiaro messaggio di errore se l'utente cerca di usare Docker come destinazione di esecuzione.
- Il file con estensione runconfig non viene salvato automaticamente quando l'utente fa clic sul pulsante _Esegui_.

#### <a name="jupyter-notebook"></a>Notebook Jupyter
- Non è possibile avviare il server Notebook se l'utente usa determinati tipi di account di ascesso.
- I messaggi di errore del server Notebook non sono presenti nei log visibili all'utente.

#### <a name="azure-portal"></a>Portale di Azure
- Se si seleziona il tema scuro del portale di Azure, il pannello di Gestione modelli viene visualizzato come una casella nera.

#### <a name="operationalization"></a>Operazionalizzazione
- Se si riutilizza un manifesto per aggiornare un servizio Web, viene creata una nuova immagine Docker con un nome casuale.
- Non è possibile recuperare i log del servizio Web dal cluster Kubernetes.
- Viene visualizzato un messaggio di errore fuorviante quando l'utente cerca di creare un account di Gestione modelli o un account di calcolo di ML e si verificano problemi relativi alle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica di [Azure Machine Learning](../service/overview-what-is-azure-ml.md).