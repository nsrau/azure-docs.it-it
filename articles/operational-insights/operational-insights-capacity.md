<properties
   pageTitle="Gestire la capacità di infrastruttura"
   description="Informazioni sull'uso della soluzione Capacity Planning in Operational Insights per stabilire e comprendere la capacità dell'infrastruttura server"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Gestire la capacità di infrastruttura

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile usare la soluzione Capacity Planning in Microsoft Azure Operational Insights per determinare la capacità dell'infrastruttura server. Installare la soluzione per aggiornare l’agente Operation Manager e il modulo di configurazione di base in Operational Insights. Questa soluzione legge i contatori delle prestazioni nel server monitorato e invia i dati di utilizzo al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati di utilizzo, che vengono quindi registrati dal servizio cloud. Nel tempo, vengono identificati modelli di utilizzo e proiezioni di capacità, in base al consumo corrente.

Ad esempio, una proiezione potrebbe identificare quando saranno necessari core aggiuntivi o altra memoria per un determinato server. In questo esempio, la proiezione potrebbe indicare che entro 30 giorni sarà necessario aggiungere memoria al server. Ciò consente di pianificare un aggiornamento di memoria durante la successiva finestra di manutenzione del server, che potrebbe verificarsi una volta ogni due settimane.

## Dashboard Capacity Management

Prima di poter usare il dashboard Capacity Management in Microsoft Azure Operational Insights, è necessario disporre della soluzione installata. Per altre informazioni sull'installazione di soluzioni, vedere [Usare la raccolta di soluzioni per aggiungere o rimuovere soluzioni](operational-insights-add-solution.md). Dopo l'installazione della soluzione Capacity Planning, sarà possibile visualizzare la capacità dei server monitorati usando il riquadro **Capacity Planning** della pagina **Overview** in Operational Insights.

![image of Capacity Planning tile](./media/operational-insights-capacity/overview-cap-plan.png)

Il riquadro apre il dashboard **Capacity Management**, in cui è possibile visualizzare un riepilogo della capacità del server. Questa pagina include i riquadri selezionabili seguenti:

- *Virtual machine count*: mostra il numero di giorni rimanenti per la capacità delle macchine virtuali

- *Compute*: mostra i core del processore e la memoria disponibile

- *Storage*: mostra lo spazio su disco usato e la latenza media del disco

- *Search*: strumento di esplorazione dei dati che può essere usato per cercare qualsiasi dato nel sistema Operational Insights

>[AZURE.NOTE]Per visualizzare i dati relativi alla gestione della capacità, è necessario abilitare la connettività di Operations Manager con Virtual Machine Manager (VMM). Per altre informazioni sulla connessione tra i sistemi, vedere Modalità di connessione di VMM con Operations Manager.

![image of Capacity Management dashboard](./media/operational-insights-capacity/gallery-capacity-01.png)

### Per visualizzare una pagina relativa alla capacità

- Nella pagina **Overview** fare clic su **Capacity Management**, e quindi su **Compute** o **Storage**.

## Pagina Compute

Per visualizzare le informazioni sulla capacità relative a utilizzo, giorni di capacità previsti ed efficienza dell'infrastruttura, è possibile usare il dashboard **Compute** in Microsoft Azure Operational Insights. L'area **Utilization** può essere usata per visualizzare l'utilizzo di memoria e di core CPU negli host delle macchine virtuali. Per stimare la quantità di capacità che dovrà essere disponibile per un determinato intervallo di date, è possibile usare lo strumento di proiezione. L'area **Efficiency** può essere usata per visualizzare il livello di efficienza degli host delle macchine virtuali. È possibile visualizzare i dettagli relativi agli elementi collegati facendo clic su questi ultimi.

È possibile generare una cartella di lavoro di Excel per le categorie seguenti:

- Primi host con il massimo utilizzo di core

- Primi host con il massimo utilizzo di memoria

- Primi host con macchine virtuali non efficienti

- Primi host per utilizzo

- Ultimi host per utilizzo

![image of Capacity Management Compute page](./media/operational-insights-capacity/gallery-capacity-02.png)


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

3. Nell'area **Efficiency** visualizzare informazioni sull'efficienza della capacità relative a macchine virtuali e host delle macchine virtuali.

##Pagina Direct Attached Storage

È possibile usare il dashboard **Direct Attached Storage** in Microsoft Azure Operational Insights per visualizzare informazioni sulla capacità relative all'utilizzo dello spazio di archiviazione, alle prestazioni dei dischi e alle proiezioni in giorni della capacità di tali dischi. È possibile usare l'area **Utilization** per visualizzare l'uso dello spazio su disco negli host delle macchine virtuali. È possibile usare l'area **Disk Performance** per visualizzare la velocità effettiva e la latenza dei dischi negli host delle macchine virtuali. È anche possibile usare lo strumento di proiezione per stimare la quantità di capacità necessaria in un determinato intervallo di date. È possibile visualizzare i dettagli relativi agli elementi collegati facendo clic su questi ultimi.

Da queste informazioni sulla capacità è possibile generare una cartella di lavoro di Excel per le categorie seguenti:

- Massimo uso di spazio su disco da parte dell'host

- Massima latenza media da parte dell'host

Le aree seguenti sono visualizzate nella pagina **Storage**:

- *Utilization*: visualizzare l’utilizzo dello spazio su disco negli host macchine virtuali.

- *Total Disk Space*: somma (spazio logico su disco) per tutti gli host

- *Used Disk Space*: somma (spazio logico su disco utilizzato) per tutti gli host

- *Available Disk Space*: spazio totale meno spazio usato su disco

- *Percentage Disk Used*: spazio usato su disco diviso per lo spazio totale

- *Percentage Disk Available*: spazio disponibile su disco diviso per lo spazio totale

![image of Capacity Management Direct Attached Storage page](./media/operational-insights-capacity/gallery-capacity-03.png)

**Disk Performance**

Usando Operational Insights, è possibile visualizzare la tendenza di utilizzo cronologica dello spazio su disco. La funzionalità di proiezione usa un algoritmo per l'uso futuro del progetto. Per l'uso dello spazio in particolare, la funzionalità di proiezione consente di prevedere quando lo spazio su disco potrebbe divenire insufficiente. Ciò può essere utile per pianificare un'adeguata archiviazione e per sapere quando sarà necessario acquistare altro spazio di archiviazione.

**Strumento di proiezione**

Grazie allo strumento di proiezione è possibile visualizzare le tendenze cronologiche dell'uso dello spazio su disco. La funzionalità di proiezione consente inoltre di prevedere quando lo spazio su disco potrebbe divenire insufficiente. Ciò consentirà di pianificare la capacità corretta e di sapere quando sarà necessario acquistare altra capacità di archiviazione.

### Per usare gli elementi della pagina Direct Attached Storage

1. Nel dashboard **Utilization** della pagina **Direct Attached Storage** è possibile visualizzare le informazioni relative all'uso dei dischi.

2. Fare clic su un elemento collegato per aprirlo nella pagina **Search** e visualizzarne le informazioni dettagliate.

3. Nell'area **Disk Performance** è possibile visualizzare informazioni sulla velocità effettiva e sulla latenza dei dischi.

4. Nello **strumento di proiezione** spostare il dispositivo di scorrimento data per visualizzare una proiezione della capacità che verrà usata nella data scelta.

<!---HONumber=July15_HO2-->