---
title: Eseguire la migrazione dei dati di analisi da Excel ad Azure Machine Learning Studio titleSuffix: Descrizione di Azure Machine Learning Studio: Un confronto dei modelli di regressione lineare in Excel e in Azure Machine Learning Studio: services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 20/03/2017
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Eseguire la migrazione dei dati di analisi da Excel ad Azure Machine Learning Studio

> *Kate Baroni* e *Ben Boatman* sono architetti di soluzioni aziendali in Microsoft Data Insights Center of Excellence. In questo articolo descrivono la loro esperienza durante la migrazione di una suite esistente per l'analisi di regressione a una soluzione basata sul cloud tramite Azure Machine Learning Studio.

## <a name="goal"></a>Obiettivo

Il progetto è iniziato con due obiettivi prefissati: 

1. Usare l'analisi predittiva per migliorare la precisione delle proiezioni dei ricavi mensili dell'organizzazione. 
2. Usare Azure Machine Learning Studio per confermare, ottimizzare e migliorare la velocità e la scalabilità dei risultati. 

Come molte aziende, anche questa organizzazione esegue un processo di previsione dei ricavi mensile. A un piccolo team di analisti aziendali è stato assegnato il compito di usare Azure Machine Learning Studio per supportare il processo e migliorare la precisione delle previsioni. Per diversi mesi, il team ha raccolto dati da più origini e ha eseguito diversi attributi dei dati usando l'analisi statistica per identificare gli attributi chiave rilevanti per la previsione delle vendite di servizi. Il passaggio successivo prevedeva la creazione di prototipi di modelli di regressione statistica basati sui dati in Excel. In poche settimane è stato prodotto un modello di regressione di Excel con prestazioni superiori rispetto ai processi correnti di previsione finanziaria e di campo. Questo è diventato il risultato di previsione di base. 

È stato quindi avviato il passaggio dell'analisi predittiva a Studio per scoprire in che modo Studio potesse migliorare le prestazioni predittive.

## <a name="achieving-predictive-performance-parity"></a>Raggiungimento della parità delle prestazioni predittive
La prima priorità consisteva nel raggiungere la parità tra i modelli di regressione di Studio ed Excel. In base agli stessi dati e alla stessa suddivisione dei dati di training e di test, si voleva raggiungere la parità delle prestazioni predittive tra Excel e Studio. Inizialmente, l'obiettivo non è stato raggiunto. Le prestazioni del modello di Excel superavano quelle di Studio. L'errore era dovuto alla scarsa comprensione dell'impostazione dello strumento di base in Studio. Dopo una sincronizzazione con il team del prodotto di Studio, è stato raggiunto un maggior livello di comprensione dell'impostazione di base richiesta per gli specifici set di dati ed è stata ottenuta la parità tra i due modelli. 

### <a name="create-regression-model-in-excel"></a>Creare un modello di regressione in Excel
La regressione di Excel usava il modello di regressione lineare standard disponibile in Strumenti di analisi di Excel. 

È stato calcolato l' *Errore assoluto medio (%)* , usato come misura delle prestazioni per il modello. Sono stati necessari tre mesi per arrivare al modello di lavoro usando Excel. Gran parte delle informazioni è stata trasferita nell'esperimento di Studio che ha consentito una migliore comprensione dei requisiti.

### <a name="create-comparable-experiment-in-studio"></a>Creare un esperimento comparabile in Studio
Per creare l'esperimento in Studio sono stati seguiti questi passaggi: 

1. È stato caricato il set di dati in Studio sotto forma di file CSV (un file di dimensioni molto ridotte)
2. È stato creato un nuovo esperimento ed è stato usato il modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) per selezionare le stesse funzionalità di dati usate in Excel 
3. È stato usato il modulo [Split data][split] (Divisione dati) con modalità *Relative Expression* (Espressione relativa) per dividere i dati negli stessi set di dati di training di Excel 
4. È stato eseguito l'esperimento con il modulo [Linear Regression][linear-regression] (Regressione lineare) (solo opzioni predefinite), quindi sono stati documentati e confrontati i risultati nel modello di regressione di Excel

### <a name="review-initial-results"></a>Rivedere i risultati iniziali
All'inizio, il modello di Excel ha superato nettamente le prestazioni del modello di Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Prestazioni | | |
| <ul style="list-style-type: none;"><li>R-quadrato corretto</li></ul> |0,96 |N/D |
| <ul style="list-style-type: none;"><li>Coefficiente di <br />determinazione</li></ul> |N/D |0,78<br />(bassa precisione) |
| Errore assoluto medio |$ 9,5 milioni |$ 19,4 milioni |
| Errore assoluto medio (%) |6,03% |12,2% |

Il processo e i risultati sono stati sottoposti agli sviluppatori e ai data scientist del team di Machine Learning, che ha subito fornito alcuni suggerimenti utili. 

* Quando si usa il modulo [Linear Regression][linear-regression] (Regressione lineare) in Studio, vengono forniti due metodi.
  * Online Gradient Descent: questo può essere il metodo più adatto a problemi su larga scala.
  * Ordinary Least Squares: questo è il metodo generalmente associato alla regressione lineare. Per set di dati di piccole dimensioni, il metodo Ordinary Least Squares è solitamente la scelta ottimale.
* Provare a modificare il parametro L2 Regularization Weight per migliorare le prestazioni. Per impostazione predefinita è impostato su 0.001, ma per i set di dati di piccole dimensioni in questo caso è stato impostato su 0.005 per migliorare le prestazioni. 

### <a name="mystery-solved"></a>Misero risolto
Dopo aver applicato i suggerimenti, in Studio sono state raggiunte le stesse prestazioni di base di Excel: 

|  | Excel | Studio (iniziale) | Studio con Least Squares |
| --- |:---:|:---:|:---:|
| Valore etichettato |Valori effettivi (numerici) |uguale |uguale |
| Strumento di apprendimento |Excel -> Analisi dati -> Regressione |Regressione lineare. |Linear Regression |
| Opzioni strumento di apprendimento |N/D |Valori predefiniti |Ordinary Least Squares<br />L2 = 0,005 |
| Set di dati |26 righe, 3 funzionalità, 1 etichetta. Tutti valori numerici. |uguale |uguale |
| Divisione: Eseguire il training |Excel con training sulle prime 18 righe, con test delle ultime 8 righe. |uguale |uguale |
| Divisione: Test |Formula di regressione Excel applicata alle ultime 8 righe |uguale |uguale |
| **Prestazioni** | | | |
| R-quadrato corretto |0,96 |N/D | |
| Coefficiente di determinazione |N/D |0,78 |0,952049 |
| Errore assoluto medio |$ 9,5 milioni |$ 19,4 milioni |$ 9,5 milioni |
| Errore assoluto medio (%) |<span style="background-color: 00FF00;"> 6,03%</span> |12,2% |<span style="background-color: 00FF00;"> 6,03%</span> |

Inoltre, i coefficienti di Excel hanno dato buoni risultati anche confrontati con i pesi delle funzionalità nel modello con training di Azure:

|  | Coefficienti di Excel | Pesi delle funzionalità di Azure |
| --- |:---:|:---:|
| Intercetta/distorsione |19470209,88 |19328500 |
| Funzionalità A |0,832653063 |0,834156 |
| Funzionalità B |11071967,08 |11007300 |
| Funzionalità C |25383318,09 |25140800 |

## <a name="next-steps"></a>Passaggi successivi
L'obiettivo, a questo punto, consisteva nell'includere il servizio Web di Machine Learning in Excel. Gli analisti aziendali usano Excel in modo estensivo, era quindi necessaria una soluzione che chiamasse il servizio Web di Machine Learning con una riga di dati di Excel e restituisse il valore previsto in Excel. 

Si voleva inoltre ottimizzare il modello usando le opzioni e gli algoritmi disponibili in Studio.

### <a name="integration-with-excel"></a>Integrazione con Excel
La soluzione consisteva nel rendere operativo il modello di regressione di Machine Learning creando un servizio Web dal modello con training. Questo servizio Web è stato creato in pochi minuti ed è stato possibile chiamarlo direttamente da Excel per restituire un valore dei ricavi previsti. 

La sezione del *dashboard dei servizi Web* include una cartella di lavoro di Excel scaricabile. La cartella di lavoro viene fornita preformattata con l'API del servizio Web e le informazioni sullo schema incorporate. Quando si fa clic su *Download Excel Workbook* (Scarica cartella di lavoro di Excel), la cartella di lavoro si apre ed è possibile salvarla nel computer locale. 

![][1]

Con la cartella di lavoro aperta, copiare i parametri predefiniti nella sezione Parameter di colore blu come mostrato di seguito. Dopo aver immesso i parametri, Excel chiama il servizio Web di Machine Learning e le etichette con il punteggio previsto vengono visualizzate nella sezione Predicted Values (Valori previsti) di colore verde. La cartella di lavoro continua a creare previsioni per i parametri in base al modello con training per tutti gli elementi di riga immessi in Parameters. Per altre informazioni su come usare questa funzionalità, vedere [Utilizzo di un servizio Web Azure Machine Learning da Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Ottimizzazione e altri esperimenti
Con una base disponibile con il modello di Excel, è stato possibile passare all'ottimizzazione del modello di regressione lineare di Machine Learning. È stato usato il modulo [Filter-Based Feature Selection][filter-based-feature-selection] (Selezione di funzionalità in base al filtro) per ottimizzare la selezione degli elementi dei dati iniziali e ottenere un miglioramento delle prestazioni del 4,6% dell'errore assoluto medio. Per i progetti futuri verrà usata questa funzionalità che consente di risparmiare settimane, normalmente impiegate nell'iterazione degli attributi dei dati per individuare il set di funzionalità più adatto per la modellazione. 

È prevista l'aggiunta di altri algoritmi all'esperimento, ad esempio quelli [bayesiani][bayesian-linear-regression] o gli [alberi delle decisioni con boosting][boosted-decision-tree-regression], per confrontare le prestazioni. 

Per eseguire un esperimento con regressione, si consiglia di provare il set di dati di esempio Energy Efficiency Regression, che contiene numerosi attributi numerici. Il set di dati viene fornito come parte dei set di dati di esempio in Studio. È possibile usare diversi moduli di apprendimento per la previsione di Heating Load o Cooling Load. Il grafico seguente è un confronto delle prestazioni di diverse soluzioni di regressione rispetto alle previsioni del set di dati Energy Efficiency per l'elemento variabile Cooling Load di destinazione: 

| Modello | Errore assoluto medio | Radice dell'errore quadratico medio | Errore assoluto relativo | Errore quadratico relativo | Coefficiente di determinazione |
| --- | --- | --- | --- | --- | --- |
| Albero delle decisioni con boosting scalabile |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Regressione lineare (Gradient Descent) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Regressione rete neurale |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Regressione lineare (Ordinary Least Squares) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Risultati principali
L'esecuzione parallela degli esempi di regressione di Excel e di Studio ha aiutato a conoscere meglio questi strumenti. La creazione del modello di base in Excel e il confronto con i modelli usando la [regressione lineare][linear-regression] di Machine Learning hanno illustrato il funzionamento di Studio e permesso di trovare opportunità di miglioramento della selezione dei dati e delle prestazioni del modello. 

Il modulo [Filter-Based Feature Selection][filter-based-feature-selection] (Selezione di funzionalità in base al filtro) si è rivelato molto utile per velocizzare i progetti di previsione. Applicando la selezione delle caratteristiche ai dati, è possibile creare un modello ottimizzato in Studio con prestazioni complessivamente migliorate. 

La possibilità di trasferire la previsione dell'analisi predittiva da Studio a Excel comporta a livello di sistema un aumento significativo della capacità di fornire risultati a un pubblico molto più ampio di utenti aziendali. 

## <a name="resources"></a>Risorse
Di seguito sono elencate alcune risorse per agevolare l'uso della regressione: 

* Regressione in Excel. Se non è mai stata provata la regressione in Excel, questa esercitazione la spiega in modo molto chiaro: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regressione e previsione. Tyler Chessman ha scritto un articolo di blog in cui spiega come fare previsioni di serie temporali in Excel, che contiene una valida descrizione di base della regressione lineare. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Regressione lineare Ordinary Least Squares: difetti, problemi e ostacoli. Per un'introduzione e una discussione sulla regressione: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

