---
title: Guida di Azure AI per soluzioni di manutenzione predittiva | Microsoft Docs
description: Descrizione completa delle funzioni di data science che consentono di creare soluzioni di manutenzione predittiva in più settori di mercato.
services: machine-learning
author: fboylu
manager: cgronlun
editor: ''
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: 50338e05241be9ce573ff3dd3bb99711cbf15b28
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248538"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Guida di Azure AI per soluzioni di manutenzione predittiva

## <a name="summary"></a>Summary

Manutenzione predittiva (**PdM**) è un'applicazione comune di analisi predittiva che può consentire alle aziende di diversi settori di usare in modo ottimale gli asset e ridurre i costi operativi. Questa guida sintetizza le linee guida e le procedure consigliate aziendali e analitiche per sviluppare e distribuire nel modo corretto soluzioni PdM tramite la tecnologia della [piattaforma Microsoft Azure per l'intelligenza artificiale](https://azure.microsoft.com/overview/ai-platform).

Per i principianti, in questa guida vengono presentati scenari aziendali specifici del settore e il processo di qualificazione di tali scenari per PdM. Vengono inoltre illustrati i requisiti dei dati e le tecniche di modellazione per la creazione di soluzioni PdM. Il contenuto principale della guida riguarda il processo di data science, inclusi i passaggi di preparazione dei dati, progettazione delle funzioni, creazione del modello e operazionalizzazione del modello. Per integrare questi concetti chiave, la guida contiene un set di modelli di soluzioni che consentono di accelerare lo sviluppo di applicazioni PdM. Vengono inoltre forniti riferimenti a risorse di formazione utili per ottenere altre informazioni sull'uso dell'intelligenza artificiale per la data science. 

### <a name="data-science-guide-overview-and-target-audience"></a>Panoramica e destinatari della guida alla data science
Nella prima metà della guida vengono descritti i problemi aziendali tipici, i vantaggi dell'implementazione di PdM per la risoluzione di questi problemi e alcuni casi d'uso comuni. Questo contenuto è destinato ai Business Decision Maker (BDM). Nella seconda metà è illustrata la data science che sta dietro PdM e viene fornito un elenco di soluzioni PdM create in base ai principi descritti in questa guida. Sono inoltre disponibili percorsi di apprendimento e riferimenti a materiale di formazione. Questo contenuto è destinato ai Technical Decision Maker (TDM).

| Iniziare da... | Se si è... |
|:---------------|:---------------|
| [Casi aziendali per la manutenzione predittiva](#Business-case-for-predictive-maintenance) |un Business Decision Maker (BDM) che vuole ridurre i tempi di inattività e i costi operativi e ottimizzare l'uso delle apparecchiature |
| [Data science per la manutenzione predittiva](#Data-Science-for-predictive-maintenance) |un Technical Decision Maker (TDM) interessato alla valutazione delle tecnologie PdM per comprendere gli specifici requisiti di elaborazione dei dati e dell'intelligenza artificiale per la manutenzione predittiva |
| [Modelli di soluzioni per la manutenzione predittiva](#Solution-templates-for-predictive-maintenance)|un progettista software o uno sviluppatore di intelligenza artificiale che vuole preparare rapidamente una demo o un modello di verifica |
| [Risorse di formazione per la manutenzione predittiva](#Training-resources-for-predictive-maintenance) | un professionista che ricopre uno o più dei ruoli precedenti e si è interessati ad apprendere i concetti fondamentali alla base della data science, gli strumenti e le tecniche.

### <a name="prerequisite-knowledge"></a>Conoscenze richieste
Il contenuto per i BDM non prevede alcuna conoscenza precedente della data science da parte del lettore. Per il contenuto per i TDM, sono utili conoscenze di base di statistica e data science. È inoltre consigliabile la conoscenza dei servizi dati e di intelligenza artificiale di Azure, Python, R, XML e JSON. Le tecniche di intelligenza artificiale sono implementate in pacchetti Python e R. I modelli di soluzioni sono implementati mediante servizi di Azure, strumenti di sviluppo e SDK.

## <a name="business-case-for-predictive-maintenance"></a>Casi aziendali per la manutenzione predittiva

Per le aziende è essenziale che le apparecchiature critiche funzionino con la massima efficienza e vengano usate in modo ottimale per realizzare il ritorno sul capitale investito. Questi asset possono andare da motori di velivoli, turbine, ascensori o sistemi di raffreddamento industriali, che costano milioni, fino ad apparecchiature di uso quotidiano, come fotocopiatrici, macchine per il caffè o distributori d'acqua.
- Generalmente, la maggior parte delle aziende si affida alla _manutenzione correttiva_, in cui le parti vengono sostituite quando presentano un problema. La manutenzione correttiva garantisce che le parti vengano usate completamente (quindi evita sprechi della vita utile del componente), ma presenta costi per le aziende in termini di tempi di inattività, manodopera e requisiti di manutenzione non pianificata (attività fuori dall'orario lavorativo o in posizioni difficili da raggiungere).
- Al livello successivo, alcune aziende applicano la _manutenzione preventiva_, in cui viene determinato il ciclo di vita utile per una parte, in modo da eseguire la manutenzione o la sostituzione prima che si verifichi un guasto. La manutenzione preventiva consente di evitare i problemi non pianificati e di grave entità. Tuttavia, restano ancora i problemi relativi ai costi elevati dei tempi di inattività pianificati, al sottoutilizzo dei componenti prima del termine della relativa vita utile e alla manodopera.
- L'obiettivo della _manutenzione predittiva_ è ottimizzare l'equilibrio tra la manutenzione correttiva e quella preventiva, rendendo possibile la sostituzione _just-in-time_ dei componenti. In questo approccio, i componenti vengono sostituiti solo quando sta per verificarsi un guasto. Estendendo il ciclo di vita dei componenti (rispetto alla manutenzione preventiva) e riducendo i costi della manutenzione non pianificata e della manodopera (rispetto alla manutenzione correttiva), le aziende possono ottenere riduzioni dei costi e vantaggi competitivi.

## <a name="business-problems-in-pdm"></a>Problemi aziendali in PdM
Le aziende affrontano notevoli rischi operativi a causa dei guasti imprevisti e dispongono di informazioni limitate per identificare la causa radice dei problemi nei sistemi complessi. Alcuni dei principali problemi aziendali sono i seguenti:

- Rilevare anomalie nelle prestazioni o nelle funzionalità di sistemi o apparecchiature.
- Stimare se un asset potrebbe guastarsi nell'immediato futuro.
- Stimare la vita utile rimanente di un asset.
- Identificare le cause principali del guasto di un asset.
- Identificare le azioni di manutenzione da eseguire su un asset ed entro quale termine.

I tipici obiettivi di una soluzione PdM sono:

- Ridurre il rischio operativo delle apparecchiature di importanza critica.
- Aumentare tasso di rendimento per gli asset prevedendo i guasti prima che si verifichino.
- Controllare i costi di manutenzione, consentendo operazioni di manutenzione just-in-time.
- Ridurre l'abbandono da parte dei clienti, migliorare l'immagine del marchio ed evitate perdite in termini di vendite.
- Ridurre i costi di magazzino riducendo i livelli di inventario grazie alla previsione del punto di riordino.
- Individuare gli schemi connessi ai diversi problemi di manutenzione.
- Specificare gli indicatori di prestazioni chiave (KPI), ad esempio punteggi di integrità per le condizioni degli asset.
- Stimare il ciclo di vita rimanente degli asset.
- Consigliare attività di manutenzione tempestive.
- Abilitare un inventario just-in-time tramite la stima delle date degli ordini per la sostituzione delle parti.

Questi obiettivi sono i punti di partenza per consentire a:

- _data scientist_ di analizzare e risolvere specifici problemi predittivi.
- _progettisti e sviluppatori cloud_ di creare una soluzione end-to-end.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Qualificazione dei problemi per la manutenzione predittiva
È importante sottolineare che non tutti i casi d'uso o i problemi aziendali possono essere risolti efficacemente da una soluzione PdM. Esistono tre importanti criteri di qualificazione da prendere in considerazione durante la selezione dei problemi:

- Il problema deve essere di natura predittiva, ovvero deve essere necessario prevedere un esito o un risultato. Il problema deve anche prevedere un chiaro percorso di azione per impedire i guasti quando vengono rilevati.
- Il problema deve includere un record della cronologia operativa dell'apparecchiatura che contenga _sia i risultati positivi che quelli negativi_. Nell'ambito di questi record deve inoltre essere disponibile il set di azioni eseguite per attenuare i risultati negativi. Sono anche importanti segnalazioni di errori, log di manutenzione relativi alla degradazione delle prestazioni, riparazioni e log delle sostituzioni. È inoltre utile disporre di informazioni sulle riparazioni e record delle sostituzioni.
- La cronologia registrata deve essere rispecchiata in dati _pertinenti_ di qualità _sufficiente_ per supportare il caso d'uso. Per altre informazioni sulla pertinenza e sufficienza dei dati, vedere [Requisiti dei dati per la manutenzione predittiva](#Data-requirements-for-predictive-maintenance).
- Infine, l'azienda deve disporre di esperti del settore con una conoscenza precisa del problema. Devono essere consapevoli dei processi interni e delle procedure consigliate per essere in grado di aiutare l'analista a comprendere e interpretare i dati. Devono inoltre poter apportare le modifiche necessarie ai processi aziendali esistenti per contribuire a raccogliere i dati corretti per i problemi, se necessario.

## <a name="sample-pdm-use-cases"></a>Casi d'uso PdM di esempio
Questa sezione è incentrata su una raccolta di casi d'uso PdM per diversi settori, ad esempio aerospaziale, dei servizi pubblici e dei trasporti. Ogni sezione inizia con un problema aziendale e descrive i vantaggi di PdM, i dati riguardanti il problema aziendale e infine i vantaggi di una soluzione PdM.

| Problema aziendale | Vantaggi di PdM |
|:-----------------|-------------------|
|**Aviazione**      |                   |
|_Ritardi e cancellazioni dei voli_ a causa di problemi meccanici. Se non vengono riparati in tempo, i guasti possono causare l'annullamento dei voli e creare problemi di pianificazione e operativi. |Le soluzioni PdM possono prevedere la probabilità che un volo subisca un ritardo o una cancellazione a causa di guasti meccanici dell'aereo.|
|_Guasti di componenti del motore di un aereo_: la sostituzione delle parti del motore di un aereo è una delle attività di manutenzione più comuni per le compagnie aeree. Le soluzioni di manutenzione richiedono una gestione accurata della disponibilità, della consegna e della pianificazione delle scorte di componenti.|La possibilità di raccogliere informazioni sull'affidabilità dei componenti consente una sostanziale riduzione dei costi di investimento.|
|**Finanza** |                         |
|Un _guasto di uno sportello bancomat_ è un problema comune nel settore bancario. In questo caso, il problema consiste nel calcolare la probabilità che una transazione di incasso da un bancomat venga interrotta a causa di un inceppamento delle banconote o un guasto di un componente del dispositivo di erogazione del contante. La possibilità di stimare in anticipo gli errori delle transazioni consente di intervenire proattivamente sugli sportelli bancomat per evitare che si verifichino errori.| Invece di lasciare che si verifichi un guasto nel corso di una transazione, è preferibile programmare il macchinario in modo da negare il servizio in base alla stima.|
|**Energia** |                          |
|_Guasti di turbine eoliche_: le turbine eoliche sono la principale fonte di energia nei paesi più attenti all'ambiente e comportano costi di capitale elevati. Un aspetto fondamentale per le turbine eoliche è rappresentato dal motore del generatore. Un guasto di questo componente mette fuori servizio la turbina. È inoltre molto costoso da riparare.|La stima di indicatori KPI come il tempo medio tra i guasti (MTBF) può consentire alle aziende del settore energetico di evitare i guasti delle turbine e ridurre al minimo i tempi di inattività. Stimando le probabilità di guasto, i tecnici possono monitorare le turbine con la più alta probabilità di guasto e pianificare regimi di manutenzione temporizzata. I modelli predittivi forniscono informazioni dettagliate sui diversi fattori che contribuiscono al guasto, permettendo ai tecnici comprendere meglio le cause radice dei problemi.|
|_Guasti di interruttori automatici_: per garantire la distribuzione di corrente ad abitazioni e aziende, è necessario che le linee elettriche siano operative ininterrottamente. Gli interruttori automatici consentono di limitare o evitare il danneggiamento delle linee elettriche in caso di sovraccarico o condizioni meteorologiche avverse. Il problema aziendale in questo caso consiste nella stima della probabilità di guasto degli interruttori.| Le soluzioni PdM contribuiscono a ridurre i costi di riparazione e prolungano il ciclo di vita delle apparecchiature come gli interruttori. Consentono di migliorare la qualità della rete elettrica riducendo i guasti imprevisti e le interruzioni dei servizi.|
|**Trasporti e logistica** |    |
|_Guasti delle porte degli ascensori_: le grandi aziende produttrici di ascensori forniscono una gamma completa di servizi per milioni di ascensori in funzione in tutto il mondo. La sicurezza, l'affidabilità e i tempi di attività degli ascensori sono un elemento critico per i clienti. Queste aziende tengono traccia di questi e molti altri attributi tramite sensori, per facilitare la manutenzione correttiva e preventiva. In un ascensore, il problema più evidente per un cliente è il malfunzionamento delle porte. Il problema aziendale in questo caso consiste nel fornire un'applicazione predittiva con una base di conoscenze che possa stimare le cause potenziali dei guasti alle porte.| Gli ascensori rappresentano investimenti di capitale che possono avere una durata di 20 e 30 anni. Ogni vendita potenziale può essere altamente competitiva, pertanto le aspettative per il servizio e il supporto sono molto elevate. La manutenzione predittiva può fornire a queste aziende un vantaggio rispetto alla concorrenza nelle offerte di prodotti e servizi.|
|_Guasti delle ruote_: i guasti delle ruote sono la causa della metà di tutti i deragliamenti dei treni e hanno un costo di miliardi per il settore ferroviario a livello mondiale. I guasti delle ruote causano anche il deterioramento delle rotaie, provocandone prematuramente la rottura. La rottura delle rotaie può determinare eventi catastrofici come un deragliamento. Per evitare situazioni di questo tipo, le ferrovie monitorano le prestazioni delle ruote e le sostituiscono in modo preventivo. Il problema aziendale in questo caso consiste nella stima dei guasti delle ruote.| La manutenzione predittiva delle ruote ne consente la sostituzione just-in-time. |
|_Guasti delle porte dei treni della metropolitana_: uno dei motivi principali dei ritardi delle operazioni di una metropolitana sono i guasti delle porte delle carrozze. Il problema aziendale in questo caso consiste nella stima della probabilità di guasto delle porte dei treni.|La previsione tempestiva del guasto di una porta o del numero dei giorni che mancano prima che si verifichi un guasto consente alle aziende di ottimizzare le pianificazioni di manutenzione delle porte dei treni.|

Nella sezione successiva viene descritto in modo dettagliato come realizzare i vantaggi PdM discussi in precedenza.

## <a name="data-science-for-predictive-maintenance"></a>Data science per la manutenzione predittiva

In questa sezione vengono fornite indicazioni generali sui principi della data science e sulle procedure per PdM. L'obiettivo della sezione è consentire a TDM, progettisti di soluzioni o sviluppatori di comprendere i prerequisiti e il processo per la creazione di applicazioni di intelligenza artificiale end-to-end per PdM. Durante la lettura di questa sezione, è possibile esaminare le demo e i modelli di verifica elencati in [Modelli di soluzioni per la manutenzione predittiva](#Solution-templates-for-predictive-maintenance). Sarà quindi possibile usare tali principi e procedure consigliate per implementare la propria soluzione PdM in Azure.

> [!NOTE]
> Lo scopo di questa guida non è insegnare al lettore la data science. Numerose risorse utili per un approfondimento sono disponibili nella sezione relativa alle [risorse di formazione per la manutenzione predittiva](#Training-resources-for-predictive-maintenance). I [modelli di soluzioni](#Solution-templates-for-predictive-maintenance) elencati nella guida illustrano alcune di queste tecniche di intelligenza artificiale per specifici problemi PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Requisiti dei dati per la manutenzione predittiva

Il successo di qualsiasi apprendimento dipende (a) dalla qualità di ciò che viene insegnato e (b) dalla capacità di chi apprende. I modelli predittivi apprendono schemi dai dati cronologici e stimano i risultati futuri con una determinata probabilità in base a tali schemi osservati. L'accuratezza predittiva di un modello dipende dalla pertinenza, sufficienza e qualità dei dati di training e test. I nuovi dati a cui viene assegnato un punteggio tramite questo modello devono avere le stesse caratteristiche e lo stesso schema dei dati di training/test. Le caratteristiche (tipo, densità, distribuzione e così via) dei nuovi dati devono corrispondere a quelle dei set di dati di training e di test. L'obiettivo di questa sezione è illustrare tali requisiti dei dati.

### <a name="relevant-data"></a>Dati pertinenti

Innanzitutto, i dati deve essere _pertinenti per il problema_. Si consideri il caso d'uso relativo ai _guasti delle ruote_ descritto in precedenza: i dati di training devono contenere caratteristiche correlate alle operazioni delle ruote. Se il problema fosse quello di stimare i guasti del _sistema di trazione_, i dati di training dovrebbero includere tutti i vari componenti per il sistema di trazione. Il primo caso riguarda un componente specifico, mentre il secondo caso riguarda il guasto di un sottosistema di maggiori dimensioni. La raccomandazione generale è quella di progettare sistemi previsionali per componenti specifici anziché per sottosistemi di maggiori dimensioni, poiché per questi ultimi i dati saranno più distribuiti. Gli esperti del settore (vedere [Qualificazione dei problemi per la manutenzione predittiva](#Qualifying-problems-for-predictive-maintenance)) devono fornire supporto per la selezione dei subset di dati più rilevanti per l'analisi. Le origini dati pertinenti sono descritte più in dettaglio nella sezione [Preparazione dei dati per la manutenzione predittiva](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dati sufficienti
In genere, ci si pone due domande in relazione ai dati relativi alla cronologia dei guasti: (1) "Quanti eventi di errore sono necessari per l'addestramento di un modello?" (2) "Quanti record vengono considerati sufficienti?" Non esistono risposte definitive, ma solo regole empiriche. Per il punto (1), maggiore è il numero di eventi di errore, migliore sarà il modello. Per il punto (2), il numero esatto di eventi di errore dipende dai dati e dal contesto del problema da risolvere. D'altra parte, se un macchinario presenta guasti con una frequenza eccessiva, l'azienda lo sostituirà, riducendo le istanze dei guasti. Anche in questo caso, le indicazioni dagli esperti del settore sono importanti. Sono comunque disponibili metodi per affrontare il problema degli _eventi rari_. Tali metodi sono descritti nella sezione [Gestione di dati sbilanciati](#Handling-imbalanced-data).

### <a name="quality-data"></a>Dati di qualità
La qualità dei dati è di importanza critica: ogni valore di attributo del predittore deve essere _accurato_ in combinazione con il valore della variabile di destinazione. La qualità dei dati è un'area che è oggetto di studi approfonditi nei campi della statistica e della gestione dei dati, pertanto non rientra nell'ambito di questa guida.

> [!NOTE]
> Esistono diversi prodotti e risorse aziendali per garantire dati di qualità. Di seguito vengono forniti alcuni riferimenti di esempio:
> - Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning, Wiley, 2003.
> - [Exploratory Data Analysis, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Quantitative Data Cleaning for Large Databases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Introduction to Data Cleaning with R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Preparazione dei dati per la manutenzione predittiva

### <a name="data-sources"></a>Origini dati

Le origini dati pertinenti per la manutenzione predittiva includono, a titolo esemplificativo:
- Cronologia dei guasti
- Cronologia di manutenzioni/riparazioni
- Condizioni operative delle macchine
- Metadati delle apparecchiature

#### <a name="failure-history"></a>Cronologia dei guasti
Gli eventi di errore sono rari nelle applicazioni PdM. Tuttavia, quando si creano modelli predittivi, l'algoritmo deve apprendere il normale schema operativo di un componente, nonché i relativi schermi di guasto. I dati di training devono quindi contenere un numero sufficiente di esempi per entrambe le categorie. I record di manutenzione e la cronologia della sostituzione di parti rappresentano fonti di informazioni valide per individuare gli eventi di errore. Con l'aiuto di alcune conoscenze specifiche, è possibile definire come errori anche le anomalie nei dati di training.

#### <a name="maintenancerepair-history"></a>Cronologia di manutenzioni/riparazioni
La cronologia delle manutenzioni di un asset contiene i dettagli sui componenti sostituiti, le attività di riparazione eseguite e così via. Questi eventi registrano gli schemi di degradazione. La mancanza di tali informazioni di importanza critica nei dati di training può causare la generazione di risultati fuorvianti da parte del modello. Informazioni sulla cronologia dei guasti possono anche essere disponibili nella cronologia di manutenzione come speciali codici di errore o date degli ordini per le parti. Origini dati aggiuntive che influiscono sugli schermi di errore devono essere analizzate e fornite dagli esperti del settore.

#### <a name="machine-operating-conditions"></a>Condizioni operative delle macchine
I dati di streaming basati su sensori (o di altro tipo) dell'apparecchiatura durante il funzionamento rappresentano un'importante origine dati. Un presupposto chiave in PdM è che lo stato di integrità di un macchinario comporta una degradazione nel corso del tempo durante il normale funzionamento. È necessario che i dati contengano funzioni variabili nel tempo che acquisiscono schemi di aging o qualsiasi anomalia che possa causare una riduzione delle prestazioni. L'aspetto temporale dei dati è necessario per consentire all'algoritmo di apprendere gli schemi di errore e non di errore nel corso del tempo. In base a questi punti dati, l'algoritmo impara a stimare il numero di unità di tempo per cui un macchinario può continuare a funzionare prima che si verifichi un guasto.

#### <a name="static-feature-data"></a>Dati sulle caratteristiche statiche
Le caratteristiche statiche sono i metadati sulle apparecchiature. Alcuni esempi sono la marca dell'apparecchiatura, il modello, la data di fabbricazione, la data di avvio del servizio, la posizione del sistema e altre specifiche tecniche.

Esempi di dati pertinenti per i [casi d'uso PdM di esempio](#Sample-PdM-use-cases) sono riportati nella tabella seguente:

| Caso d'uso | Esempi di dati pertinenti |
|:---------|---------------------------|
|_Ritardi e cancellazioni dei voli_ | Informazioni sulle rotte dei voli sotto forma di tratte dei voli e registri di manutenzione. I dati sulle tratte dei voli includono dettagli quali data di partenza/arrivo, ora, aeroporto, scali e così via. I registri includono una serie di codici di errore e di manutenzione registrati dal personale di terra addetto alla manutenzione.|
|_Guasti di componenti del motore di un aereo_ | Dati raccolti da sensori a bordo dell'aereo che forniscono informazioni sulla condizione delle varie parti. I record di manutenzione consentono di identificare quando si sono verificati guasti dei componenti e quando questi ultimi sono stati sostituiti.|
|_Guasto di uno sportello bancomat_ | Letture dei sensori per ogni transazione (deposito di contante/assegni) ed erogazione di contante. Informazioni sulla misurazione della distanza tra le banconote, spessore delle banconote, distanza di arrivo delle banconote, attributi degli assegni e così via. Record di manutenzione che forniscono codici di errore, informazioni sulle riparazioni e dati sull'ultimo rifornimento del dispositivo di erogazione del contante.|
|_Guasti di turbine eoliche_ | I sensori eseguono il monitoraggio delle condizioni della turbina, come temperatura, direzione del vento, potenza generata, velocità del generatore e così via. I dati vengono raccolti da più turbine eoliche appartenenti a impianti eolici che si trovano in diverse aree geografiche. In genere, per ogni turbina verranno inoltrate le misurazioni di più letture a un intervallo di tempo prestabilito.|
|_Guasti di interruttori automatici_ | Log di manutenzione che includono le azioni correttive, preventive e sistematiche. Dati operativi che includono i comandi automatici e manuali inviati agli interruttori, ad esempio per le azioni di apertura e chiusura. Metadati del dispositivo, ad esempio data di fabbricazione, posizione, modello e così via. Specifiche dell'interruttore, ad esempio livelli di tensione, georilevazione e condizioni ambientali.|
|_Guasti delle porte degli ascensori_| Metadati sull'ascensore, ad esempio tipo di ascensore, data di fabbricazione, frequenza di manutenzione, tipo di edificio e così via. Informazioni operative, ad esempio numero di cicli delle porte e tempi medi di chiusura delle porte. Cronologia dei guasti con le relative cause.|
|_Guasti delle ruote_ | Dati dei sensori che misurano accelerazione delle ruote, istanze di frenatura, distanza, velocità e così via. Informazioni statiche sulle ruote, ad esempio produttore e data di fabbricazione. Dati sui guasti dedotti dal database degli ordini di parti, che tiene traccia delle date e delle quantità degli ordini.|
|_Guasti delle porte dei treni della metropolitana_ | Tempi di apertura e chiusura delle porte, altri dati operativi come la condizione corrente delle porte dei treni. I dati statici possono includere colonne per l'identificatore dell'asset, l'ora e il valore della condizione.|

### <a name="data-types"></a>Tipi di dati
Considerate le origini dati precedenti, i due tipi di dati principali osservati nel dominio PdM sono:

- _Dati temporali_: telemetria operativa, condizioni dei macchinari, tipi di ordini di lavoro, codici di priorità con timestamp al momento della registrazione. Anche per i guasti, le manutenzioni/riparazioni e la cronologia relativa all'uso saranno presenti timestamp associati a ogni evento.
- _Dati statici_: le caratteristiche della macchina e dell'operatore sono in genere statiche, perché descrivono le specifiche tecniche delle macchine o gli attributi dell'operatore. Se queste caratteristiche possono variare nel tempo, anch'esse devono disporre di timestamp associati.

Il predittore e le variabili di destinazione devono essere pre-elaborati/trasformati in [tipi di dati numerici, categorici e di altro tipo](https://www.statsdirect.com/help/basics/measurement_scales.htm), a seconda dell'algoritmo in uso.

### <a name="data-preprocessing"></a>Pre-elaborazione dei dati
Come prerequisito per la _progettazione delle funzioni_, preparare i dati da vari flussi per comporre uno schema da cui sia possibile sviluppare facilmente le funzioni. Visualizzare innanzitutto i dati come una tabella di record. Ogni riga della tabella rappresenta un'istanza di training e le colonne rappresentano le funzioni del _predittore_ (anche denominate variabili o attributi indipendenti). Organizzare i dati in modo che sia l'ultima colonna sia la _destinazione_ (variabile dipendente). Per ogni istanza di training, assegnare un'_etichetta_ come valore di questa colonna.

Per i dati temporali, suddividere la durata dei dati del sensore in unità di tempo. Ogni record deve appartenere a un'unità di tempo per un asset e deve _offrire informazioni distinte_. Le unità di tempo sono definite in base alle esigenze aziendali come multipli di secondi, minuti, ore, giorni, mesi e così via. L'unità di tempo _non deve necessariamente corrispondere alla frequenza di raccolta dati_. Se la frequenza è elevata, i dati potrebbero non indicare alcuna differenza significativa da un'unità all'altra. Si supponga, ad esempio, di raccogliere i dati sulla temperatura ambientale ogni 10 secondi. L'uso dello stesso intervallo per i dati di training aumenta solo il numero degli esempi senza fornire informazioni aggiuntive. In questo caso, una migliore strategia potrebbe prevedere l'uso della media dei dati per periodi superiori a 10 minuti o di un'ora, in base alla motivazione aziendale.

Per i dati statici
- _Record di manutenzione_: i dati non elaborati di manutenzione contengono un identificatore dell'asset e un timestamp con informazioni sulle attività di manutenzione che sono state eseguite in un determinato momento. Trasformare le attività di manutenzione in colonne _categoriche_, in cui ogni descrittore di categoria è associato in modo univoco a una specifica azione di manutenzione. Lo schema per i record di manutenzione includerà l'identificatore dell'asset, l'ora e le azioni di manutenzione.

- _Record dei guasti_: i guasti o i motivi dei guasti possono essere registrati come codici di errore specifici o eventi di errore definiti da condizioni aziendali specifiche. Nei casi in cui le apparecchiature presentino più codici di errore, l'esperto del settore deve contribuire a identificare quelli che sono pertinenti per la variabile di destinazione. Usare le condizioni o i codici di errore rimanenti per creare le funzioni del _predittore_ correlate ai guasti. Lo schema per i record dei guasti includerà l'identificatore dell'asset, l'ora, il guasto o il motivo del guasto, se disponibile.

- _Metadati della macchina e dell'operatore_: unire i dati sulla macchina e sull'operatore in un solo schema per associare un asset al rispettivo operatore, con i relativi attributi. Lo schema per le condizioni delle macchine includerà identificatore dell'asset, caratteristiche dell'asset, identificatore dell'operatore e caratteristiche dell'operatore.

Altri passaggi di pre-elaborazione dei dati includono la _gestione dei valori mancanti_ e la _normalizzazione_ dei valori degli attributi. Una discussione dettagliata esula dall'ambito di questa guida. Vedere la sezione successiva per alcuni riferimenti utili.

Dopo avere predisposto queste origini dati pre-elaborate, la trasformazione finale prima della progettazione delle funzioni consiste nel creare un join delle tabelle precedenti in base all'identificatore dell'asset e al timestamp. La tabella risultante deve contenere valori null per la colonna del guasto quando la macchina è in condizioni normali. Questi valori null possono essere attribuiti con un indicatore per il funzionamento normale. Usare questa colonna dei guasti per creare _etichette per il modello predittivo_. Per altre informazioni, vedere la sezione sulle [tecniche di modellazione per la manutenzione predittiva](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Progettazione delle funzioni
La progettazione delle funzioni è il primo passaggio prima della modellazione dei dati. Il suo ruolo nel processo di data science è [descritto in questo articolo](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). Una _funzione_ è un attributo predittivo per il modello, ad esempio temperatura, pressione, vibrazione e così via. Per PdM, la progettazione delle funzioni comporta l'astrazione dell'integrità di una macchina a partire da dati cronologici raccolti per un periodo di tempo considerevole. In questo senso, è diversa da attività simili come il monitoraggio remoto, il rilevamento delle anomalie e il rilevamento degli errori. 

### <a name="time-windows"></a>Intervalli di tempo
Il monitoraggio remoto comporta la segnalazione degli eventi che si verificano in determinati _momenti_. I modelli di rilevamento delle anomalie valutano i flussi di dati in ingresso (assegnando loro un punteggio) per identificare le anomalie in determinati momenti. Il rilevamento degli errori classifica gli errori come di tipi specifici quando si verificano in determinati momenti. Al contrario, PdM implica la previsione degli errori in un _periodo di tempo futuro_, in base alle funzioni che rappresentano il comportamento della macchina in un _periodo di tempo cronologico_. Per PdM, i dati delle funzioni relativi a momenti specifici comportano un rumore eccessivo per la formulazione di previsioni. I dati relativi a ogni funzione devono quindi essere _uniformati_ mediante l'aggregazione dei punti dati in intervalli di tempo.

### <a name="lag-features"></a>Funzioni di ritardo
I requisiti aziendali definiscono l'intervallo di tempo nel futuro rispetto al quale il modello deve eseguire la previsione. A sua volta, questa durata consente di stabilire "quanto indietro nel tempo deve guardare il modello" per formulare queste stime. Questo periodo viene chiamato _ritardo_ e le funzioni progettate in base a questo periodo di ritardo sono denominate _funzioni di ritardo_. In questa sezione vengono descritte le funzioni di ritardo che possono essere create da origini dati con timestamp e la creazione di funzioni da origini dati statiche. Le funzioni di ritardo in genere sono di natura _numerica_.

> [!IMPORTANT]
> La finestra temporale è determinata tramite la sperimentazione e deve essere finalizzata con l'aiuto di un esperto del settore. Lo stesso vale per la selezione e definizione delle funzioni di ritardo, le relative aggregazioni e il tipo di finestre temporali.

#### <a name="rolling-aggregates"></a>Aggregazioni in sequenza
Per ogni record di un asset, viene scelta una finestra con dimensione "W" che corrisponde al numero di unità di tempo per cui calcolare le aggregazioni. Vengono quindi calcolate le funzioni di ritardo usando i periodi W _prima della data_ di quel record. Nella figura 1, le linee blu indicano i valori di un sensore registrati per un asset per ogni unità di tempo. Indicano una media mobile dei valori della funzione su una finestra con dimensione W=3. La media mobile viene calcolata su tutti i record con timestamp compresi nell'intervallo da t<sub>1</sub> (in arancione) a t<sub>2</sub> (in verde). Il valore per W è in genere in minuti o ore, a seconda della natura dei dati. Tuttavia, per alcuni problemi, la selezione di un valore W elevato (ad esempio, 12 mesi) può fornire l'intera cronologia di un asset fino all'ora del record.

![Figura 1. Funzioni di aggregazione in sequenza](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) Figura 1. Funzioni di aggregazione in sequenza

Alcuni esempi di funzioni di aggregazione in sequenza in una finestra temporale sono il conteggio, la media, le misure CUMESUM (somma cumulativa) e i valori minimo e massimo. Inoltre, spesso vengono usati la varianza, la deviazione standard e il numero di outlier oltre N deviazioni standard. Alcuni esempi di aggregazioni che possono essere applicate per i [casi d'uso](#Sample-PdM-use-cases) presentati in questa guida sono elencati di seguito. 
- _Ritardi dei voli_: numero di codici di errore nell'ultimo giorno/settimana.
- _Guasti di componenti del motore di un aereo_: medie mobili, deviazione standard e somma per il giorno/settimana precedente e così via. Questa metrica deve essere determinata in collaborazione con l'esperto del settore aziendale.
- _Guasti degli sportelli bancomat_: medie mobili, mediana, intervallo, deviazioni standard, numero di outlier oltre tre deviazioni standard, funzioni CUMESUM superiore e inferiore.
- _Guasti delle porte dei treni della metropolitana_: numero di eventi nel giorno precedente, nell'ultima settimana, nelle ultime due settimane e così via.
- _Guasti di interruttori automatici_: numero di guasti nell'ultima settimana, nell'ultimo anno, negli ultimi tre anni e così via.

Un'altra tecnica utile in PdM consiste nell'acquisire modifiche della tendenza, picchi e modifiche dei livelli usando algoritmi che rilevano le anomalie nei dati.

#### <a name="tumbling-aggregates"></a>Aggregazioni a cascata
Per ogni record con etichetta di un asset, viene definita una finestra con dimensione _W-<sub>k</sub>_, dove _k_ è il numero di finestre con dimensione _W_. Vengono quindi create le aggregazioni su _k_ _finestre a cascata_ _W-k, W-<sub>(k-1)</sub>, …, W-<sub>2</sub>, W-<sub>1</sub>_ per i periodi prima di un timestamp del record. _k_ può essere un numero ridotto per acquisire gli effetti a breve termine o un numero elevato per acquisire schemi di degradazione a lungo termine (vedere la figura 2).

![Figura 2. Funzioni di aggregazione a cascata](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) Figura 2. Funzioni di aggregazione a cascata

Ad esempio, le funzioni di ritardo per il caso d'uso delle turbine eoliche possono essere create con W=1 e k=3. Tali funzioni implicano il ritardo per ognuno degli ultimi tre mesi usando gli outlier superiore e inferiore.

### <a name="static-features"></a>Funzioni statiche

Le specifiche tecniche delle apparecchiature come la data di fabbricazione, il numero di modello e la posizione sono alcuni esempi di funzioni statiche. Vengono trattate come variabili _categoriche_ per la modellazione. Alcuni esempi per il caso d'uso degli interruttori automatici sono la tensione, la corrente, la potenza, il tipo di trasformatore e fonte di alimentazione. Per i guasti delle ruote, un esempio è il tipo di ruote (lega o acciaio).

Come risultato delle operazioni di preparazione dei dati descritte finora, i dati saranno organizzati come illustrato di seguito. I dati di training, test e convalida devono avere questo schema logico (in questo esempio il tempo è espresso in unità di giorni).

| ID asset | Tempo | <Feature Columns> | Etichetta |
| ---- | ---- | --- | --- |
| A123 |Giorno 1 | . . . | . |
| A123 |Giorno 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Giorno 1 | . . . | . |
| B234 |Giorno 2 | . . . | . |
| ...  |...   | . . . | . |

L'ultimo passaggio nella progettazione delle funzioni è l'**assegnazione di etichette** alla variabile di destinazione. Questo processo dipende dalla tecnica di modellazione. A sua volta, la tecnica di modellazione varia a seconda del problema aziendale e della natura dei dati disponibili. L'assegnazione di etichette è descritta nella sezione successiva.

> [!IMPORTANT]
> La preparazione dei dati e la progettazione delle funzioni sono altrettanto importanti delle tecniche di modellazione per la corretta creazione di soluzioni PdM. L'esperto del settore e lo specialista della soluzione devono dedicare tempo per la determinazione delle funzioni e dei dati appropriati per il modello. Di seguito sono elencate solo alcune delle numerose pubblicazioni disponibili sulla progettazione delle funzioni:
> - Pyle, D. Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists, O'Reilly, 2018.
> - Dong, G. Liu, H. (Editors), Feature Engineering for Machine Learning and Data Analytics (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Tecniche di modellazione per la manutenzione predittiva

In questa sezione vengono descritte le principali tecniche di modellazione per i problemi PdM, insieme ai relativi metodi per la creazione di etichette specifiche. È necessario tenere presente che una singola tecnica di modellazione può essere usata in diversi settori. La tecnica di modellazione è associata al problema di data science, invece che al contesto dei dati disponibili.

> [!IMPORTANT]
> La scelta delle etichette per i casi di errore e la strategia di assegnazione delle etichette  
> devono essere determinate in collaborazione con l'esperto del settore.

### <a name="binary-classification"></a>Classificazione binaria
La classificazione binaria viene usata per _stimare la probabilità che un'apparecchiatura possa guastarsi in un periodo di tempo futuro_, denominato _orizzonte temporale futuro X_. X viene determinato in base al problema aziendale e ai dati disponibili, in collaborazione con l'esperto del settore. Alcuni esempi:
- _lead time minimo_ necessario per sostituire i componenti, distribuire le risorse di manutenzione, eseguire la manutenzione per evitare un problema che potrebbe verificarsi in tale periodo di tempo.
- _numero minimo di eventi_ che possono avere luogo prima che si verifichi un problema.

In questa tecnica, vengono identificati due tipi di esempi di training. Un esempio positivo, _che indica un errore_, con etichetta = 1. Un esempio negativo, che indica il normale funzionamento, con etichetta = 0. La variabile di destinazione, e quindi i valori delle etichette, sono _categorici_. Il modello deve identificare la probabilità che ogni nuovo esempio possa avere esito negativo o funzionare normalmente entro le prossime X unità di tempo.

#### <a name="label-construction-for-binary-classification"></a>Costruzione delle etichette per la classificazione binaria
In questo caso, la domanda è: "qual è la probabilità che l'asset si guasti entro X unità di tempo?" Per rispondere a questa domanda, assegnare a X record prima dell'errore di un asset l'etichetta "possibile guasto" (etichetta = 1) e a tutti gli altri record l'etichetta "normale" (etichetta = 0) (vedere la figura 3).

![Figura 3. Assegnazione di etichette per la classificazione binaria](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) Figura 3. Assegnazione di etichette per la classificazione binaria

Di seguito sono elencati gli esempi della strategia di assegnazione delle etichette per alcuni dei casi d'uso.
- _Ritardi dei voli_: per X è possibile scegliere 1 giorno, in modo da stimare i ritardi nelle 24 ore successive. A tutti i voli entro 24 ore prima dei guasti viene assegnata l'etichetta 1.
- _Guasti di sportelli bancomat_: un obiettivo potrebbe essere quello di determinare la probabilità di errore di una transazione nell'ora successiva. In questo caso, a tutte le transazioni eseguite entro l'ultima ora dal guasto viene assegnata l'etichetta 1. Per stimare la probabilità di errore nelle prossime N banconote erogate, a tutte le banconote erogate nelle ultime N banconote da un guasto viene assegnata l'etichetta 1.
- _Guasti di interruttori automatici_: l'obiettivo potrebbe essere quello di stimare la probabilità che il successivo comando dell'interruttore non riesca. In questo caso, per X verrà scelto un comando futuro.
- _Guasti delle porte dei treni_: per X è possibile scegliere due giorni.
- _Guasti delle turbine eoliche_: per X è possibile scegliere due mesi.

### <a name="regression-for-predictive-maintenance"></a>Regressione per la manutenzione predittiva
I modelli di regressione vengono usati per _calcolare la vita utile rimanente di un asset_. La vita utile rimanente è definita come il periodo di tempo per cui un asset è operativo prima che si verifichi il guasto successivo. Ogni esempio di training è un record che appartiene a un'unità di tempo _nY_ per un asset, dove _n_ è il multiplo. Il modello deve calcolare la vita utile rimanente di ogni nuovo esempio come _numero continuo_. Questo numero indica il periodo di tempo rimanente prima del guasto.

#### <a name="label-construction-for-regression"></a>Costruzione delle etichette per la regressione
In questo caso, la domanda è: "qual è la vita utile rimanente dell'apparecchiatura?" Per ogni record prima del guasto, calcolare l'etichetta come il numero di unità di tempo rimanenti prima del guasto successivo. In questo metodo le etichette sono variabili continue (vedere la figura 4).

![Figura 4. Assegnazione di etichette per la regressione](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) Figura 4. Assegnazione di etichette per la regressione

Per la regressione, l'assegnazione delle etichette viene eseguita in base a un punto di guasto. Il calcolo non è possibile se non si conosce il tempo di sopravvivenza dell'asset prima di un guasto. Pertanto, a differenza della classificazione binaria, gli asset senza errori nei dati non possono essere usati per la modellazione. La soluzione migliore per questo problema è l'uso di un'altra tecnica statistica detta [analisi di sopravvivenza](https://en.wikipedia.org/wiki/Survival_analysis). Tuttavia, possono insorgere potenziali complicazioni nell'applicare questa tecnica ai casi d'uso PdM, che prevedono dati variabili nel tempo con intervalli frequenti. Per altre informazioni sull'analisi di sopravvivenza, vedere [questo articolo](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificazione multiclasse per la manutenzione predittiva
Le tecniche di classificazione multiclasse possono essere usate nelle soluzioni PdM per due scenari:
- Stimare _due risultati futuri_: il primo risultato è _un intervallo di tempo prima del guasto_ per un asset. L'asset viene assegnato a uno dei diversi periodi di tempo possibili. Il secondo risultato è la probabilità di guasto in un periodo futuro per via di _una delle molteplici cause radice_. Questa stima consente al personale di manutenzione di controllare i sintomi e definire le pianificazioni di manutenzione.
- Stimare la _causa radice più probabile_ di un guasto specifico. Questo risultato fornisce suggerimenti in merito al set di azioni di manutenzione corretto per la riparazione di un guasto. Un elenco classificato di cause radice e delle azioni di riparazione associate consente tecnici di assegnare priorità alle azioni di riparazione in seguito a un guasto.

#### <a name="label-construction-for-multi-class-classification"></a>Costruzione delle etichette per la classificazione multiclasse
In questo caso, la domanda è: "qual è la probabilità che un asset si guasti entro _nZ_ unità di tempo, dove _n_ è il numero di periodi?" Per rispondere a questa domanda, assegnare etichette a nZ record prima del guasto di un asset usando intervalli di tempo (3Z, 2Z, Z). Assegnare a tutti gli altri record l'etichetta "normale" (etichetta = 0). In questo metodo la variabile di destinazione contiene valori _categorici_ (vedere la figura 5).

![Figura 5. Assegnazione di etichette per la classificazione multiclasse per la stima dell'ora del guasto](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) Figura 5. Assegnazione di etichette per la classificazione multiclasse per la stima dell'ora del guasto

In questo caso, la domanda è: "qual è la probabilità che l'asset si guasti entro X unità di tempo per via della causa radice o del problema _P<sub>i</sub>_?" dove _i_ è il numero di possibili cause radice. Per rispondere a questa domanda, assegnare a X record prima dell'errore di un asset l'etichetta "possibile guasto per via della causa radice _P<sub>i</sub>_" (etichetta = _P<sub>i</sub>_). Assegnare a tutti gli altri record l'etichetta "normale" (etichetta = 0). Anche in questo metodo le etichette sono categoriche (vedere la figura 6).

![Figura 6. Assegnazione di etichette per la classificazione multiclasse per la stima delle cause radice](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) Figura 6. Assegnazione di etichette per la classificazione multiclasse per la stima delle cause radice

Il modello assegna una probabilità di guasto a causa di ogni problema _P<sub>i</sub>_, oltre alla probabilità che non si verifichi alcun guasto. Queste probabilità possono essere ordinate per grandezza, per consentire la stima dei problemi con la più alta possibilità che si verifichino in futuro.

In questo caso, la domanda è: "quali azioni di manutenzione consigliare dopo un guasto?" Per rispondere a questa domanda, l'assegnazione di etichette _non richiede la selezione di un orizzonte futuro_, perché il modello non deve prevedere un guasto in futuro. Deve semplicemente stimare la causa radice più probabile_dopo che il guasto si è già verificato_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Metodi di training, convalida e test per la manutenzione predittiva
[Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) illustra in modo completo il ciclo di training, test e convalida del modello. In questa sezione vengono trattati gli aspetti specifici per PdM.

### <a name="cross-validation"></a>Convalida incrociata
L'obiettivo della [convalida incrociata](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) è definire un set di dati per "testare" il modello in fase di training. Questo set di dati viene denominato _set di convalida_. Questa tecnica consente di limitare problemi come l'_overfitting_ e fornisce informazioni sul modo in cui il modello verrà generalizzato per un set di dati indipendente, ovvero un set di dati sconosciuto, che potrebbe essere relativo a un problema reale. La routine di training e test per PdM deve considerare gli aspetti variabili nel tempo per una migliore generalizzazione dei dati futuri non visti.

Molti algoritmi di apprendimento automatico dipendono da una quantità di iperparametri che possono modificare significativamente le prestazioni del modello. I valori ottimali di questi iperparametri non vengono calcolati automaticamente durante il training del modello. Dovranno essere specificati dal data scientist. Per trovare i valori ottimali degli iperparametri sono disponibili vari modi.

Quello più comune è la _convalida incrociata di k sezioni_ che suddivide in modo casuale gli esempi in _k_ sezioni. Per ogni set di valori degli iperparametri, eseguire l'algoritmo di apprendimento _k_ volte. A ogni iterazione, usare gli esempi nella sezione corrente come set di convalida, mentre il resto degli esempi come set di training. Eseguire il training dell'algoritmo sugli esempi di training e calcolare le metriche delle prestazioni sugli esempi di convalida. Alla fine di questo ciclo, calcolare la media delle metriche delle prestazioni _k_. Per ogni set di valori degli iperparametri, scegliere quelli con le prestazioni medie migliori. L'attività di scelta degli iperparametri è spesso di natura sperimentale.

Nei problemi PdM i dati vengono registrati in serie temporali di eventi che derivano da diverse origini dati. Questi record possono essere ordinati in base all'ora di assegnazione dell'etichetta. Di conseguenza, se si suddivide _in modo causale_ il set di dati in set di training e convalida, _alcuni esempi di training possono risultare successivi agli esempi di convalida_. Le prestazioni future dei valori degli iperparametri verranno stimate in base ad alcuni dati arrivati _prima_ che fosse eseguito il training del modello. Queste stime potrebbero risultare eccessivamente ottimistiche, specialmente se le serie temporali non sono fisse e variano nel tempo. I valori degli iperparametri scelti potrebbero quindi risultare non ottimali.

Il metodo consigliato consiste nel suddividere gli esempi in set di training e di convalida _in modo dipendente dal tempo_, così che tutti gli esempi di convalida siano successivi nel tempo a tutti i gli esempi di training. Per ogni set di valori degli iperparametri, eseguire il training dell'algoritmo sul training set. Misurare le prestazioni del modello sullo stesso set di convalida. Scegliere i valori degli iperparametri che mostrano le prestazioni migliori. I valori degli iperparametri scelti dalla suddivisione training/convalida consentono di ottenere prestazioni future del modello migliori rispetto ai valori scelti in modo casuale dalla convalida incrociata.

Il modello finale può essere generato eseguendo il training di un algoritmo di apprendimento su tutti i dati di training usando i valori degli iperparametri migliori.

### <a name="testing-for-model-performance"></a>Test delle prestazioni del modello
Una volta creato un modello, è necessario eseguire una stima delle relative prestazioni future sui nuovi dati. Una stima valida è rappresentata dalle metriche delle prestazioni dei valori degli iperparametri sul set di convalida oppure dalle metriche delle prestazioni medie calcolate da una convalida incrociata. Queste stime sono spesso eccessivamente ottimistiche. L'azienda in molti casi potrebbe disporre di linee guida aggiuntive per le modalità di test del modello.

Il metodo consigliato per PdM è suddividere gli esempi in set di dati di training, di convalida e di test _in modo dipendente dal tempo_. Tutti gli esempi di test devono successivi a tutti gli esempi di training e di convalida. Dopo la suddivisione, generare il modello e misurarne le prestazioni, come descritto in precedenza.

Quando le serie temporali sono fisse e facili da stimare, sia l'approccio casuale che quello dipendente dal tempo generano valutazioni simili delle prestazioni future. Quando però le serie temporali non sono fisse e/o difficili da stimare, l'approccio dipendente dal tempo genera stime più realistiche delle prestazioni future.

### <a name="time-dependent-split"></a>Suddivisione dipendente dal tempo
In questa sezione sono descritte le procedure consigliate per l'implementazione della suddivisione dipendente dal tempo. Una suddivisione bidirezionale dipendente dal tempo tra set di training e di test è descritta di seguito.

Si supponga di avere un flusso di eventi con timestamp, ad esempio le misurazioni da diversi sensori. Definire le funzioni e le etichette degli esempi di training e test in base a intervalli di tempo che contengono più eventi. Ad esempio, per la classificazione binaria, creare le funzioni in base agli eventi passati e le etichette in base agli eventi futuri entro "X" unità di tempo future (vedere le sezioni relative a [progettazione delle funzioni](#Feature-engineering) e [tecniche di modellazione](#Modeling-techniques-applied-to-PdM-use-cases)). Quindi, l'intervallo di tempo dell'assegnazione di etichette è successivo all'intervallo di tempo delle relative funzioni.

Per la suddivisione dipendente dal tempo, selezionare un _tempo limite per il training T<sub>c</sub>_ in cui eseguire il training di un modello, con iperparametri ottimizzati usando dati cronologici fino a T<sub>c</sub>. Per impedire la perdita delle etichette future che si trovano oltre T<sub>c</sub> nei dati di training, scegliere l'ultimo intervallo di tempo per l'assegnazione di etichette agli esempi di training impostando X unità prima di T<sub>c</sub>. Nell'esempio illustrato nella figura 7, ogni quadratino rappresenta un record nel set di dati in cui le funzioni e le etichette vengono calcolate come descritto in precedenza. La figura mostra i record che dovranno essere inseriti nei set di training e di test per X=2 e W=3:

![Figura 7. Suddivisione dipendente dal tempo per la classificazione binaria](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) Figura 7. Suddivisione dipendente dal tempo per la classificazione binaria

I quadratini verdi rappresentano i record appartenenti alle unità di tempo che possono essere usate per il training. Ogni esempio di training viene generato prendendo in considerazione gli ultimi tre periodi per la generazione delle funzioni e due periodi futuri per le etichette prima di T<sub>c</sub>. Quando una parte dei due periodi di futuri è oltre T<sub>c</sub>, escludere tale esempio dal training set perché non si presuppone alcuna visibilità oltre T<sub>c</sub>.

I quadratini neri rappresentano i record del set di dati con etichetta finale che non dovranno essere usati nel training set, a causa di questo vincolo. Questi record inoltre non verranno usati nei dati di test, poiché sono precedenti a T<sub>c</sub>. Inoltre, i relativi intervalli di tempo per l'assegnazione delle etichette dipendono in parte dall'intervallo di tempo di training, il che non rappresenta una situazione ideale. I dati di training e di test devono avere intervalli di tempo separati per l'assegnazione delle etichette, in modo da evitare la perdita di informazioni sulle etichette.

Le tecniche descritte finora consentono la sovrapposizione tra gli esempi di training e di test con timestamp vicini a T<sub>c</sub>. Una soluzione per ottenere una maggiore separazione consiste nell'escludere dal set di test gli esempi entro W unità di tempo da T<sub>c</sub>. Una suddivisione aggressiva di questo tipo tuttavia dipende da un'ampia disponibilità di dati.

I modelli di regressione usati per la stima della vita utile rimanente sono più gravemente influenzati dal problema della perdita. L'uso del metodo di suddivisione casuale determina un overfitting molto elevato. Per i problemi di regressione, la suddivisione deve essere tale per cui i record che appartengono ad asset con guasti prima di T<sub>c</sub> vengono inclusi nel set di training. I record di asset con guasti dopo il punto di taglio sono inclusi nel set di test.

Un'altra procedura consigliata per la suddivisione dei dati per il training e il testing consiste nell'uso di una suddivisione basata sull'ID asset. La suddivisione deve essere tale per cui nessuno degli asset usati nel set di training deve essere usato nel testing delle prestazioni del modello. Con questo approccio, un modello ha maggiori probabilità di fornire risultati più realistici con i nuovi asset.

### <a name="handling-imbalanced-data"></a>Gestione di dati sbilanciati
Nei problemi di classificazione, se sono presenti più esempi di una classe rispetto alle altre, si dice che il set di dati è _sbilanciato_. Idealmente, è preferibile avere abbastanza esempi rappresentativi di ogni classe dei dati di training per poter distinguere le diverse classi. Se una classe è inferiore al 10% dei dati, i dati vengono considerati sbilanciati. La classe sotto-rappresentata viene denominata _classe di minoranza_.

Molti problemi PdM implicano set di dati sbilanciati di questo tipo, in cui una classe è gravemente sotto-rappresentata rispetto alle altre classi. In alcuni casi, la classe di minoranza può costituire solo lo 0,001% dei punti dati totali. Lo sbilanciamento delle classi non è un problema specifico di PdM. Un problema simile si presenta in altri ambiti in cui errori e anomalie si presentano di rado, ad esempio il rilevamento delle frodi e delle intrusioni di rete. Questi errori costituiscono gli esempi della classe di minoranza.

In caso di sbilanciamento delle classi nei dati, le prestazioni della maggior parte degli algoritmi di apprendimento standard vengono compromesse, perché il loro scopo è ridurre il tasso di errore complessivo. Per un set di dati con il 99% di esempi positivi e l'1% di esempi negativi, un modello potrebbe risultare con un'accuratezza del 99% assegnando un'etichetta negativa a tutte le istanze. Il modello tuttavia classificherà erroneamente tutti gli esempi positivi, pertanto, anche se l'accuratezza è elevata, l'algoritmo non è utile. Di conseguenza, le metriche di valutazione convenzionali, come l'_accuratezza complessiva sul tasso di errore_, non sono sufficienti per l'apprendimento sbilanciato. Quando è necessario gestire set di dati sbilanciati, vengono usate altre metriche per la valutazione del modello:
- Precision
- Richiamo
- Punteggi F1
- Costo ROC (Receiver Operating Characteristics) rettificato

Per altre informazioni su queste metriche, vedere [Valutazione del modello](#Model-evaluation).

Sono tuttavia disponibili alcuni metodi che consentono di ovviar al problema dello sbilanciamento di classi. I due principali sono le _tecniche di campionamento_ e l'_apprendimento suscettibile alla variazione dei costi_.

#### <a name="sampling-methods"></a>Metodi di campionamento
L'apprendimento sbilanciato comporta l'uso di metodi di campionamento per modificare il training set in un set di dati bilanciato. I metodi di campionamento non devono essere applicati al set di test. Anche se sono disponibili molte tecniche di campionamento diverse, quelle più semplici sono il _sovracampionamento_ e il _sottocampionamento_ casuali.

Il _sovracampionamento casuale_ consiste nel selezionare un campione casuale dalla classe di minoranza, nel replicare questi esempi e aggiungerli al training set. Di conseguenza, viene aumentato il numero di esempi nella classe di minoranza, bilanciando infine il numero di esempi delle diverse classi. Uno svantaggio del sovracampionamento è il fatto che più istanze di determinati esempi possono rendere il classificatore troppo specifico, causando un overfitting. Il modello può mostrare un'elevata accuratezza di training, ma le prestazioni dei dati di test non visti potrebbero non essere ottimali.

Al contrario, il _sottocampionamento casuale_ consiste nel selezionare un campione casuale da una classe di maggioranza e nel rimuovere tali esempi dai training set. Tuttavia, la rimozione di esempi dalla classe di maggioranza può far sì che il classificatore ignori concetti importanti riguardanti la classe di maggioranza. Un altro approccio valido è anche il _campionamento ibrido_, in cui il sovracampionamento della classe di minoranza e il sottocampionamento della classe di maggioranza avvengono contemporaneamente.

Sono disponibili numerose tecniche di campionamento sofisticate. La tecnica scelta dipende dalle proprietà dei dati e dai risultati degli esperimenti iterativi eseguiti dal data scientist.

#### <a name="cost-sensitive-learning"></a>Apprendimento suscettibile alla variazione dei costi
In PdM, i guasti che costituiscono la classe di minoranza sono più interessanti degli esempi normali. Di conseguenza, in genere ci si concentra sulle prestazioni dell'algoritmo sui guasti. La stima errata di una classe positiva come negativa può comportare costi più elevati rispetto al contrario. Questa situazione è comunemente detta perdita ineguale o costi asimmetrici dell'errata classificazione di elementi in classi diverse. Il classificatore ideale deve fornire un'elevata accuratezza della stima rispetto alla classe di minoranza, senza compromettere l'accuratezza per la classe di maggioranza.

Esistono diversi modi per raggiungere questo equilibrio. Per ridurre il problema della perdita ineguale, assegnare un costo elevato all'errata classificazione della classe di minoranza e provare a ridurre il costo complessivo. Algoritmi come le _macchine a vettori di supporto (SVM)_ adottano intrinsecamente questo metodo, consentendo di specificare il costo degli esempi positivi e negativi durante il training. Analogamente, i metodi di aumento di priorità, come gli _alberi delle decisioni con boosting_, in genere forniscono ottime prestazioni in caso di dati sbilanciati.

## <a name="model-evaluation"></a>Valutazione del modello.
L'errata classificazione è un problema significativo per gli scenari PdM in cui il costo dei falsi allarmi per l'azienda è elevato. Ad esempio, la decisione di forzare a terra un aereo in base a una stima errata di un guasto del motore può compromettere le pianificazioni e i piani di viaggio. Portare offline una macchina da una catena di montaggio può portare a una perdita di ricavi. La valutazione del modello con le metriche delle prestazioni adatte sui nuovi dati di test è pertanto fondamentale.

Le tipiche metriche delle prestazioni usate per valutare i modelli PdM sono descritte di seguito:

- L'[accuratezza](https://en.wikipedia.org/wiki/Accuracy_and_precision) è la metrica più comune usata per descrivere le prestazioni di un classificatore. Tuttavia l'accuratezza è sensibile alle distribuzioni dei dati e rappresenta una misura inefficace per gli scenari con set di dati sbilanciati. In alternativa, vengono usate altre metriche. Strumenti quali la [matrice di confusione](https://en.wikipedia.org/wiki/Confusion_matrix) vengono usati per calcolare e comprendere l'accuratezza del modello.
- La [precisione](https://en.wikipedia.org/wiki/Precision_and_recall) dei modelli PdM è correlata al tasso di falsi allarmi. Una precisione più bassa del modello in genere corrisponde a un tasso di falsi allarmi più elevato.
- Il tasso di [richiamo](https://en.wikipedia.org/wiki/Precision_and_recall) indica quanti guasti sono stati identificati correttamente nel set di test da parte del modello. Tassi di richiamo più elevati indicano che il modello identifica correttamente i veri guasti.
- Il [punteggio F1](https://en.wikipedia.org/wiki/F1_score) è la media armonica di precisione e richiamo, con un valore compreso tra 0 (peggiore) e 1 (migliore).

Per la classificazione binaria
- Una metrica molto diffusa è anche costituta dalle [curve ROC (Receiver Operating Characteristic)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic). Nelle curve ROC, le prestazioni del modello vengono interpretate in base a un punto operativo fisso nella curva ROC.
- Tuttavia, per i problemi PdM le _tabelle di classificazione decile_ e i _grafici di accuratezza_ offrono più informazioni. Considerano solo la classe positiva (guasti) e forniscono un'immagine più complessa delle prestazioni dell'algoritmo rispetto alle curve ROC.
  - Le _tabelle di classificazione decile_ vengono create usando esempi di test in ordine decrescente delle probabilità di guasto. Gli esempi ordinati vengono quindi raggruppati in decili, ad esempio il 10% di campioni con la probabilità più alta e quindi il 20%, 30% e così via. Il rapporto (tasso di veri positivi)/(baseline casuale) per ogni decile consente di stimare le prestazioni dell'algoritmo in ogni decile. La baseline casuale assume i valori 0,1, 0,2 e così via.
  - I [grafici di accuratezza](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) tracciano il tasso di veri positivi del decile rispetto al tasso di veri positivi casuale per tutti i decili. Di solito i primi decili sono l'aspetto centrale dei risultati perché mostrano i guadagni più significativi. I primi decili possono anche essere considerati rappresentativi di una condizione "a rischio" quando vengono usati per PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operazionalizzazione del modello per la manutenzione predittiva

Il vantaggio della data science si realizza solo quando il modello sottoposto a training viene reso operativo. In altre parole, il modello deve essere distribuito nei sistemi aziendali per eseguire stime basate su nuovi dati non visti in precedenza.  I nuovi dati devono essere esattamente conformi alla _firma_ del modello sottoposto a training in due modi:
- Tutte le funzionalità devono essere presenti in ogni istanza logica (ad esempio, una riga in una tabella) dei nuovi dati.
- I nuovi dati devono essere pre-elaborati, e ognuna delle funzioni deve essere progettata, esattamente come i dati di training.

Il processo precedente è descritto in diversi modi nella letteratura accademica e di settore. Tuttavia, tutte le dichiarazioni seguenti hanno lo stesso significato:
- _Assegnare un punteggio ai nuovi dati_ usando il modello
- _Applicare il modello_ ai nuovi dati
- _Operazionalizzare_ il modello
- _Distribuire_ il modello
- _Eseguire il modello_ sui nuovi dati

Come indicato in precedenza, l'operazionalizzazione del modello per PdM è diversa da attività simili. Gli scenari che implicano il rilevamento delle anomalie e il rilevamento degli errori in genere implementano un _punteggio online_ (detto anche _punteggio in tempo reale_). In questo caso, il modello _assegna un punteggio_ a ogni record in ingresso e restituisce una stima. Per il rilevamento delle anomalie, la stima è un'indicazione del fatto che si è verificata un'anomalia (esempio: SVM a una classe). Per il rilevamento degli errori, è il tipo o la classe di errore.

Al contrario, PdM comporta un _punteggio batch_. Per essere conformi alla firma del modello, le funzioni nei nuovi dati devono essere progettate nello stesso modo dei dati di training. Per i set di dati di grandi dimensioni che sono tipici per i nuovi dati, le funzioni sono aggregate in intervalli di tempo e i punteggi vengono assegnati in batch. Il punteggio batch in genere viene assegnato in sistemi distribuiti, come [Spark](http://spark.apache.org/) o [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Esistono un paio di alternative, entrambi non ottimali:
- I motori per i dati di streaming supportano l'aggregazione sulle finestre in memoria, pertanto si potrebbe sostenere che supportano il punteggio online. Tuttavia, questi sistemi sono adatti per dati ad alta densità in finestre temporali ristrette o per elementi di tipo sparse in finestre più ampie. Potrebbero non offrire una scalabilità adeguata per i dati ad alta densità in finestre temporali più ampie, come illustrato negli scenari PdM.
- Se il punteggio batch non è disponibile, la soluzione è adattare il punteggio online per gestire i nuovi dati in piccoli batch, uno alla volta.

## <a name="solution-templates-for-predictive-maintenance"></a>Modelli di soluzioni per la manutenzione predittiva

La sezione finale di questa guida fornisce un elenco di modelli di soluzioni PdM, esercitazioni ed esperimenti implementati in Azure. In alcuni casi, queste applicazioni PdM possono essere distribuite in una sottoscrizione di Azure in pochi minuti. Possono essere usate come demo di modelli di verifica, ambienti sandbox per sperimentare alternative o acceleratori per le implementazioni effettive in produzione. Questi modelli sono disponibili in [Azure AI Gallery](http://gallery.azure.ai) o nel repository [GitHub di Azure](https://github.com/Azure). Questi diversi esempi verranno distribuiti nel modello di soluzione nel corso del tempo.

| # | Title | DESCRIZIONE |
|--:|:------|-------------|
| 1 | [Esempio di apprendimento automatico per la manutenzione predittiva di Azure](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |Esempio PdM per la stima dei guasti entro N unità di tempo. Questo esempio è scritto come progetto di Azure Machine Learning Workbench ed è ideale per chi non ha familiarità con PdM. [Documentazione aggiuntiva](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) correlata a questo esempio.|
| 2 | [Modello di soluzione per la manutenzione predittiva di Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Un framework end-to-end per illustrare più scenari PdM. Questo modello presenta due scenari: il primo è un nuovo caso d'uso per la classificazione delle condizioni di errore in tempo reale. Il secondo scenario è semplicemente un'integrazione della soluzione [1] in questo modello di soluzione. Descrive come riutilizzare la stessa infrastruttura distribuita per aggiungere altri scenari nuovi o esistenti.|
| 3 | [Apprendimento avanzato per la manutenzione predittiva](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Notebook di Azure con una soluzione demo sull'uso di reti LSTM (Long Short-Term Memory), una classe delle reti neurali ricorrenti, per la manutenzione predittiva, con un [post di blog su questo esempio](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Guida alla modellazione per la manutenzione predittiva in R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guida alla modellazione PdM con script in R.|
| 5 | [Manutenzione predittiva di Azure per il settore aerospaziale](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Uno dei primi modelli di soluzione PdM basati su Azure Machine Learning 1.0 per la manutenzione di aerei. La guida ha avuto origine da questo progetto. |
| 6 | [Azure AI Toolkit per IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | Intelligenza artificiale in IoT Edge tramite TensorFlow. Il toolkit consente di creare pacchetti con modelli di apprendimento avanzato per i contenitori Docker compatibili con Azure IoT Edge e di esporre tali modelli come API REST.
| 7 | [Manutenzione predittiva di Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS - Soluzione preconfigurata. Modello PdM con IoT Suite per la manutenzione di aerei. [Un altro documento](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) e una [procedura dettagliata](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) correlati allo stesso progetto. |
| 8 | [Modello di manutenzione predittiva con SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo dello scenario relativo alla vita utile rimanente basata su R Services. |
| 9 | [Predictive Maintenance Modeling Guide](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva) | Funzione del set di dati per la manutenzione di aerei progettata tramite R con [esperimenti](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) e [set di dati](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1), oltre a [notebook di Azure](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) ed [esperimenti](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) in Azure ML 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Risorse di formazione per la manutenzione predittiva

Il [percorso di apprendimento di AI Azure per la manutenzione predittiva](https://github.com/Azure/AI-PredictiveMaintenance/blob/master/docs/azure-ai-learning-path-for-predictive-maintenance.md) fornisce materiale di formazione per una migliore comprensione dei concetti e dei calcoli matematici dietro gli algoritmi e le tecniche usati nei problemi PdM. 

Microsoft Azure offre risorse di formazione e contenuti gratuiti sui concetti e le procedure relativi all'intelligenza artificiale.

| Risorsa di formazione  | Disponibilità |
|:-------------------|--------------|
| [Sviluppatore per intelligenza artificiale in Azure](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Pubblico |
| [Microsoft AI School](http://aischool.microsoft.com/learning-paths) | Pubblico |
| [Formazione su Azure AI da GitHub](http://azure.github.io/learnanalytics/public) | Pubblico |
| [LinkedIn Learning](http://www.linkedin.com/learning) | Pubblico |
| [Webinar YouTube sull'intelligenza artificiale Microsoft](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Pubblico |
| [Microsoft AI Show](http://channel9.msdn.com/Shows/AI-Show) | Pubblico |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | Per i partner Microsoft |
| [Microsoft Partner Network](http://learningportal.microsoft.com) | Per i partner Microsoft |

Sono inoltre disponibili corsi aperti online su larga scala (MOOC) sull'intelligenza artificiale offerti da istituzioni come Stanford, il MIT e altri istituti didattici.