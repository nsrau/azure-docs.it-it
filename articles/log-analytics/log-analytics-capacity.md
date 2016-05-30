<properties
	pageTitle="Soluzione di gestione della capacità di Log Analytics | Microsoft Azure"
	description="È possibile usare la soluzione di pianificazione della capacità di Log Analytics per determinare la capacità dei server Hyper-V gestiti da System Center Virtual Machine Manager"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="banders"/>

# Soluzione di gestione della capacità di Log Analytics


È possibile usare la soluzione di pianificazione della capacità di Log Analytics per determinare la capacità dei server Hyper-V gestiti da System Center Virtual Machine Manager. Questa soluzione richiede sia System Center Operations Manager sia System Center Virtual Machine Manager. La soluzione di pianificazione della capacità non è disponibile se si usa solo agenti connessi direttamente. Viene installata per aggiornare l'agente di Operations Manager. Questa soluzione legge i contatori delle prestazioni nel server monitorato e invia i dati di utilizzo al servizio OMS nel cloud affinché possano essere elaborati. Viene applicata la logica ai dati di utilizzo, che vengono quindi registrati dal servizio cloud. Nel tempo, vengono identificati modelli di utilizzo e proiezioni di capacità, in base al consumo corrente.

Ad esempio, una proiezione potrebbe identificare quando saranno necessari core aggiuntivi o altra memoria per un determinato server. In questo esempio, la proiezione potrebbe indicare che entro 30 giorni sarà necessario aggiungere memoria al server. Ciò consente di pianificare un aggiornamento di memoria durante la successiva finestra di manutenzione del server, che potrebbe verificarsi una volta ogni due settimane.

>[AZURE.NOTE] La soluzione di gestione della capacità non può essere aggiunta ad aree di lavoro, ma i clienti che hanno già installato la soluzione di gestione della capacità possono continuare a usarla.

La soluzione di pianificazione della capacità è in corso di aggiornamento al fine di risolvere i seguenti problemi segnalati dai clienti:

- Necessità di usare Virtual Machine Manager e Operations Manager
- Impossibilità di personalizzare o applicare filtri in base ai gruppi
- Aggregazione dei dati ogni ora, non abbastanza frequente
- Nessuna analisi a livello di macchina virtuale
- Affidabilità dei dati

Vantaggi della nuova soluzione di capacità:

- Supporto per la raccolta di dati granulari con maggiore affidabilità e precisione
- Supporto per Hyper-V anche senza VMM
- Visualizzazione delle metriche in Power BI
- Analisi di utilizzo a livello di macchina virtuale


## Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

- Per la soluzione di gestione della capacità è necessario Operations Manager.
- Per la soluzione di gestione della capacità è necessario Virtual Machine Manager.
- È necessario che Operations Manager sia connesso a Virtual Machine Manager (VMM). Per informazioni aggiuntive sulla connessione tra i sistemi, vedere [Modalità di connessione di VMM con Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
- Operations Manager deve essere connesso a Log Analytics.
- Aggiungere la soluzione di gestione della capacità all'area di lavoro di OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.


## Informazioni dettagliate sulla raccolta dei dati per la gestione della capacità

La tabella seguente illustra i metodi di raccolta dei dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per la gestione della capacità.

| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|
|Windows|![No](./media/log-analytics-capacity/oms-bullet-red.png)|![Sì](./media/log-analytics-capacity/oms-bullet-green.png)|![No](./media/log-analytics-capacity/oms-bullet-red.png)| ![Sì](./media/log-analytics-capacity/oms-bullet-green.png)|![Sì](./media/log-analytics-capacity/oms-bullet-green.png)| ogni ora|


## Pagina di gestione della capacità


 Dopo l'installazione della soluzione di pianificazione della capacità, sarà possibile visualizzare la capacità dei server monitorati usando il riquadro **Capacity Planning** (Pianificazione della capacità) della pagina **Overview** (Panoramica) di OMS.

![image of Capacity Planning tile](./media/log-analytics-capacity/oms-capacity01.png)

Il riquadro apre il dashboard **Capacity Management**, in cui è possibile visualizzare un riepilogo della capacità del server. Questa pagina include i riquadri selezionabili seguenti:

- *Virtual machine count*: mostra il numero di giorni rimanenti per la capacità delle macchine virtuali
- *Compute*: mostra i core del processore e la memoria disponibile
- *Storage*: mostra lo spazio su disco usato e la latenza media del disco
- *Search (Ricerca)*: strumento di esplorazione dei dati che può essere usato per cercare qualsiasi tipo di dati nel sistema OMS

![image of Capacity Management dashboard](./media/log-analytics-capacity/oms-capacity02.png)


### Per visualizzare una pagina relativa alla capacità

- Nella pagina **Overview** fare clic su **Capacity Management**, e quindi su **Compute** o **Storage**.

## Pagina Compute

Per visualizzare le informazioni sulla capacità relative a utilizzo, giorni di capacità previsti ed efficienza dell'infrastruttura, è possibile usare il dashboard **Compute** (Elaborazione) di Microsoft Azure OMS. L'area **Utilization** può essere usata per visualizzare l'utilizzo di memoria e di core CPU negli host delle macchine virtuali. Per stimare la quantità di capacità che dovrà essere disponibile per un determinato intervallo di date, è possibile usare lo strumento di proiezione. L'area **Efficiency** può essere usata per visualizzare il livello di efficienza degli host delle macchine virtuali. È possibile visualizzare i dettagli relativi agli elementi collegati facendo clic su questi ultimi.

È possibile generare una cartella di lavoro di Excel per le categorie seguenti:

- Primi host con il massimo utilizzo di core
- Primi host con il massimo utilizzo di memoria
- Primi host con macchine virtuali non efficienti
- Primi host per utilizzo
- Ultimi host per utilizzo

![image of Capacity Management Compute page](./media/log-analytics-capacity/oms-capacity03.png)


Le aree seguenti sono visualizzate nel dashboard **Compute**:

**Utilization**: mostra l'utilizzo di memoria e di core CPU negli host di macchina virtuale.

- *Used Cores*: somma relativa a tutti gli host (% della CPU usata moltiplicata per il numero di core fisici nell'host).
- *Free Cores*: core fisici totali meno i core usati.
- *Percentage Cores Available*: numero di core fisici disponibili diviso per il numero totale di core fisici.
- *Virtual Cores per VM*: numero di core virtuali totali nel sistema diviso per il numero totale di macchine virtuali nel sistema.
- *Virtual Cores to Physical Cores Ratio*: rapporto tra core fisici totali e core fisici usati dalle macchine virtuali nel sistema.
- *Number of virtual Cores Available*: rapporto tra memorie centrali virtuali e fisiche moltiplicato per le memorie centrali fisiche disponibili.
- *Used Memory*: somma della memoria usata da tutti gli host.
- *Free Memory*: memoria fisica totale meno memoria usata.
- *Percentage Memory Available*: memoria fisica disponibile divisa per la memoria fisica totale.
- *Virtual Memory per VM*: memoria virtuale totale nel sistema divisa per il numero totale di macchine virtuali nel sistema.
- *Virtual Memory to Physical Memory Ratio*: memoria virtuale totale nel sistema divisa per la memoria fisica totale nel sistema.
- *Virtual Memory Available*: rapporto tra memoria virtuale e memoria fisica moltiplicato per la memoria fisica disponibile.

**Strumento di proiezione**

Con lo strumento di proiezione è possibile visualizzare le tendenze cronologiche relative all'uso delle risorse. Sono incluse le tendenze di uso per macchine virtuali, memoria, core e archiviazione. La funzionalità di proiezione usa un algoritmo specifico per informare l'utente quando sta per esaurirsi ognuna delle risorse. In questo modo è possibile calcolare una pianificazione della capacità appropriata, per sapere quando è necessario acquistare capacità aggiuntiva (ad esempio memoria, core o archiviazione).

**Efficiency**

- *Idle VM*: uso inferiore del 10% della CPU e del 10% della memoria per l'intervallo di tempo specificato.
- *Overutilized VM*: uso maggiore del 90% della CPU e del 90% della memoria per l'intervallo di tempo specificato.
- *Idle Host*: uso inferiore del 10% della CPU e del 10% della memoria per l'intervallo di tempo specificato.
- *Overutilized Host*: uso maggiore del 90% della CPU e del 90% della memoria per l'intervallo di tempo specificato.

### Per usare gli elementi nella pagina Compute

1. Nel dashboard **Utilization** della pagina **Compute** visualizzare le informazioni sulla capacità relative ai core CPU e alla memoria in uso.
2. Fare clic su un elemento per aprirlo nella pagina **Search** e visualizzarne le informazioni dettagliate.
3. Nello strumento **Projection** spostare il dispositivo di scorrimento data per visualizzare una proiezione della capacità che verrà usata nella data scelta.
4. Nell'area **Efficiency** visualizzare informazioni sull'efficienza della capacità relative a macchine virtuali e host delle macchine virtuali.

## Pagina Direct Attached Storage

È possibile usare il dashboard **Direct Attached Storage** di OMS per visualizzare informazioni sulla capacità relative all'utilizzo dello spazio di archiviazione, alle prestazioni dei dischi e alle proiezioni in giorni della capacità di tali dischi. È possibile usare l'area **Utilization** per visualizzare l'uso dello spazio su disco negli host delle macchine virtuali. È possibile usare l'area **Disk Performance** per visualizzare la velocità effettiva e la latenza dei dischi negli host delle macchine virtuali. È anche possibile usare lo strumento di proiezione per stimare la quantità di capacità necessaria in un determinato intervallo di date. È possibile visualizzare i dettagli relativi agli elementi collegati facendo clic su questi ultimi.

Da queste informazioni sulla capacità è possibile generare una cartella di lavoro di Excel per le categorie seguenti:

- Massimo uso di spazio su disco da parte dell'host
- Massima latenza media da parte dell'host

Le aree seguenti sono visualizzate nella pagina **Storage**:

- *Utilization*: visualizzare l'utilizzo dello spazio su disco negli host macchine virtuali.
- *Total Disk Space*: somma (spazio logico su disco) per tutti gli host
- *Used Disk Space*: somma (spazio logico su disco utilizzato) per tutti gli host
- *Available Disk Space*: spazio totale meno spazio usato su disco
- *Percentage Disk Used*: spazio usato su disco diviso per lo spazio totale
- *Percentage Disk Available*: spazio disponibile su disco diviso per lo spazio totale

![image of Capacity Management Direct Attached Storage page](./media/log-analytics-capacity/oms-capacity04.png)

**Disk Performance**

Con OMS è possibile visualizzare la tendenza cronologica di utilizzo dello spazio su disco. La funzionalità di proiezione usa un algoritmo per l'uso futuro del progetto. Per l'uso dello spazio in particolare, la funzionalità di proiezione consente di prevedere quando lo spazio su disco potrebbe divenire insufficiente. Ciò può essere utile per pianificare un'adeguata archiviazione e per sapere quando sarà necessario acquistare altro spazio di archiviazione.

**Strumento di proiezione**

Grazie allo strumento di proiezione è possibile visualizzare le tendenze cronologiche dell'uso dello spazio su disco. La funzionalità di proiezione consente inoltre di prevedere quando lo spazio su disco potrebbe divenire insufficiente. Ciò consentirà di pianificare la capacità corretta e di sapere quando sarà necessario acquistare altra capacità di archiviazione.

### Per usare gli elementi della pagina Direct Attached Storage

1. Nel dashboard **Utilization** della pagina **Direct Attached Storage** è possibile visualizzare le informazioni relative all'uso dei dischi.
2. Fare clic su un elemento collegato per aprirlo nella pagina **Search** e visualizzarne le informazioni dettagliate.
3. Nell'area **Disk Performance** è possibile visualizzare informazioni sulla velocità effettiva e sulla latenza dei dischi.
4. Nello **strumento di proiezione** spostare il dispositivo di scorrimento data per visualizzare una proiezione della capacità che verrà usata nella data scelta.


## Passaggi successivi

- Per visualizzare informazioni dettagliate sulla gestione della capacità, usare [Ricerche nei log in Log Analytics](log-analytics-log-searches.md).

<!---HONumber=AcomDC_0518_2016-->