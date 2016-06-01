
Per i limiti generali delle macchine virtuali di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../articles/azure-subscription-service-limits.md).

Le dimensioni standard sono costituite da più serie: A, D, DS, G e GS. Si tengano presenti le considerazioni seguenti per alcune di queste dimensioni:

*   Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswalle) e con tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,2 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

*   Le macchine virtuali serie G offrono la maggiore quantità di memoria e vengono eseguite su host con processori della famiglia Intel Xeon E5 V3.


*   Le macchine virtuali serie DS, DSv2 e GS possono usare l'archiviazione Premium, che offre un'archiviazione ad alte prestazioni e a bassa latenza, per carichi di lavoro con uso intensivo di I/O. Usano le unità SSD (Solid State Drive) per ospitare i dischi della macchina virtuale e offrono una cache del disco SSD locale. L'archiviazione Premium è disponibile solo in determinate aree geografiche. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).


*   Le VM di serie A possono essere distribuite su una vasta gamma di tipi di hardware e processori. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale.

*   La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.


La dimensione della macchina virtuale influisce sul prezzo. Influisce, inoltre, sull’elaborazione, sulla memoria e sulla capacità di archiviazione della macchina virtuale. I costi di archiviazione vengono calcolati separatamente in base alle pagine usate nell'account di archiviazione. Per ulteriori informazioni, vedere [Dettagli prezzi per le macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).


Le considerazioni seguenti potrebbero essere utili all’utente per scegliere una dimensione:


* Le dimensioni A8-A11 sono note anche come *istanze a elevato utilizzo di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. Per informazioni e considerazioni dettagliate sull'uso di queste dimensioni, vedere l'articolo relativo alle [informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


*	Le serie Dv2, D, G e DS/GS sono ideali per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale o requisiti di memoria superiori. Offrono una potente combinazione per molte applicazioni di livello aziendale.

*   Alcuni degli host fisici nei data center di Azure potrebbero non supportare macchine virtuali di dimensioni superiori, ad esempio da A5 ad A11. Di conseguenza, potrebbe essere visualizzato il messaggio di errore **Impossibile configurare la macchina virtuale <machine name>** o **Impossibile creare la macchina virtuale <machine name>** quando si configura una macchina virtuale esistente in base a una nuova dimensione, si crea una nuova macchina virtuale in una rete virtuale creata prima del 16 aprile 2013 o si aggiunge una nuova macchina a un servizio cloud esistente. Vedere [Errore: "Impossibile configurare la macchina virtuale"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) nel forum di supporto per le soluzioni alternative per ogni scenario di distribuzione.


## Considerazioni sulle prestazioni

È stato creato il concetto di unità di elaborazione di Azure (ACU, Azure Compute Unit) per mettere a punto un modo per confrontare le prestazioni di calcolo (CPU) degli SKU di Azure. In questo modo, sarà più semplice identificare lo SKU più adatto a soddisfare le proprie esigenze di prestazioni. L'unità ACU adotta come standard una macchina virtuale Small (Standard\_A1), a cui attribuisce il valore 100. Per tutte le altre SKU sarà quindi possibile valutare la maggiore velocità di elaborazione con cui sono in grado di eseguire un benchmark standard.

>[AZURE.IMPORTANT] L'unità ACU costituisce solo un'indicazione di riferimento. I risultati possono variare in base al carico di lavoro.

<br>

|Famiglia SKU |ACU/Core |
|---|---|
|[Standard\_A0](#standard-tier-a-series) |50 |
|[Standard\_A1-4](#standard-tier-a-series) |100 |
|[Standard\_A5-7](#standard-tier-a-series) |100 |
|[A8-A11](#standard-tier-a-series) |225 *|
|[D1-14](#standard-tier-d-series) |160 |
|[D1-15v2](#standard-tier-dv2-series) |210 - 250 *|
|[DS1-14](#standard-tier-ds-series) |160 |
|[DS1-15v2](#standard-tier-dsv2-series) |210-250* |
|[G1-5](#standard-tier-g-series) |180 - 240 *|
|[GS1-5](#standard-tier-gs-series) |180 - 240 *|


Le unità ACU contrassegnate con * usano la tecnologia Intel® Turbo per aumentare la frequenza della CPU e offrire un miglioramento delle prestazioni. L'entità dell'aumento di prestazioni può variare in base alle dimensioni della macchina virtuale, al carico di lavoro e agli altri carichi di lavoro in esecuzione sullo stesso host.

## Tabelle delle dimensioni

Le tabelle seguenti illustrano le dimensioni e le capacità offerte da ogni dimensione.

* La capacità di archiviazione è rappresentata usando 1024 ^ 3 byte come unità di misura per GB. Questa unità di misura è definita talvolta gibibyte o definizione di base 2. Quando si confrontano dimensioni che usano sistemi di base diversi, è necessario ricordare che le dimensioni di base 2 potrebbero risultare più piccole di quelli di base 10 ma per qualsiasi dimensione specifica (ad esempio, 1 GB) un sistema di base 2 fornisce maggiore capacità di un sistema di base 10, perché 1024^3 è superiore a 1000^3.

* La larghezza di banda della rete massima è la larghezza di banda aggregata massima allocata e assegnata a ogni tipo di VM. La larghezza di banda massima fornisce indicazioni per selezionare il tipo di VM corretto per garantire la disponibilità della capacità di rete adeguata. Quando si passa da bassa, moderata, alta e molto alta, la velocità effettiva aumenta di conseguenza. Le prestazioni di rete effettive dipenderanno da molti fattori, tra cui carichi di rete e dell’applicazione e le impostazioni di rete dell’applicazione.


## Livello Standard: serie A

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|
|Standard\_A0 |1|768 MB|1| Temporaneo = 20 GB |1|1x500| basso |
|Standard\_A1 |1|1,75 GB|1|Temporaneo = 70 GB |2|2x500| moderata |
|Standard\_A2 |2|3,5 GB|1|Temporaneo = 135 GB |4|4x500| moderata |
|Standard\_A3 |4|7 GB|2|Temporaneo = 285 GB |8|8x500| alto |
|Standard\_A4 |8|14 GB|4|Temporaneo = 605 GB |16|16x500| alto |
|Standard\_A5 |2|14 GB|1|Temporaneo = 135 GB |4|4X500| moderata |
|Standard\_A6 |4|28 GB|2|Temporaneo = 285 GB |8|8x500| alto |
|Standard\_A7 |8|56 GB|4|Temporaneo = 605 GB |16|16x500| alto |



## Livello standard: serie A - Istanze a elevato utilizzo di calcolo

Nota: per informazioni e considerazioni sull'uso di queste dimensioni, vedere l'articolo relativo alle [informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|
|Standard\_A8|8|56 GB|2| Temporaneo = 382 GB |16|16x500| alto |
|Standard\_A9|16|112 GB|4| Temporaneo = 382 GB |16|16x500| molto alta |
|Standard\_A10|8|56 GB|2| Temporaneo = 382 GB |16|16x500| alto |
|Standard\_A11|16|112 GB|4| Temporaneo = 382 GB |16|16x500| molto alta |

## Livello Standard: serie D

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|
|Standard\_D1 |1|3,5 GB|1|Temporaneo (SSD) = 50 GB |2|2x500| moderata |
|Standard\_D2 |2|7 GB|2|Temporaneo (SSD) = 100 GB |4|4x500| alto |
|Standard\_D3 |4|14 GB|4|Temporaneo (SSD) = 200 GB |8|8x500| alto |
|Standard\_D4 |8|28 GB|8|Temporaneo (SSD) = 400 GB |16|16x500| alto |
|Standard\_D11 |2|14 GB|2|Temporaneo (SSD) = 100 GB |4|4x500| alto |
|Standard\_D12 |4|28 GB|4|Temporaneo (SSD) = 200 GB |8|8x500| alto |
|Standard\_D13 |8|56 GB|8|Temporaneo (SSD) = 400 GB |16|16x500| alto |
|Standard\_D14 |16|112 GB|8|Temporaneo (SSD) = 800 GB |32|32x500| molto alta |


## Livello standard: serie Dv2

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3,5 GB|1|Temporaneo (SSD) = 50 GB |2|2x500| moderata |
|Standard\_D2\_v2 |2|7 GB|2|Temporaneo (SSD) = 100 GB |4|4x500| alto |
|Standard\_D3\_v2 |4|14 GB|4|Temporaneo (SSD) = 200 GB |8|8x500| alto |
|Standard\_D4\_v2 |8|28 GB|8|Temporaneo (SSD) = 400 GB |16|16x500| alto |
|Standard\_D5\_v2 |16|56 GB|8|Temporaneo (SSD) = 800 GB |32|32x500| molto alta |
|Standard\_D11\_v2 |2|14 GB|2|Temporaneo (SSD) = 100 GB |4|4x500| alto |
|Standard\_D12\_v2 |4|28 GB|4|Temporaneo (SSD) = 200 GB |8|8x500| alto |
|Standard\_D13\_v2 |8|56 GB|8|Temporaneo (SSD) = 400 GB |16|16x500| alto |
|Standard\_D14\_v2 |16|112 GB|8|Temporaneo (SSD) = 800 GB |32|32x500| molto alta |
|Standard\_D15\_v2 |20|140 GB|10|Temporaneo (SSD) = 1 TB |40|40x500| molto alta |


## Livello Standard: serie DS*

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Dimensione cache (GB)|IOPS max. disco e larghezza di banda| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3,5|1|Disco SSD locale = 7 GB |2|43| 3\.200 32 MB al secondo | moderata |
|Standard\_DS2 |2|7|2|Disco SSD locale = 14 GB |4|86| 6\.400 64 MB al secondo | alto |
|Standard\_DS3 |4|14|4|Disco SSD locale = 28 GB |8|172| 12\.800 128 MB al secondo | alto |
|Standard\_DS4 |8|28|8|Disco SSD locale = 56 GB |16|344| 25\.600 256 MB al secondo | alto |
|Standard\_DS11 |2|14|2|Disco SSD locale = 28 GB |4|72| 6\.400 64 MB al secondo | alto |
|Standard\_DS12 |4|28|4|Disco SSD locale = 56 GB |8|144| 12\.800 128 MB al secondo | alto |
|Standard\_DS13 |8|56|8|Disco SSD locale = 112 GB |16|288| 25\.600 256 MB al secondo | alto |
|Standard\_DS14 |16|112|8|Disco SSD locale = 224 GB |32|576| 50\.000 512 MB al secondo | molto alta |

**Il numero massimo di operazioni di input/output al secondo (IOPS) e la velocità effettiva (larghezza di banda) possibili con una macchina virtuale serie DS dipende dalla dimensione del disco. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).


## Livello standard: serie DSv2*

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Dimensione cache (GB)|IOPS max. disco e larghezza di banda| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1\_v2 |1|3,5|1|Disco SSD locale = 7 GB |2|43| 3\.200 48 MB al secondo | moderata |
|Standard\_DS2\_v2 |2|7|2|Disco SSD locale = 14 GB |4|86| 6\.400 96 MB al secondo | alto |
|Standard\_DS3\_v2 |4|14|4|Disco SSD locale = 28 GB |8|172| 12\.800 192 MB al secondo | alto |
|Standard\_DS4\_v2 |8|28|8|Disco SSD locale = 56 GB |16|344| 25\.600 384 MB al secondo | alto |
|Standard\_DS5\_v2 |16|56|8|Disco SSD locale = 112 GB |32|688| 50\.000 768 MB al secondo | alto |
|Standard\_DS11\_v2 |2|14|2|Disco SSD locale = 28 GB |4|72| 6\.400 96 MB al secondo | alto |
|Standard\_DS12\_v2 |4|28|4|Disco SSD locale = 56 GB |8|144| 12\.800 192 MB al secondo | alto |
|Standard\_DS13\_v2 |8|56|8|Disco SSD locale = 112 GB |16|288| 25\.600 384 MB al secondo | alto |
|Standard\_DS14\_v2 |16|112|8|Disco SSD locale = 224 GB |32|576| 50\.000 768 MB al secondo | molto alta |
|Standard\_DS15\_v2 |20|140 GB|10|Disco SSD locale = 280 GB |40| 720|62\.500 960 MB al secondo | molto alta |


**Il numero massimo di operazioni di input/output al secondo (IOPS) e la velocità effettiva (larghezza di banda) possibili con una macchina virtuale serie DS dipende dalla dimensione del disco. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).


## Livello Standard: serie G

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|Disco SSD locale = 384 GB |4|4x500| alto |
|Standard\_G2 |4|56 GB|2|Disco SSD locale = 768 GB |8|8x500| alto |
|Standard\_G3 |8|112 GB|4|Disco SSD locale = 1.536 GB |16|16x500| molto alta | 
|Standard\_G4 |16|224 GB|8|Disco SSD locale = 3.072 GB |32|32x500| estremamente alta |
|Standard\_G5 |32|448 GB|8|Disco SSD locale = 6.144 GB |64| 64 x 500 | estremamente alta |

## Livello Standard: serie GS

|Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Dimensione cache (GB)|IOPS max. disco e larghezza di banda| Larghezza di banda della rete max |
|---|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|Disco SSD locale = 56 GB |4|264| 5\.000 125 MB al secondo | alto |
|Standard\_GS2|4|56|2|Disco SSD locale = 112 GB |8|528| 10\.000 250 MB al secondo | alto | 
|Standard\_GS3|8|112|4|Disco SSD locale = 224 GB |16|1056| 20\.000 500 MB al secondo | molto alta |
|Standard\_GS4|16|224|8|Disco SSD locale = 448 GB |32|2112| 40\.000 1.000 MB al secondo | estremamente alta |
|Standard\_GS5|32|448|8|Disco SSD locale = 896 GB |64|4224| 80\.000 2.000 MB al secondo | estremamente alta |

## Note: Standard A0 - A4 che utilizza l’interfaccia della riga di comando e Powershell 


Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in Powershell e nell’interfaccia della riga di comando:

* Standard\_A0 è ExtraSmall 
* Standard\_A1 è Small
* Standard\_A2 è Medium
* Standard\_A3 è Large
* Standard\_A4 è ExtraLarge


## Passaggi successivi

- Informazioni su [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../articles/azure-subscription-service-limits.md).
- Altre informazioni sulle [istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) per carichi di lavoro come High-performance Computing (HPC).



