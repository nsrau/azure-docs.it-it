<properties
 pageTitle="Dimensioni dei servizi cloud"
 description="Elenca le varie dimensioni dei ruoli Web e di lavoro del servizio cloud di Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="03/25/2016"
 ms.author="adegeo"/>

# Dimensioni dei servizi cloud

Questo argomento descrive le dimensioni e le opzioni disponibili per le istanze del ruolo di servizio cloud (ruoli Web e di lavoro). Fornisce anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse.

I servizi cloud sono uno dei diversi tipi di risorse di calcolo offerti da Azure. Fare clic [qui](cloud-services-choose-me.md) per altre informazioni sui servizi cloud.

> [AZURE.NOTE]Per i limiti di Azure correlati, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## Dimensioni delle istanze del ruolo Web e di lavoro

Le considerazioni seguenti potrebbero essere utili all’utente per scegliere una dimensione:

* Le istanze di macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

* Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswalle) e con tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,2 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

* I ruoli Web e di lavoro richiedono più spazio su disco temporaneo rispetto alle macchine virtuali di Azure a causa dei requisiti di sistema. I file di sistema riservano 4 GB di spazio per il file di paging di Windows e 2 GB di spazio per il file dump di Windows.

* Il disco del sistema operativo contiene il sistema operativo guest di Windows, la cartella Programmi (incluse le installazioni eseguite mediante attività di avvio a meno che non si specifichi un altro disco), le modifiche del Registro di sistema, la cartella System32 e .NET Framework.

* Il **disco di archiviazione temporanea** contiene i file di configurazione e i log di Azure, Diagnostica Azure, inclusi i log di IIS, e tutte le risorse di archiviazione locale definite.

* Il **disco dell’applicazione** è il disco in cui viene estratto il file con estensione cspkg e che contiene il sito Web, i file binari, il processo host del ruolo, le attività di avvio, il file web.config e così via.

* Le dimensioni delle macchine virtuali A8/A10 e A9/A11 hanno la stessa capacità. Le istanze di macchine virtuali A8 e A9 includono una scheda di rete aggiuntiva connessa a una rete con accesso diretto a memoria remota (RDMA) per una comunicazione rapida tra macchine virtuali. Le istanze A8 e A9 sono progettate per applicazioni di elaborazione ad alte prestazioni che richiedono comunicazione costante e a bassa latenza tra i nodi durante l'esecuzione, ad esempio, le applicazioni che usano l'interfaccia MPI (Message Passing Interface). Le istanze di macchina virtuale A10 e A11 non includono la scheda di rete aggiuntiva. Le istanze A10 e A11 sono progettate per applicazioni di calcolo ad alte prestazioni che non richiedono una comunicazione costante e a bassa latenza tra i nodi, note anche come applicazioni parametriche o imbarazzantemente parallele.

    >[AZURE.NOTE] Se si stanno considerando i formati da A8 ad A11, selezionare [qui](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) per altre informazioni.

>[AZURE.NOTE] Tutte le dimensioni delle macchine forniscono un **disco dell’applicazione** che archivia tutti i file del pacchetto di servizio cloud grande circa 1,5 GB.

Assicurarsi di esaminare i [prezzi](pricing/details/cloud-services/) di ogni dimensione del servizio cloud.

## Scopo generico

Per siti Web, database di piccole e medie dimensioni e altre applicazioni normali.

>[AZURE.NOTE] La capacità di archiviazione è rappresentata usando 1024 ^ 3 byte come unità di misura per GB. Questa unità di misura è definita talvolta gibibyte o definizione di base 2. Quando si confrontano dimensioni che usano sistemi di base diversi, è necessario ricordare che le dimensioni di base 2 potrebbero risultare più piccole di quelli di base 10 ma per qualsiasi dimensione specifica (ad esempio, 1 GB) un sistema di base 2 fornisce maggiore capacità di un sistema di base 10, perché 1024^3 è superiore a 1000^3.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| Molto piccola | 1 | 0,75 GB | 19 GB |
| Small | 1 | 1,75 GB | 224 GB |
| Media | 2 | 3,5 GB | 489 GB |
| Large | 4 | 7 GB | 999 GB |
| Molto grande | 8 | 14 GB | 2\.039 GB |

>[AZURE.NOTE] Da **Molto piccola** a **Molto grande** possono anche essere denominate rispettivamente **A0 A4**.

## A elevato utilizzo di memoria

Per database di grandi dimensioni, server farm di SharePoint e applicazioni a velocità effettiva elevata.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| A5 | 2 | 14 GB | 489 GB |
| A6 | 4 | 28 GB | 999 GB |
| A7 | 8 | 56 GB | 2\.039 GB |

## Soluzione ottimizzata con supporto InfiniBand

Disponibile in data center selezionati. Le macchine virtuali A8 e A9 dispongono di [processori Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Aggiunge una rete **InfiniBand** di 32 Gbit al secondo con tecnologia Accesso diretto a memoria remota (RDMA, Remote Direct Memory Access). Ideale per applicazioni MPI (Message Passing Interface), cluster a prestazioni elevate, modellazione e simulazioni, codifica video e altri scenari a elevato utilizzo di calcolo o di rete.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| A8 | 8 | 56 GB | 382 GB |
| A9 | 16 | 112 GB | 382 GB |

## A elevato utilizzo di calcolo

Disponibile in data center selezionati. Le macchine virtuali A10 e A11 dispongono di [processori Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Ideale per cluster ad alte prestazioni, modellazione e simulazioni, codifica video e altri scenari a uso intensivo di risorse di calcolo o rete. Configurazione simile alle istanze A8 e A9, senza rete InfiniBand e tecnologia RDMA.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| A10 | 8 | 56 GB | 382 GB |
| A11 | 16 | 112 GB | 382 GB |

## Serie D: calcolo ottimizzato

Le macchine virtuali di serie D offrono unità SSD (Solid State Drive) e processori più veloci del 60% rispetto alla serie A e sono disponibili anche per ruoli Web o di lavoro in Servizi cloud di Azure. Questa serie è ideale per le applicazioni che richiedono CPU più veloci, prestazioni migliori per il disco locale o quantità più elevate di memoria.

## Scopo generico (D)

Per siti Web, database di piccole e medie dimensioni e altre applicazioni normali.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| Standard\_D1 | 1 | 3,5 GB | 50 GB |
| Standard\_D2 | 2 | 7 GB | 100 GB |
| Standard\_D3 | 4 | 14 GB | 200 GB |
| Standard\_D4 | 8 | 28 GB | 400 GB |

## A elevato utilizzo di memoria (D)

Per database di grandi dimensioni, server farm di SharePoint e applicazioni a velocità effettiva elevata.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| Standard\_D11 | 2 | 14 GB | 100 GB |
| Standard\_D12 | 4 | 28 GB | 200 GB |
| Standard\_D13 | 8 | 56 GB | 400 GB |
| Standard\_D14 | 16 | 112 GB | 800 GB |

## Serie Dv2: calcolo ottimizzato

Le istanze serie Dv2 sono la nuova generazione delle istanze serie D e possono essere usate come macchine virtuali o servizi cloud. Le istanze serie Dv2 includeranno CPU più potenti e in media il 35% più veloci rispetto a quelle delle istanze serie D e avranno le stesse configurazioni di memoria e dischi della serie D. Le istanze serie Dv2 sono basate sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz di ultima generazione e con la tecnologia Intel Turbo Boost Technology 2.0 possono arrivare fino a 3,2 GHz. Le serie Dv2 e D sono ideali per le applicazioni che richiedono CPU più veloci, prestazioni migliori per i dischi locali o quantità di memoria più elevate e offrono una potente combinazione per molte applicazioni di livello aziendale.

## Scopo generico (Dv2)

Per siti Web, database di piccole e medie dimensioni e altre applicazioni normali.

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| Standard\_D1\_v2 | 1 | 3,5 GB | 50 GB |
| Standard\_D2\_v2 | 2 | 7 GB | 100 GB |
| Standard\_D3\_v2 | 4 | 14 GB | 200 GB |
| Standard\_D4\_v2 | 8 | 28 GB | 400 GB |
| Standard\_D5\_v2 | 16 | 56 GB | 800 GB |

## A elevato utilizzo di memoria (Dv2)

Per database di grandi dimensioni, server farm di SharePoint e applicazioni a velocità effettiva elevata

| Dimensioni (id) | Core | RAM | Dimensioni totali del disco |
| --------------- | :-------: | ------: | ------: |
| Standard\_D11\_v2 | 2 | 14 GB | 100 GB |
| Standard\_D12\_v2 | 4 | 28 GB | 200 GB |
| Standard\_D13\_v2 | 8 | 56 GB | 400 GB |
| Standard\_D14\_v2 | 16 | 112 GB | 800 GB |

## Configurare le dimensioni per i servizi Cloud

È possibile specificare la dimensione di un'istanza del ruolo di macchina virtuale come parte del modello del servizio descritto dal [file csdef](cloud-services-model-and-package.md#csdef). Le dimensioni di un ruolo determinano il numero di memorie centrali CPU, la capacità di memoria e la dimensione del file system locale allocati a una istanza in esecuzione. Scegliere le dimensioni del ruolo in base ai requisiti delle risorse dell'applicazione.

Di seguito è riportato un esempio per impostare la dimensione del ruolo [Standard\_D2](#general-purpose-d) per un'istanza di ruolo Web:

```xml
<WebRole name="WebRole1" vmsize="<mark>Standard_D2</mark>">
...
</WebRole>
```

<!---HONumber=AcomDC_0420_2016-->