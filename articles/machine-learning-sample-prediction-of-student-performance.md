<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Machine Learning Sample: Predict student performance | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts student performance on tests." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Esempio di Azure Machine Learning: Previsione delle prestazioni degli studenti

*L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:*

    Sample Experiment - Student Performance - Development

## Set di dati e descrizione del problema

Per questo esperimento il set di dati è il set di training Algebra\_2008\_2009 dalla KDD Cup del 2010. Per scaricare questo set di dati, accedere all'indirizzo [][]<https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp></a>. Il set di dati contiene i file di log del sistema di tutoring degli studenti. Le funzionalità fornite includono: ID del problema e breve descrizione, ID dello studente, timestamp e numero di tentativi effettuati dallo studente prima di risolvere il problema nel modo corretto. Il problema di apprendimento consiste nel prevedere se uno studente risolverà il problema al primo tentativo. Seguendo le regole della KDD Cup, viene misurata l'accuratezza degli algoritmi di apprendimento tramite la [Radice dell'errore quadratico medio][Radice dell'errore quadratico medio]. Il set di dati originale contiene 8,9 milioni di record. Per accelerare l'esperimento il campione del set di dati è stato ridotto alle prime 100.000 righe. Nel set di dati sono presenti 23 colonne di vario tipo: numerico, categorico e timestamp. Le colonne sono separate da tabulazioni

## Flusso di lavoro per lo sviluppo

Il set di dati contiene caratteri non ASCII che non possono essere gestiti dal modulo **Applica operazione R**. Prima di usare il set di dati in Passau, sono stati rimossi i caratteri non ASCII usando i comandi di PowerShell seguenti:

    gc algebra_2008_2009_train.txt –head 100000 | sc algebra_train_small.txt
    sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
    cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

Il file algebra\_train\_small.txt\_ascii risultante è ancora molto grande (36 milioni). Il file viene archiviato nell'archiviazione BLOB di Azure e quindi caricato nell'esperimento tramite il modulo **Lettore**. I comandi di PowerShell per archiviare il file nell'archiviazione BLOB sono:

    Add-AzureAccount
    $key = Get-AzureStorageKey -StorageAccountName <your storage account name>
    $ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
    Set-AzureStorageBlobContent –Container <container name in your storage account> -File "algebra_train_small.txt_ascii" –Context $ctxt

![][]

I parametri del modulo **Lettore** sono visualizzati sopra. In questo esempio il nome dell'account di archiviazione è "datascience" e il file del set di dati algebra\_train\_small.txt\_ascii viene inserito nel contenitore "sampleexperiments". La chiave dell'account è una chiave di accesso dell'account di archiviazione di Azure. È possibile recuperare la chiave dell'account nel portale di gestione di Azure ([][1]<https://manage.windowsazure.com></a>).

![][2]

Nei passaggi successivi, mostrati sopra, vengono eseguite diverse trasformazioni per convertire il set di dati nel formato adatto agli algoritmi di apprendimento. Per convertire la colonna timestamp "First Transaction Time" in formato stringa, viene usato il modulo **Editor metadati**. Questa operazione è necessaria per generare la divisione apprendimento/test. Quindi, tramite il modulo **Colonne progetto** si procede alla rimozione delle colonne che non verranno usate per l'esperimento. Viene usato lo strumento di pulitura dei valori mancanti per sostituire tutti i valori mancanti con 0. Nel passaggio successivo la colonna "Unit Name, Section Name" viene divisa in due colonne, ovvero "Unit Name" e "Section Name". Questa operazione viene eseguita tramite il codice R seguente nel modulo **Esecuzione R**.

    dataset <- maml.mapInputPort(1)
    b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
    names(b) <- c("Unit Name","Section Name")
    data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
    maml.mapOutputPort("data.set")  

I primi passaggi dell'esperimento che prevedono il caricamento dei dati e le trasformazioni iniziali sono mostrati sopra. Dopo avere modificato i dati, l'esperimento viene suddiviso in 4 rami. In ogni ramo viene testato un set di funzionalità diverso. Alcuni set di funzionalità sono stati usati in precedenza da [1][1]. Nel primo ramo, illustrato di seguito, il set di funzionalità è costituito dalle funzionalità StudentID, Unit

![][3]

![][4]

Name, Section Name, Problem Name, Problem View, Opportunity e Textual Description, in cui viene fornita una descrizione testuale del problema. Le funzionalità non integer vengono rappresentate come funzionalità categoriche. In questo ramo verrà rimossa la colonna Hints tramite il modulo **Colonne progetto** perché questa colonna non è disponibile per la previsione dell'esito del nuovo studente. Si usa quindi il modulo **Dividi** per dividere il set di dati in set di training e set di test. Poiché tutti gli esempi hanno un 
timestamp, la divisione sarà basata sul tempo. Tutte le righe di First Transaction Time relative al 2008 saranno incluse nel set di training, quelle relative al 2009 nel set di test. I parametri del modulo **Dividi** sono visualizzati a destra. Dopo la divisione, verrà generato un modello di classificazione binario tramite l'albero delle decisioni con boosting e verrà assegnato un punteggio ai dati di test. La colonna etichetta nel modulo **Modello di training** è Correct First Attempt.

![][5]

Nella sequenza successiva dei moduli **Applica operazione matematica** e **Statistiche elementari**, mostrati sopra, viene calcolata la radice dell'errore quadratico medio del modello usando punteggi non elaborati ed etichette True. Si noti che per i modelli di regressione questa metrica viene calcolata dal modulo **Analizzatore**, ma per gli altri modelli è necessario calcolare questa metrica manualmente. Nel primo modulo **Applica operazione matematica** viene calcolata la differenza tra la colonna etichetta e la colonna punteggio generata dal modulo **Modello di punteggio**. Nel secondo modulo **Applica operazione matematica** viene calcolato il quadrato della differenza. Nel modulo **Statistiche elementari** viene invece calcolata la media delle differenze quadratiche. Infine, l'ultimo modulo **Applica operazione matematica** viene usato per il calcolo della radice quadrata. I parametri di questi quattro moduli sono visualizzati di seguito.

![][6]

![][7]

![][8]

![][9]

Nel secondo ramo, mostrato sopra, viene usata anche la dimensione temporale. Oltre alle funzionalità del primo ramo, vengono usati i nomi degli ultimi due passaggi del problema attualmente risolto dall'utente, unitamente alle relative descrizioni. Nel set di dati fornito, tutte le attività dell'utente sono archiviate in base al timestamp in ordine crescente. Nel set di dati fornito, le attività dell'utente sono senza interfoliazione, ovvero sono presentate innanzitutto tutte le righe del primo utente, quindi tutte le righe del secondo utente e così via. Di conseguenza, per individuare gli ultimi passaggi dell'utente, è necessario usare la colonna RowID. Per un utente fisso, questa colonna funge da asse temporale. Se si aggiunge 1 a questa colonna usando Applica operazione matematica\*\*\*\*, è possibile spostare ogni riga in avanti di un'unità di tempo. Il modulo **Join** consentirà quindi di unire il set di dati originale e quello spostato usando RowID, StudentID e ProblemName come chiavi. Si otterrà un set di dati espanso nel quale ogni riga include un record temporale t e t-1 per lo stesso StudentID e ProblemName. *Left Outer Join* consente di mantenere le righe che non contengono passaggi precedenti con gli stessi StudentID e ProblemID. Si applica quindi la combinazione dei moduli **Applica operazione matematica** e **Join** ancora una volta per ottenere le funzionalità relative a due passaggi fa. I parametri esatti di **Applica operazione matematica** e **Join** sono visualizzati di seguito.

![][10]

![][11]

![][12]

Dopo l'esecuzione di questa seconda operazione, si ottengono diverse colonne identiche. Ad esempio, a seguito dell'utilizzo del modulo Join, la colonna ProblemName è stata replicata tre volte, per i passaggi t, t-1 e t-2. Per rimuovere le colonne in eccesso, viene usato il modulo **Colonne progetto**. Infine, poiché è stata usata la funzione *Left Outer Join*, è possibile che per alcune righe generate dall'operatore Join i valori siano mancanti. Per sostituire tutti i valori mancanti con la stringa "0" verrà usato il modulo **Strumento di pulitura valori mancanti**. I parametri del modulo **Strumento di pulitura valori mancanti** sono visualizzati sopra.

Dopo avere calcolato il nuovo set di funzionalità, il flusso di lavoro del secondo ramo, è identico a quello del primo.

![][13]

Nel terzo ramo, oltre alle funzionalità usate nel secondo ramo, verranno usate anche le funzionalità quadratiche e cubiche che costituiscono le concatenazioni delle funzionalità originali del primo ramo. Le funzionalità quadratiche e cubiche vengono calcolate con il modulo **Esecuzione operazione R** con il codice R visualizzato sopra. Dopo avere calcolato il nuovo set di funzionalità, è possibile procedere al training, all'assegnazione del punteggio e alla valutazione, esattamente come nei primi due rami.

Nel quarto ramo vengono usate funzionalità completamente diverse da quelle dei primi tre. Per ogni StudentID viene calcolato il valore medio di Correct First Attempt fino al valore temporale t (escluso). Questo valore viene indicato come CFA(StudentID,t). Allo stesso modo, Hints(StudentID,t) indica il valore medio di Hints per uno StudentID fisso fino al valore temporale t (escluso). Per accelerare il calcolo di questi valori medi, anziché prendere in considerazione l'intero set di dati, vengono usati i 10 record più recenti prima del valore temporale t. Le definizioni di CFA(Problem Name,t), CFA(Step Name,t), CFA(Description,t), Hints(Problem Name,t), Hints(Step Name,t) e Hints(Description,t) sono simili. Dato un esempio x con First Transaction Time=t(x) e i valori delle colonne StudentID(x), Problem Name(x), Step Name(x) and Description(x), vengono generate le 8 funzionalità CFA e Hints:

    CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
    Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]

Allo stesso modo, vengono calcolate 8 funzionalità CFA e Hints aggiuntive usando le concatenazioni di StudentID e ProblemName, ProblemName e StepName, StudentID e UnitName e StudentID e ProblemDescription. Si ottengono quindi 16 funzionalità che vengono usate per prevedere il valore della colonna Correct First Attempt. Il calcolo di queste 16 funzionalità viene eseguito usando il codice R all'interno del modulo **Esecuzione operazione R**. Questo codice è lungo e noioso, ma altamente ottimizzato. Dopo avere calcolato le funzionalità, si procederà alla rimozione di alcune colonne ausiliarie aggiunte dal codice R. A questo scopo si usa il modulo **Colonne progetto**. Il flusso di lavoro completo per le funzionalità di calcolo nel quarto ramo è mostrato sopra. Dopo avere calcolato il nuovo set di funzionalità, è possibile procedere al training, all'assegnazione del punteggio e alla valutazione, esattamente come nei primi tre rami.

Dopo avere calcolato i valori della radice dell'errore quadratico medio in tutti e quattro i rami, vengono raccolti i risultati usando il modulo **Aggiungi riga**. Vengono anche generate le annotazioni usando il modulo **Esecuzione R**. Il flusso di lavoro di questa parte finale dell'esperimento viene descritto di seguito.

![][15]

L'output finale dell'esperimento è costituito dalla tabella seguente, dove la prima colonna indica il nome del set di funzionalità e la seconda riga corrisponde alla radice dell'errore quadratico medio misurata negli esempi di test:

![][16]

La conclusione è che il quarto set di funzionalità presenta la radice dell'errore quadratico medio più bassa.

## Riferimenti

H.-F. Yu et al. Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010.

  []: https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp
  [Radice dell'errore quadratico medio]: http://en.wikipedia.org/wiki/Root-mean-square_deviation
  []: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
  [1]: https://manage.windowsazure.com
  [2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
  [3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
  [4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
  [5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
  [6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
  [7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
  [8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
  [9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
  [10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
  [11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
  [12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
  [13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
  [14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
  [15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
  [16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
