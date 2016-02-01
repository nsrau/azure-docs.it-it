<properties
   pageTitle="Pianificazione della capacità per le app di Service Fabric | Microsoft Azure"
   description="Descrive come identificare il numero di nodi di calcolo necessari per un'applicazione dell'infrastruttura di servizi"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/12/2015"
   ms.author="subramar"/>


# Pianificazione della capacità per le applicazioni Service Fabric


Questo documento illustra come stimare la quantità di risorse (CPU, RAM e spazio di archiviazione su disco) necessaria per eseguire le applicazioni di Azure Service Fabric. I requisiti delle risorse tendono a cambiare nel tempo. In genere sono necessarie poche risorse durante lo sviluppo e il test del servizio e un numero maggiore nella fase di produzione e di aumento della popolarità dell'applicazione. Quando si progetta un'applicazione, si consiglia di prendere in considerazione i requisiti a lungo termine e di adottare subito i provvedimenti necessari affinché il servizio possa essere scalato facilmente per soddisfare le richieste elevate dei clienti.

 Quando si crea un cluster di Service Fabric, si definiscono i tipi di macchine virtuali (VM) che costituiranno il cluster. Ogni VM è dotata di una quantità limitata di risorse sotto forma di CPU (core e velocità), larghezza di banda di rete, RAM e spazio di archiviazione su disco. Con la crescita del servizio nel tempo, è possibile aggiornare le VM affinché offrano un numero maggiore di risorse e/o aggiungere VM al cluster. Per la seconda opzione, è chiaramente necessario progettare il servizio in modo che possa sfruttare i vantaggi offerti dall'aggiunta di nuove VM al cluster in modo dinamico.

Alcuni servizi gestiscono pochi dati o addirittura nessun dato nelle VM. Pertanto, la pianificazione della capacità per questi servizi deve riguardare principalmente le prestazioni. Ciò significa che è necessario valutare attentamente le prestazioni degli algoritmi del codice e le CPU delle VM (core e velocità) necessarie per eseguire gli algoritmi. È consigliabile valutare anche la larghezza di banda della rete, compresa la frequenza con cui si verificano trasferimenti di rete nonché la quantità dei dati trasferiti. Se il servizio deve mantenere un buon livello di prestazioni con l'aumento dell'utilizzo, è possibile aggiungere altre VM al cluster e bilanciare il carico delle richieste di rete in tutte le VM.

Per i servizi che gestiscono una grande quantità di dati nelle VM, la pianificazione della capacità deve concentrarsi soprattutto sulle dimensioni. È necessario pertanto valutare attentamente la capacità della RAM e dello spazio di archiviazione su disco della VM. Per il codice dell'applicazione il sistema di gestione della memoria virtuale di Windows rende lo spazio di archiviazione su disco analogo alla RAM. In questo modo, le applicazioni possono usare più memoria di quella fisicamente disponibile nella VM. Una maggiore disponibilità di RAM consente di aumentare le prestazioni, in quanto la VM può sfruttare più spazio di archiviazione su disco nella RAM. Quando si sceglie una VM, selezionarne una con uno spazio di archiviazione su disco sufficiente per contenere tutti i dati che si vuole inserire nella VM e scegliere una quantità di RAM che consenta di accedere ai dati alla velocità desiderata. Se i dati del servizio aumentano nel tempo, è possibile aggiungere altre VM al cluster e partizionare i dati in tutte le VM.

## Determinare il numero di nodi necessari

Il partizionamento del servizio consente di scalare orizzontalmente i dati del servizio. Per altre informazioni sul partizionamento, vedere l'articolo relativo al [partizionamento dell'infrastruttura di servizi](service-fabric-concepts-partitioning.md). Ogni partizione deve essere contenuta in una singola VM, ma è possibile posizionare più partizioni (di piccole dimensioni) in una singola VM. La disponibilità di un più ampio numero di partizioni di piccole dimensioni offre quindi una maggiore flessibilità rispetto alla disponibilità di un numero ridotto di partizioni di dimensioni maggiori. Lo svantaggio è rappresentato dal fatto che la presenza di numerose partizioni aumenta il sovraccarico di Service Fabric impedendo l'esecuzione di operazioni transazionali tra le partizioni. È inoltre disponibile un maggiore traffico di rete potenziale, se il codice del servizio deve accedere spesso a porzioni di dati che si trovano in partizioni diverse. Quando si progetta il servizio, è opportuno valutare attentamente questi vantaggi e svantaggi per adottare un'efficace strategia di partizionamento.

Si supponga che l'applicazione abbia un singolo servizio con stato con una dimensione di archiviazione che si prevede raggiunga i GB della dimensione del database in un anno. Si è disposti ad aggiungere altre applicazioni e partizioni, man mano che le dimensioni aumenteranno dopo tale anno. Per individuare la dimensione totale del database nelle repliche, è necessario tenere in considerazione anche il fattore di replica, che determina il numero di repliche per il servizio (la dimensione totale del database nelle repliche è il fattore di replica moltiplicato per la dimensione del database). Node\_Size rappresenta lo spazio di archiviazione su disco o RAM per ogni nodo che si vuole usare per il servizio. Per ottenere prestazioni ottimali, sarà necessario che il database si adatti alla memoria del cluster e si dovrà inserire un elemento Node\_Size corrispondente alla capacità della RAM della VM scelta. Allocando un elemento Node\_Size maggiore della capacità di RAM, si fa affidamento sul paging del sistema operativo. Le prestazioni potrebbero pertanto non essere ottimali, ma potrebbero comunque essere sufficienti per il servizio.

Il numero di nodi necessari per ottenere le massime prestazioni può essere calcolato come segue:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Account per l'espansione

Si consiglia di calcolare il numero dei nodi in base alla dimensione del database che si prevede di raggiungere, oltre alla dimensione con cui si dà avvio al servizio. Aumentare quindi il numero di nodi man mano che il servizio cresce, in modo da evitare l'overprovisioning del numero di nodi. Il numero di partizioni si deve tuttavia basare sul numero di nodi necessari quando si esegue il servizio al livello massimo di espansione.

È inoltre opportuno avere poche macchine di riserva (capacità in eccesso) disponibili in qualsiasi momento, in modo da poter gestire picchi imprevisti o errori dell'infrastruttura (ad esempio, se alcune VM smettono di funzionare). Anche se questo aspetto può essere determinato facendo riferimento ai picchi previsti, un buon punto di partenza è riservare alcune VM aggiuntive (5-10%).

Quanto appena descritto presuppone l'esistenza di un singolo servizio con stato. Se si hanno più servizi con stato, sarà necessario aggiungere anche la dimensione del database associata agli altri servizi nell'equazione o calcolare il numero di nodi separatamente per ogni servizio con stato. Il servizio può includere repliche o partizioni non bilanciate. Alcune partizioni possono avere più dati rispetto ad altre pertanto per altre informazioni, fare riferimento all'[articolo sulle procedure consigliate relative alla partizione](service-fabric-concepts-partitioning.md). Tuttavia, l'equazione precedente non tiene conto delle partizioni e delle repliche, poiché Service Fabric garantisce che le repliche siano distribuite tra i nodi in modalità ottimizzata.


## Usare un foglio di calcolo per il calcolo dei costi

È ora opportuno inserire alcuni dati reali nella formula specificata in precedenza. Un [foglio di calcolo di esempio](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) illustra come pianificare la capacità di un'applicazione contenente tre tipi di oggetti dati. Per ogni oggetto sono stati definiti in modo approssimativo la dimensione e il numero di oggetti che dovrebbe contenere. Viene anche definito il numero di repliche per ogni tipo di oggetto. Il foglio di calcolo consente di calcolare la quantità totale di memoria da archiviare nel cluster.

Viene quindi immessa una dimensione di VM e il costo mensile. In base alla dimensione della VM, il foglio di calcolo indica il numero minimo di partizioni da usare per suddividere i dati affinché possano fisicamente adattarsi ai nodi. È possibile che si desideri un numero maggiore di partizioni per soddisfare le esigenze di calcolo specifiche dell'applicazione e del traffico di rete. Il foglio di calcolo illustra che il numero di partizioni che gestiscono gli oggetti del profilo utente è aumentato da 1 a 6.

A questo punto, in base a tali informazioni, il foglio di calcolo mostra che è possibile contenere tutti i dati con le partizioni e le repliche desiderate in un cluster a 26 nodi. Tale cluster risulta tuttavia compresso e possono essere necessari alcuni nodi aggiuntivi per gestire gli aggiornamenti e gli errori del nodo. Il foglio di calcolo mostra inoltre che la disponibilità di più di 57 nodi non offre alcun valore aggiuntivo in quanto alcuni nodi possono rimanere vuoti. Anche in questo caso, può essere comunque necessario superare i 57 nodi per gestire gli aggiornamenti e gli errori del nodo. È possibile apportare modifiche al foglio di calcolo in modo che corrisponda alle esigenze specifiche dell'applicazione.

![Foglio di calcolo per il calcolo dei costi][Image1]



## Passaggi successivi

Per altre informazioni sul partizionamento del servizio, consultare [Partizionamento dei servizi Service Fabric][10].



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=AcomDC_0121_2016-->