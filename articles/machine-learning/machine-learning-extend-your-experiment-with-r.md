<properties
	pageTitle="Estendere l'esperimento con R | Microsoft Azure"
	description="Come estendere le funzionalità di Azure Machine Learning Studio tramite il linguaggio R usando il modulo Execute R Script."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="garye" />


#Estendere l'esperimento con R

È possibile estendere le funzionalità di ML Studio tramite il linguaggio R usando il modulo [Execute R Script][execute-r-script].

Questo modulo accetta più set di dati di input e produce un singolo set di dati come output. È possibile digitare uno script R nel parametro **R Script** del modulo [Execute R Script][execute-r-script].

Per accedere a ogni porta di input del modulo, usare codice simile al seguente:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Elenco di tutti i pacchetti installati attualmente

L'elenco dei pacchetti installati può cambiare. Per ottenere un elenco completo, includere le righe seguenti nel modulo [Execute R Script][execute-r-script] per inviare l'elenco al set di dati di output:

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

Per visualizzare l'elenco di pacchetti, connettere un modulo di conversione, ad esempio [Convert to CSV][convert-to-csv] all'output del modulo [Execute R Script][execute-r-script], eseguire l'esperimento, quindi fare clic sull'output del modulo di conversione e selezionare **Download**.

##Importazione di pacchetti

È anche possibile importare pacchetti non ancora installati da un repository di gestione temporanea di ML Studio usando i comandi seguenti nel modulo [Execute R Script][execute-r-script] e l'archivio di pacchetti compresso:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

dove `my_favorite_package.zip` contiene il file ZIP del pacchetto.

##Elenco dei pacchetti installati

Per praticità, nella tabella seguente è presente l'elenco dei pacchetti inclusi nella versione corrente.

Per ottenere l'elenco completo dei pacchetti attualmente disponibili, vedere la sezione precedente **Elenco di tutti i pacchetti installati attualmente**. La documentazione corrente di R è disponibile [qui](http://cran.r-project.org/manuals.html).

- [Moduli R da A a E]
- [Moduli R da F a L]
- [Moduli R da M a R]
- [Moduli R da M a Z]

[Moduli R da A a E]: #r-modules-beginning-with-a-through-e
[Moduli R da F a L]: #r-modules-beginning-with-f-through-l
[Moduli R da M a R]: #r-modules-beginning-with-m-through-r
[Moduli R da M a Z]: #r-modules-beginning-with-s-through-z


###Moduli R da A a E

| Nome del pacchetto | Descrizione del pacchetto |
| ------------ | ------------------- |
| abc | Strumenti per il calcolo bayesiano approssimato |
| abind | Combinazione di matrici multidimensionali |
| actuar | Funzioni attuariali |
| ade4 | Analisi di dati ecologici: metodi esplorativi ed euclidei nelle scienze ambientali |
| AdMit | Combinazione adattiva di distribuzioni T di Student |
| aod | Analisi di dati sovradispersi |
| ape | Analisi filogenetica e dell'evoluzione |
| approximator | Stima bayesiana di codici computer complessi |
| arm | Analisi dei dati con modelli di regressione e multilivello/gerarchici |
| arules | Data mining di regole di associazione e set di elementi frequenti |
| arulesViz | Visualizzazione di regole di associazione e set di elementi frequenti |
| ash | Routine ASH di David Scott |
| assertthat | Pre e post asserzioni facili |
| AtelieR | Interfaccia grafica utente GTK per l'insegnamento dei concetti di base nell'inferenza statistica e l'esecuzione di test bayesiani elementari |
| BaBooN | Corrispondenza media predittiva del bootstrap bayesiano - Imputazione multipla e singola per dati discreti |
| BACCO | Analisi bayesiana dell'output del codice computer (BACCO) |
| BaM | Funzioni e set di dati per i libri di Jeff Gill |
| bark | Kernel di regressione additiva di Bayes |
| BAS | Media del modello di Bayes tramite campionamento adattivo bayesiano |
| base | Pacchetto R di base |
| BayesDA | Funzioni e set di dati per il libro relativo all'analisi di dati bayesiani |
| bayesGARCH | Stima bayesiana del modello GARCH(1,1) con innovazioni per T di Student |
| bayesm | Inferenza bayesiana per marketing/microeconometria |
| bayesmix | Modelli di combinazioni di Bayes con JAGS |
| bayesQR | Regressione quantile di Bayes |
| bayesSurv | Regressione bayesiana della sopravvivenza con distribuzioni flessibili di errori ed effetti causali |
| Bayesthresh | Modelli bayesiani di effetti misti delle soglie per dati separabili per categorie |
| BayesTree | Metodi bayesiani per modelli basati su albero |
| BayesValidate | Pacchetto BayesValidate |
| BayesX | Utilità R che accompagnano il pacchetto software BayesX |
| BayHaz | Funzioni R per la stima del tasso di pericolo di Bayes |
| bbemkr | Stima bayesiana della larghezza di banda per la regressione di kernel multivariata con errore gaussiano |
| BCBCSF | Classificazione bayesiana con correzione della distorsione e funzioni selezionate |
| BCE | Stimatore della composizione bayesiana: stima della composizione (tassonomica) di esempio dai dati del biomarcatore |
| bclust | Raggruppamento bayesiano che usa il modello gerarchico di tipo Spike e Slab, adatto per il raggruppamento di dati altamente dimensionali |
| bcp | Pacchetto per l'esecuzione di un'analisi bayesiana dei problemi di modifica del punto |
| BenfordTests | Test statistici per la valutazione della conformità alla legge di Benford |
| bfp | Polinomi frazionari bayesiani |
| BH | Aumento priorità dei file di intestazione C++ |
| bisoreg | Regressione isotonica bayesiana con polinomi di Bernstein |
| Bit | Classe per vettori di valori booleani di 1 bit |
| bitops | Operazioni bit per bit |
| BLR | Regressione lineare bayesiana |
| BMA | Media del modello bayesiano |
| Bmix | Campionamento bayesiano per combinazioni di tipo stick-breaking |
| BMS | Libreria delle medie del modello bayesiano |
| bnlearn | Apprendimento struttura di rete, apprendimento di parametri e inferenza di Bayes |
| boa | Programma di analisi dell'output bayesiano per MCMC |
| Bolstad | Funzioni Bolstad |
| boot | Funzioni bootstrap (in origine di Angelo Canty per S) |
| bootstrap | Funzioni per il libro con un'introduzione al bootstrap |
| bqtl | Toolkit di mapping QTL bayesiano |
| BradleyTerry2 | Modelli Bradley-Terry |
| brew | Framework di modelli per la generazione di report |
| brglm | Riduzione della distorsione nei modelli lineari generalizzati con risposta binomiale |
| bspec | Inferenza spettrale bayesiana |
| bspmma | bspmma: modelli semiparametrici bayesiani per meta-analisi |
| BVS | Selezione di varianti bayesiane: tecniche di incertezza del modello bayesiano per studi di associazione genetica |
| cairoDevice | Driver di dispositivo per la grafica con anti-aliasing multipiattaforma basato su Cairo |
| calibrator | Calibrazione bayesiana di codici computer complessi |
| car | Complemento alla regressione applicata |
| caret | Formazione per classificazione e regressione |
| catnet | Inferenza di rete bayesiana categorica |
| caTools | Strumenti: spostamento di statistiche nella finestra, GIF, Base64, ROC AUC e così via. |
| chron | Oggetti cronologici che possono gestire date e ore |
| class | Funzioni per la classificazione |
| cluster | Analisi di raggruppamenti estesa, Rousseeuw et al. |
| clusterSim | Ricerca della procedura di raggruppamento ottimale per un set di dati |
| coda | Analisi di output e diagnostica per MCMC |
| codetools | Strumenti di analisi del codice per R |
| coin | Procedure di inferenza condizionale in un framework di test di permutazione |
| colorspace | Modifica dello spazio colore |
| combinat | Utilità combinatorie |
| compiler | Pacchetto del compilatore R |
| corpcor | Stima efficiente della covarianza e della correlazione (parziale) |
| cslogistic | Regressione logistica specificata condizionalmente |
| ctv | Visualizzazioni di attività CRAN |
| cubature | Integrazione multivariata adattiva su ipercubi |
| data.table | Estensione di data.frame |
| datasets | Pacchetto di set di dati R |
| date | Funzioni per la gestione delle date |
| dclone | Strumenti di clonazione dati e MCMC per metodi della massima verosimiglianza |
| deal | Apprendimento di reti bayesiane con variabili miste |
| Deducer | Deducer: interfaccia utente grafica di analisi dei dati per R |
| DeducerExtras | Finestre di dialogo e funzioni aggiuntive per Deducer |
| deldir | Triangolazione di Delaunay e tassellatura di Dirichlet (Voronoi) |
| DEoptimR | Ottimizzazione dell'evoluzione differenziale in puro R |
| deSolve | Risolutori generali per equazioni differenziali ordinarie (ODE), equazioni differenziali parziali (PDE), equazioni algebriche differenziali (DAE) ed equazioni differenziali con ritardo (DDE) |
| devtools | Strumenti per facilitare lo sviluppo di codice R |
| dichromat | Combinazioni di colori per dicromatici |
| digest | Creazione di digest hash crittografici di oggetti R |
| distrom | Regressione multinomiale distribuita |
| dlm | Analisi di probabilità e bayesiana di modelli lineare dinamici |
| doSNOW | Adattatore parallelo ForEach per il pacchetto snow |
| dplyr | dplyr: grammatica di manipolazione dei dati |
| DPpackage | Modellazione non parametrica bayesiana in R |
| dse | Stima di sistemi dinamici (pacchetto di serie temporali) |
| e1071 | Funzioni varie del Dipartimento di statistica (e1071), TU Vienna |
| EbayesThresh | Determinazione di soglia empirica di Bayes e metodi correlati |
| ebdbNet | Stima empirica di Bayes di reti bayesiane dinamiche |
| effects | Visualizzazioni di effetti per modelli lineari, generalizzati, logit multimoniali, logit di odds proporzionali e modelli di effetti misti |
| emulator | Emulazione bayesiano di programmi per computer |
| ensembleBMA | Previsione probabilistica con insiemi e media del modello bayesiano |
| entropy | Stima dell'entropia, informazioni reciproche e quantità correlate |
| EvalEst | Stima di sistemi dinamica - Estensioni |
| evaluate | Strumenti di analisi e valutazione che forniscono maggiori dettagli rispetto a quelli predefiniti |
| evdbayes | Analisi bayesiana nella teoria dei valori estremi |
| evora | Valori anomali di variabili epigenetiche per analisi della stima del rischio |
| exactLoglinTest | Test esatti con il metodo Monte Carlo per i modelli log-lineari |
| expm | Matrice esponenziale |
| extremevalues | Rilevamento di valori anomali univariati |


###Moduli R da F a L

| Nome del pacchetto | Descrizione del pacchetto |
| ------------ | ------------------- |
| factorQR | Modelli di fattori di regressione quantile di Bayes |
| faoutlier | Metodi di rilevamento di casi di influenza per analisi dei fattori e SEM |
| fitdistrplus | Guida per l'adattamento di una distribuzione parametrica a dati non censurati o censurati |
| FME | Ambiente di modellazione flessibile per modellazione inversa, sensitività, identificabilità e analisi di Monte Carlo |
| foreach | Costrutto del ciclo ForEach per R |
| forecast | Funzioni di previsione per serie temporali e modelli lineari |
| foreign | Lettura di dati archiviati da Minitab, S, SAS, SPSS, Stata, Systat, Weka, dBase, ... |
| formatR | Formattazione automatica del codice R |
| Formula | Formule di modelli estesi |
| fracdiff | Modelli ARIMA differenziati in modo frazionario, noti anche come ARFIMA(p,d,q) |
| gam | Modelli additivi generalizzati |
| gamlr | Regressione lazo gamma |
| gbm | Modelli di regressione con boosting generalizzati |
| gclus | Raggruppamento di elementi grafici |
| gdata | Diversi strumenti di programmazione R per la modifica dei dati |
| gee | Risolutore di equazioni di stima generalizzato |
| genetics | Genetica della popolazione |
| geoR | Analisi dei dati geostatici |
| geoRglm | geoRglm: pacchetto di modelli spaziali lineari generalizzati |
| geosphere | Trigonometria sferica |
| ggmcmc | Strumenti grafici per l'analisi di simulazioni Monte Carlo basati su Catena di Markov da inferenza bayesiana |
| ggplot2 | Implementazione della grammatica della grafica |
| glmmBUGS | Modelli misti lineari generalizzati e modelli spaziali con WinBUGS, BRugs o OpenBUGS |
| glmnet | Modelli lineari generalizzati regolarizzati di tipo lazo o di rete elastica |
| gmodels | Diversi strumenti di programmazione R per l'adattamento dei modelli |
| gmp | Aritmetica a precisione multipla |
| gnm | Modelli non lineari generalizzati |
| googlePublicData | Libreria R per la compilazione di file di metadati DSPL dello strumento di esplorazione dati pubblici di Google |
| googleVis | Interfaccia tra R e i grafici di Google |
| GPArotation | Rotazione del fattore GPA |
| gplots | Diversi strumenti di programmazione R per tracciare i dati |
| graphics | Pacchetto di elementi grafici R |
| grDevices | Dispositivi grafici R e supporto per colori e tipi di carattere |
| gregmisc | Funzioni varie di Greg |
| grid | Pacchetto di elementi grafici della griglia |
| gridExtra | Funzioni nella grafica della griglia |
| growcurves | Modelli della curva di accrescimento non parametrica e semiparametrica di Bayes che includono anche effetti casuali di appartenenza |
| grpreg | Percorsi di regolarizzazione per i modelli di regressione con variabili supplementari raggruppate |
| gsubfn | Utilità per stringhe e argomenti di funzioni |
| gtable | Disposizione di oggetti grafici in tabelle |
| gtools | Vari strumenti di programmazione R |
| gWidgets | API gWidgets per la compilazione di interfacce utente grafiche interattive e indipendenti dal toolkit |
| gWidgetsRGtk2 | Implementazione del toolkit di gWidgets per RGtk2 |
| haplo.stats | Analisi statistica di aplotipi con tratti e variabili supplementari quando la fase di collegamento è ambigua |
| hbsae | Stima gerarchica di una piccola area bayesiana |
| hdrcde | Aree con la più alta densità e stima della densità condizionale |
| heavy | Pacchetto per l'adattamento di valori anomali mediante distribuzioni a coda pesante |
| hflights | Voli partiti da Houston nel 2011 |
| HH | Analisi statistica e visualizzazione dei dati: Heiberger e Holland |
| HI | Simulazione delle distribuzioni supportate da iperpiani annidati |
| highr | Evidenziazione della sintassi per R |
| Hmisc | Varie di Harrell |
| htmltools | Strumenti per HTML |
| httpuv | Libreria HTTP e WebSocket per server |
| httr | Strumenti per l'utilizzo di URL e HTTP |
| IBrokers | API R per Trader Workstation di Interactive Brokers |
| igraph | Analisi di rete e visualizzazione |
| intervals | Strumenti per l'utilizzo di punti e intervalli |
| iplots | iPlots - elementi grafici interattivi per R |
| ipred | Predittori migliorati |
| irr | Vari coefficienti di affidabilità e accordo della stima |
| iterators | Costrutto dell'iteratore per R |
| JavaGD | Dispositivo grafico Java |
| JGR | JGR - Interfaccia utente grafica Java per R |
| kernlab | Lab di Machine Learning basato su kernel |
| KernSmooth | Funzioni per smorzamento del kernel per Wand e Jones (1995) |
| KFKSDS | Filtro di Kalman, regolatore e regolatore di perturbazione |
| kinship2 | Funzioni di ascendenza |
| kknn | Vicini più prossimi k pesati |
| klaR | Classificazione e visualizzazione |
| knitr | Pacchetto di uso generale per la generazione di report dinamici in R |
| ks | Smorzamento del kernel |
| labeling | Applicazione di etichette all'asse |
| Lahman | Database di baseball di Sean Lahman |
| lars | Regressione angolare minima, lazo e progressiva in avanti |
| lattice | Grafica del reticolo |
| latticeExtra | Utilità grafiche aggiuntive basate su reticolo |
| lava | Modelli di variabili latenti lineari |
| lavaan | Analisi di variabili latenti |
| leaps | Selezione del subset di regressione |
| LearnBayes | Funzioni per l'apprendimento dell'inferenza bayesiana |
| limSolve | Modelli di soluzione di inverse lineari |
| lme4 | Modelli di effetti misti lineari con autovalori e S4 |
| lmm | Modelli misti lineari |
| lmPerm | Test di permutazione per modelli lineari |
| lmtest | Test dei modelli di regressione lineare |
| locfit | Regressione locale, probabilità e stima della densità |
| lpSolve | Interfaccia di Lp\_solve v. 5.5 per la risoluzione di programmi lineari/interi |


###Moduli R da M a R

| Nome del pacchetto | Descrizione del pacchetto |
| ------------ | ------------------- |
| magic | Creazione ed esame dei quadrati magici |
| magrittr | magrittr - operatore pipe di redirezione per R |
| mapdata | Database di mappe aggiuntivi |
| mapproj | Proiezioni di mappe |
| maps | Disegno di mappe geografiche |
| maptools | Strumenti per la lettura e la gestione di oggetti spaziali |
| maptree | Mapping, eliminazione e creazione di modelli di albero |
| markdown | Rendering del markdown per R |
| MASS | Supporto di funzioni e set di dati per MASS di Venables e Ripley |
| MasterBayes | Metodi ML e MCMC per la ricostruzione e l'analisi dell'ascendenza |
| Matrix | Metodi e classi di matrici di tipo sparse e dense |
| matrixcalc | Raccolta di funzioni per i calcoli della matrice |
| MatrixModels | Creazione di modelli con matrici di tipo sparse e dense |
| maxent | Regressione logistica multinomiale con consumo di memoria ridotto e supporto per la classificazione del testo |
| maxLik | Stima delle probabilità massima |
| mcmc | Monte Carlo basato su Catena di Markov |
| MCMCglmm | Modelli MCMC misti lineari generalizzati |
| MCMCpack | Pacchetto Monte Carlo basato su Catena di Markov (MCMC) |
| memoise | Funzioni memoize |
| methods | Metodi e classi formali |
| mgcv | Veicolo di calcolo GAM misto con stima dello smorzamento GCV/AIC/REML |
| mice | Attribuzione multivariata da equazioni concatenate |
| microbenchmark | Funzioni di temporizzazione accurate al submicrosecondo |
| mime | Nomi file di mappe per i tipi MIME |
| minpack.lm | Interfaccia R per l'algoritmo dei minimi quadrati non lineari di Levenberg-Marquardt disponibile in MINPACK, oltre al supporto per i limiti |
| minqa | Algoritmi di ottimizzazione senza derivate per approssimazione quadratica |
| misc3d | Vari tracciati 3D |
| miscF | Funzioni varie |
| miscTools | Vari strumenti e utilità |
| mixtools | Strumenti per l'analisi dei modelli di combinazioni finite |
| mlbench | Problemi di benchmark di Machine Learning |
| mlogitBMA | Media del modello bayesiano per modelli di logit multinomiale |
| mnormt | Distribuzioni T e normali multivariate |
| MNP | Pacchetto R per l'adattamento del modello di probit multinomiale |
| modeltools | Strumenti e classi per modelli statistici |
| mombf | Fattori di Bayes del momento e del momento inverso |
| monomvn | Stima per dati T di Student e normali multivariati con assenza monotona |
| monreg | Regressione monotona non parametrica |
| mosaic | Utilità per l'insegnamento di matematica e statistica del progetto MOSAIC (mosaic-web.org) |
| MSBVAR | Modelli di autoregressione vettoriale bayesiana con passaggio a Markov |
| msm | Modelli Markov con più stati e nascosti a tempo continuo |
| multcomp | Inferenza simultanea in modelli parametrici generali |
| multicool | Permutazione di tipo multiset nell'ordine cool-lex. |
| munsell | Sistema dei colori Munsell |
| mvoutlier | Rilevamento di valori anomali multivariati basati su metodi solidi |
| mvtnorm | Distribuzioni T e normali multivariate |
| ncvreg | Percorsi di regolarizzazione per modelli di regressione penalizzati da SCAD e MCP |
| nlme | Modelli di effetti misti lineari e non lineari |
| NLP | Infrastruttura di elaborazione in linguaggio naturale |
| nnet | Reti neurali feedforward e modelli log-lineari multinomiali |
| numbers | Funzioni numerico-teoriche |
| numDeriv | Derivate numeriche accurate |
| openNLP | Interfaccia degli strumenti Apache OpenNLP |
| openNLPdata | File JAR di Apache OpenNLP e modelli di base in lingua inglese |
| OutlierDC | Rilevamento di valori anomali usando la regressione quantile per dati censurati |
| OutlierDM | Rilevamento di valori anomali per dati replicati a velocità effettiva elevata |
| outliers | Test per valori anomali |
| pacbpred | Stima e previsione PAC-bayesiana in modelli additivi di tipo sparse |
| parallel | Supporto per il calcolo parallelo in R |
| partitions | Partizioni additive di interi |
| party | Laboratorio per il partitioning ricorsivo |
| PAWL | Implementazione dell'algoritmo PAWL |
| pbivnorm | CDF normale bivariata vettorizzata |
| pcaPP | PCA affidabile tramite la tecnica statistica Projection Pursuit |
| permute | Funzioni per la generazione di permutazioni di dati limitate |
| pls | Quadrati minimi parziali e regressione di componenti principali |
| plyr | Strumenti per dividere, applicare e combinare dati |
| png | Lettura e scrittura di immagini PNG |
| polynom | Raccolta di funzioni per implementare una classe per le modifiche polinomiali univariate |
| PottsUtils | Funzioni di utilità per i modelli di Potts |
| predmixcor | Regola di classificazione basata su modelli di combinazioni di Bayes con selezione di funzioni con correzione della distorsione |
| PresenceAbsence | Valutazione del modello di presenza-assenza |
| prodlim | Stima dei limiti del prodotto Metodo Kaplan-Meier e Aalen-Johansson per l'analisi della cronologia (sopravvivenza) di eventi censurati |
| profdpm | Combinazioni di elaborazione dei profilo Dirichlet |
| profileModel | Strumenti per la profilatura delle funzioni di inferenza per diverse classi di modelli |
| proto | Programmazione di prototipi basati su oggetti |
| pscl | Political Science Computational Laboratory della Stanford University |
| psych | Procedure per ricerche psicologiche, psicometriche e della personalità |
| quadprog | Funzioni per risolvere i problemi di programmazione quadratica |
| quantreg | Regressione quantile |
| qvcalc | Quasi varianze per gli effetti dei fattori nei modelli statistici |
| R.matlab | Lettura e scrittura di file MAT insieme alla connettività da R a MATLAB |
| R.methodsS3 | Funzione di utilità per la definizione di metodi S3 |
| R.oo | Programmazione R orientata a oggetti con o senza riferimenti |
| R.utils | Varie utilità di programmazione |
| R2HTML | Esportazione in HTML per gli oggetti R |
| R2jags | Pacchetto per l'esecuzione di file JAG da R |
| R2OpenBUGS | Esecuzione di OpenBUGS da R |
| R2WinBUGS | Esecuzione di WinBUGS e OpenBUGS da R/S-PLUS |
| ramps | Modellazione geostatistica bayesiana con RAMPS |
| RandomFields | Simulazione e analisi di campi casuali |
| randomForest | Foreste casuali di Breiman e Cutler per la classificazione e la regressione |
| RArcInfo | Funzioni per importare dati da coperture binarie di Arc/Info V7.x |
| raster | raster: analisi e modellazione di dati geografici |
| rbugs | Fusione di R e OpenBugs e oltre |
| RColorBrewer | Tavolozze ColorBrewer |
| Rcpp | Integrazione diretta di R e C++ |
| RcppArmadillo | Integrazione di Rcpp per la libreria di algebra lineare basata su modelli Armadillo |
| rcppbugs | Binding R per cppbugs |
| RcppEigen | Integrazione di Rcpp per la libreria di algebra lineare basata su modelli Eigen |
| RcppExamples | Esempi d'uso di Rcpp per l'interfaccia di R e C++ |
| RCurl | Interfaccia client (HTTP/FTP/...) di rete generale per R |
| relimp | Contributo relativo di effetti in un modello di regressione |
| reshape | Flessibilità di modifica della forma dei dati |
| reshape2 | Flessibilità di modifica della forma dei dati: riavvio del pacchetto di modifica della forma |
| rgdal | Binding per la libreria di astrazione dei dati geospaziali |
| rgeos | Interfaccia per Geometry Engine - Open Source (GEOS) |
| rgl | Sistema del dispositivo di visualizzazione 3D (OpenGL) |
| RGraphics | Dati e funzioni dal libro sugli elementi grafici R, seconda edizione |
| RGtk2 | Binding R per Gtk 2.8.0 e versioni successive |
| RJaCGH | Salto MCMC reversibile per l'analisi di matrici CGH |
| rjags | Modelli grafici bayesiani che usano MCMC |
| rJava | Interfaccia da R a Java di basso livello |
| RJSONIO | Serializzazione di oggetti R in JSON, JavaScript Object Notation |
| robCompositions | Stima affidabile per dati strutturali |
| robustbase | Statistiche di base affidabili |
| RODBC | Accesso al Database ODBC |
| rootSolve | Analisi non lineare di ricerca della radice, dell'equilibrio e dello stato stazionario di equazioni differenziali ordinarie |
| roxygen | Programmazione avanzata in R |
| roxygen2 | Documentazione nel codice sorgente per R |
| rpart | Partizionamento ricorsivo e alberi di regressione |
| rrcov | Strumenti di stima scalabili e affidabili con punto di scomposizione elevato |
| rscproxy | statconn: fornisce un'interfaccia in stile C portabile per R (StatConnector) |
| RSGHB | Funzioni per la stima bayesiana gerarchica: un approccio flessibile |
| RSNNS | Reti neurali in R tramite il simulatore SNNS (Stuttgart Neural Network Simulator) |
| RTextTools | Classificazione automatica del testo tramite apprendimento supervisionato |
| RUnit | Framework di test dell'unità R |
| runjags | Utilità dell'interfaccia, metodi di elaborazione parallela e distribuzioni aggiuntive per i modelli MCMC in JAGS |
| Runuran | Interfaccia R per i generatori di variabili casuale UNU.RAN |
| rworldmap | Mapping di dati globali, vettori e raster |
| rworldxtra | Contini di paesi ad alta risoluzione |


###Moduli R da M a Z

| Nome del pacchetto | Descrizione del pacchetto |
| ------------ | ------------------- |
| SampleSizeMeans | Calcoli delle dimensioni del campione per medie normali |
| SampleSizeProportions | Calcolo dei requisiti di dimensioni del campione durante la stima della differenza tra due proporzioni binomiali |
| sandwich | Affidabili di stima della matrice di covarianza |
| sbgcop | Stima e attribuzione della copula bayesiana gaussiana semiparametrica |
| scales | Funzioni di scalabilità per la grafica |
| scapeMCMC | Tracciati diagnostici MCMC |
| scatterplot3d | Grafico a dispersione 3D |
| sciplot | Funzioni grafiche scientifiche per la progettazione fattoriale |
| segmented | Relazioni segmentate nei modelli di regressione con stima dei punti di interruzione/di modifica |
| sem | Modelli di equazione strutturale |
| seriation | Infrastruttura di seriazione |
| setRNG | Impostazione del generatore di numeri casuali (normali) e seeding |
| sgeostat | Framework orientato a oggetti per la modellazione geostatistica in S+ |
| shapefiles | Lettura e scrittura di file di forma ESRI |
| shiny | Framework dell'applicazione Web per R |
| SimpleTable | Analisi di inferenza e sensitività per effetti casuali da tabelle K 2 x 2 e 2 x 2 x K in presenza di effetti di confondimento non misurati |
| slam | Array e matrici lightweight di tipo sparse |
| smoothSurv | Regressione di sopravvivenza con distribuzione di errori regolarizzata |
| sna | Strumenti per l'analisi di social network |
| snow | Semplice rete di workstation |
| SnowballC | Stemmer Snowball basati sulla libreria UTF-8 di libstemmer C |
| snowFT | Semplice rete di workstation a tolleranza di errore |
| sp | Classi e metodi per dati spaziali |
| spacetime | Classi e metodi per dati spazio-temporali |
| SparseM | Algebra lineare di tipo sparse |
| spatial | Funzioni per analisi di modelli di punti e kriging |
| spBayes | Modellazione spaziale-temporale univariata e multivariata |
| spdep | Dipendenza spaziale: peso di schemi, statistich e modelli |
| spikeslab | Stima e selezione di variabili con la regressione di tipo Spike e Slab |
| splancs | Analisi dei modelli di punti spaziali e spazio-temporali |
| splines | Classi e funzioni spline di regressione |
| spTimer | Modellazione bayesiana spazio-temporale con R |
| Statistiche | Pacchetto di statistiche R |
| stats4 | Funzioni statistiche con le classi S4 |
| stochvol | Efficiente inferenza bayesiana per i modelli di volatilità stocatica (SV) |
| stringr | Semplificazione dell'uso delle stringhe |
| strucchange | Test, monitoraggio e datazione di modifiche strutturali |
| stsm | Modelli di serie temporali strutturali |
| stsm.class | Classi e metodi per modelli di serie temporali strutturali |
| SuppDists | Distribuzioni supplementari |
| survival | Analisi di sopravvivenza |
| svmpath | svmpath: algoritmo SVM Path |
| tau | Utilità di analisi del testo |
| tcltk | Interfaccia Tcl/Tk |
| tcltk2 | Aggiunte Tcl/Tk |
| TeachingDemos | Dimostrazioni per insegnamento e apprendimento |
| tensorA | Operazioni aritmetiche tensoriali avanzate con indici denominati |
| testthat | Codice Testthat Strumenti per facilitare i test |
| textcat | Categorizzazione di testo basata su n-gramma |
| textir | Regressione inversa per l'analisi del testo |
| tfplot | Utilità utente per gli intervalli di tempo |
| tframe | Kernel di codifica degli intervalli di tempo |
| tgp | Modelli di processi gaussiani di treed bayesiani |
| TH.data | Archivio dati di TH |
| timeDate | Rmetrics - Oggetti cronologici e del calendario |
| tm | Pacchetto di data mining del testo |
| tools | Strumenti per lo sviluppo di pacchetti |
| translations | Pacchetto di conversioni R |
| tree | Alberi di classificazione e regressione |
| tseries | Analisi di serie temporali e finanza computazionale |
| tsfa | Analisi di fattori delle serie temporali |
| tsoutliers | Rilevamento automatico di valori anomali nelle serie temporali |
| TSP | Problema del venditore in viaggio |
| UsingR | Set di dati per il testo che usa R per le statistiche introduttive |
| utils | Pacchetto di utilità R |
| varSelectIP | Selezione del modello di Bayes oggettivo |
| vcd | Visualizzazione di dati categorici |
| vegan | Pacchetto per la community di ecologisti |
| VGAM | Modelli vettoriali additivi e lineari generalizzati |
| VIF | Regressione VIF: algoritmo di regressione veloce per dati di grandi dimensioni |
| whisker | {{mustache}} per R, creazione di modelli senza logica |
| wordcloud | Cloud di Word |
| XLConnect | Connettore di Excel per R |
| XML | Strumenti per l'analisi e la generazione di codice XML in R e S-Plus |
| xtable | Tabelle di esportazione in LaTeX o HTML |
| xts | Serie temporali eXtensible |
| yaml | Metodi per la conversione di dati R in YAML e viceversa |
| zic | Inferenza bayesiana per modelli di conteggio non inflazionati |
| zipfR | Modelli statistici per distribuzioni della frequenza di parole |
| zoo | Infrastuttura S3 per serie temporali regolari e irregolari (osservazioni con ordinamento Z) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=Oct15_HO3-->