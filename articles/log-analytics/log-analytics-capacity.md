---
title: "Soluzione di gestione della capacità di Log Analytics | Documentazione Microsoft"
description: "È possibile usare la soluzione di pianificazione della capacità di Log Analytics per determinare la capacità dei server Hyper-V gestiti da System Center Virtual Machine Manager."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 57e7fbdaa393e078b62a6d6a0b181b67d532523d
ms.openlocfilehash: c34cda0da164c711c8effc78d2af38ad8df581aa


---
# <a name="capacity-management-solution-in-log-analytics"></a>Soluzione di gestione della capacità di Log Analytics
È possibile usare la soluzione di pianificazione della capacità di Log Analytics per determinare la capacità dei server Hyper-V. Questa soluzione richiede sia System Center Operations Manager sia System Center Virtual Machine Manager. Se si usano agenti direttamente connessi, la soluzione di pianificazione delle capacità non è funzionale. Questa soluzione legge i contatori delle prestazioni nel server monitorato e invia i dati di utilizzo al servizio OMS nel cloud affinché possano essere elaborati. Viene applicata la logica ai dati di utilizzo, che vengono quindi registrati dal servizio cloud. Nel tempo, vengono identificati modelli di utilizzo e proiezioni di capacità, in base al consumo corrente.

Ad esempio, una proiezione potrebbe identificare quando saranno necessari core aggiuntivi o altra memoria per un determinato server. In questo esempio, la proiezione potrebbe indicare che entro 30 giorni sarà necessario aggiungere memoria al server. Ciò consente di pianificare un aggiornamento di memoria durante la successiva finestra di manutenzione del server.

> [!NOTE]
> La soluzione di gestione della capacità non può essere aggiunta ad aree di lavoro, ma i clienti che hanno già installato la soluzione di gestione della capacità possono continuare a usarla.  
>
>

In anteprima privata è disponibile una soluzione alternativa per la gestione di limiti di capacità e prestazioni. Questa soluzione è dedicata alla risoluzione dei problemi riscontrati con la soluzione di gestione della capacità originale:

* Necessità di usare Virtual Machine Manager e Operations Manager
* Impossibilità di personalizzare o applicare filtri in base ai gruppi
* Aggregazione dei dati ogni ora, non abbastanza frequente
* Nessuna analisi a livello di macchina virtuale
* Affidabilità dei dati

Vantaggi della nuova soluzione di capacità:

* Supporto per la raccolta di dati granulari con maggiore affidabilità e precisione
* Supporto per Hyper-V anche senza VMM
* Analisi di utilizzo a livello di macchina virtuale

La nuova soluzione richiede Hyper-V Server 2012 o versione successiva. La soluzione fornisce informazioni approfondite sull'ambiente Hyper-V e offre visibilità sull'uso generale (CPU, memoria e disco) dell'host e delle macchine virtuali in esecuzione sui server Hyper-V. Vengono acquisite le metriche relative a CPU, memoria e disco di tutti gli host e delle macchine virtuali in esecuzione su tali server.

La restante documentazione di questa pagina si riferisce alla soluzione di gestione della capacità precedente. Questa documentazione verrà aggiornata quando la nuova soluzione sarà disponibile in anteprima pubblica.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* Per la soluzione di gestione della capacità è necessario Operations Manager.
* Per la soluzione di gestione della capacità è necessario Virtual Machine Manager.
* È necessario che Operations Manager sia connesso a Virtual Machine Manager (VMM). Per altre informazioni sulla connessione tra i sistemi, vedere [Come connettere VMM con Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
* Operations Manager deve essere connesso a Log Analytics.
* Aggiungere la soluzione di gestione della capacità all'area di lavoro di OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

## <a name="capacity-management-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati per la gestione della capacità
Capacity Management raccoglie dati relativi alle prestazioni, metadati e dati dello stato tramite gli agenti abilitati.

La tabella seguente illustra i metodi di raccolta dei dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per la gestione della capacità.

| piattaforma | Agente diretto | Agente di Operations Manager | Archiviazione di Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![No](./media/log-analytics-capacity/oms-bullet-red.png) |![Sì](./media/log-analytics-capacity/oms-bullet-green.png) |![No](./media/log-analytics-capacity/oms-bullet-red.png) |![Sì](./media/log-analytics-capacity/oms-bullet-green.png) |![Sì](./media/log-analytics-capacity/oms-bullet-green.png) |ogni ora |

La tabella seguente illustra esempi di tipi di dati raccolti da Capacity Management:

| **Tipo di dati** | **Fields** |
| --- | --- |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Prestazioni |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stato |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="capacity-management-page"></a>Pagina di gestione della capacità
Dopo l'installazione della soluzione di pianificazione della capacità, è possibile visualizzare la capacità dei server monitorati usando il riquadro **Pianificazione della capacità** nella pagina **Panoramica** di OMS.

![image of Capacity Planning tile](./media/log-analytics-capacity/oms-capacity01.png)

Il riquadro apre il dashboard **Capacity Management** , in cui è possibile visualizzare un riepilogo della capacità del server. Questa pagina include i riquadri selezionabili seguenti:

* *Virtual machine count*: mostra il numero di giorni rimanenti per la capacità delle macchine virtuali
* *Compute*: mostra i core del processore e la memoria disponibile
* *Storage*: mostra lo spazio su disco usato e la latenza media del disco
* *Ricerca*: strumento di esplorazione dei dati che può essere usato per cercare qualsiasi tipo di dati nel sistema OMS

![image of Capacity Management dashboard](./media/log-analytics-capacity/oms-capacity02.png)

### <a name="to-view-a-capacity-page"></a>Per visualizzare una pagina relativa alla capacità
* Nella pagina **Panoramica**fare clic su **Gestione della capacità** e quindi su **Calcolo** o **Archiviazione**.

## <a name="compute-page"></a>Pagina Compute
Per visualizzare le informazioni sulla capacità relative all'utilizzo, ai giorni di capacità previsti e all'efficienza dell'infrastruttura, è possibile usare il dashboard **Calcolo** di Microsoft Azure OMS. L'area **Utilization** può essere usata per visualizzare l'utilizzo di memoria e di core CPU negli host delle macchine virtuali. Per stimare la quantità di capacità che dovrà essere disponibile per un determinato intervallo di date, è possibile usare lo strumento di proiezione. L'area **Efficiency** può essere usata per visualizzare il livello di efficienza degli host delle macchine virtuali. È possibile visualizzare i dettagli relativi agli elementi collegati facendo clic su questi ultimi.

È possibile generare una cartella di lavoro di Excel per le categorie seguenti:

* Primi host con il massimo utilizzo di core
* Primi host con il massimo utilizzo di memoria
* Primi host con macchine virtuali non efficienti
* Primi host per utilizzo
* Ultimi host per utilizzo

![image of Capacity Management Compute page](./media/log-analytics-capacity/oms-capacity03.png)

Le aree seguenti sono visualizzate nel dashboard **Compute** :

**Utilization**: mostra l'utilizzo di memoria e di core CPU negli host di macchina virtuale.

* *Used Cores*: somma relativa a tutti gli host (% della CPU usata moltiplicata per il numero di core fisici nell'host).
* *Free Cores*: core fisici totali meno i core usati.
* *Percentage Cores Available*: numero di core fisici disponibili diviso per il numero totale di core fisici.
* *Virtual Cores per VM*: numero di core virtuali totali nel sistema diviso per il numero totale di macchine virtuali nel sistema.
* *Virtual Cores to Physical Cores Ratio*: rapporto tra core fisici totali e core fisici usati dalle macchine virtuali nel sistema.
* *Number of virtual Cores Available*: rapporto tra memorie centrali virtuali e  fisiche moltiplicato per le memorie centrali fisiche disponibili.
* *Used Memory*: somma della memoria usata da tutti gli host.
* *Free Memory*: memoria fisica totale meno memoria usata.
* *Percentage Memory Available*: memoria fisica disponibile divisa per la memoria fisica totale.
* *Virtual Memory per VM*: memoria virtuale totale nel sistema divisa per il numero totale di macchine virtuali nel sistema.
* *Virtual Memory to Physical Memory Ratio*: memoria virtuale totale nel sistema divisa per la memoria fisica totale nel sistema.
* *Virtual Memory Available*: rapporto tra memoria virtuale e memoria fisica moltiplicato per la memoria fisica disponibile.

**Strumento di proiezione**

Con lo strumento di proiezione è possibile visualizzare le tendenze cronologiche relative all'uso delle risorse. Sono incluse le tendenze di uso per macchine virtuali, memoria, core e archiviazione. La funzionalità di proiezione usa un algoritmo specifico per informare l'utente quando sta per esaurirsi ognuna delle risorse. In questo modo è possibile calcolare una pianificazione della capacità appropriata, per sapere quando è necessario acquistare capacità aggiuntiva (ad esempio memoria, core o archiviazione).

**Efficiency**

* *Idle VM*: uso inferiore del 10% della CPU e del 10% della memoria per l'intervallo di tempo specificato.
* *Overutilized VM*: uso maggiore del 90% della CPU e del 90% della memoria per l'intervallo di tempo specificato.
* *Idle Host*: uso inferiore del 10% della CPU e del 10% della memoria per l'intervallo di tempo specificato.
* *Overutilized Host*: uso maggiore del 90% della CPU e del 90% della memoria per l'intervallo di tempo specificato.

### <a name="to-work-with-items-on-the-compute-page"></a>Per usare gli elementi nella pagina Compute
1. Nel dashboard **Utilizzo** della pagina **Calcolo** visualizzare le informazioni sulla capacità relative ai core CPU e alla memoria in uso.
2. Fare clic su un elemento per aprirlo nella pagina **Search** e visualizzarne le informazioni dettagliate.
3. Nello strumento **Projection** spostare il dispositivo di scorrimento data per visualizzare una proiezione della capacità che verrà usata nella data scelta.
4. Nell'area **Efficiency** visualizzare informazioni sull'efficienza della capacità relative a macchine virtuali e host delle macchine virtuali.

## <a name="direct-attached-storage-page"></a>Pagina Direct Attached Storage
È possibile usare il dashboard **Direct Attached Storage** (Archiviazione diretta) di OMS per visualizzare informazioni sulla capacità relative all'utilizzo dello spazio di archiviazione, alle prestazioni dei dischi e alle proiezioni in giorni della capacità di tali dischi. È possibile usare l'area **Utilization** per visualizzare l'uso dello spazio su disco negli host delle macchine virtuali. È possibile usare l'area **Disk Performance** per visualizzare la velocità effettiva e la latenza dei dischi negli host delle macchine virtuali. È anche possibile usare lo strumento di proiezione per stimare la quantità di capacità necessaria in un determinato intervallo di date. È possibile visualizzare i dettagli relativi agli elementi collegati facendo clic su questi ultimi.

Da queste informazioni sulla capacità è possibile generare una cartella di lavoro di Excel per le categorie seguenti:

* Massimo uso di spazio su disco da parte dell'host
* Massima latenza media da parte dell'host

Le aree seguenti sono visualizzate nella pagina **Storage** :

* *Utilization*: visualizzare l'utilizzo dello spazio su disco negli host macchine virtuali.
* *Total Disk Space*: somma (spazio logico su disco) per tutti gli host
* *Used Disk Space*: somma (spazio logico su disco utilizzato) per tutti gli host
* *Available Disk Space*: spazio totale meno spazio usato su disco
* *Percentage Disk Used*: spazio usato su disco diviso per lo spazio totale
* *Percentage Disk Available*: spazio disponibile su disco diviso per lo spazio totale

![image of Capacity Management Direct Attached Storage page](./media/log-analytics-capacity/oms-capacity04.png)

**Disk Performance**

Con OMS è possibile visualizzare la tendenza cronologica di utilizzo dello spazio su disco. La funzionalità di proiezione usa un algoritmo per l'uso futuro del progetto. Per l'uso dello spazio in particolare, la funzionalità di proiezione consente di prevedere quando lo spazio su disco potrebbe divenire insufficiente. La proiezione aiuta a pianificare un'adeguata archiviazione e a sapere quando sarà necessario acquistare altro spazio di archiviazione.

**Strumento di proiezione**

Grazie allo strumento di proiezione è possibile visualizzare le tendenze cronologiche dell'uso dello spazio su disco. La funzionalità di proiezione consente inoltre di prevedere quando lo spazio su disco potrebbe divenire insufficiente. La proiezione aiuta a pianificare un'adeguata capacità e a sapere quando sarà necessario acquistare altra capacità di archiviazione.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>Per usare gli elementi della pagina Direct Attached Storage
1. Nel dashboard **Utilizzo** della pagina **Direct Attached Storage** (Archiviazione diretta) è possibile visualizzare le informazioni relative all'utilizzo dei dischi.
2. Fare clic su un elemento collegato per aprirlo nella pagina **Search** e visualizzarne le informazioni dettagliate.
3. Nell'area **Disk Performance** è possibile visualizzare informazioni sulla velocità effettiva e sulla latenza dei dischi.
4. Nello **strumento di proiezione**spostare il dispositivo di scorrimento data per visualizzare una proiezione della capacità che verrà usata nella data scelta.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare informazioni dettagliate sulla gestione della capacità, usare [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) .



<!--HONumber=Jan17_HO1-->


