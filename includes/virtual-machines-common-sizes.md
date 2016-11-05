


In Azure sono disponibili più dimensioni standard. Si tengano presenti le considerazioni seguenti per alcune di queste dimensioni:

* Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswell) e grazie alla tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.
* La serie F è basata sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz che può raggiungere velocità di clock fino a 3,1 GHz con la tecnologia Intel Turbo Boost 2.0. Si tratta delle stesse prestazioni CPU della serie Dv2 di VM.  Con un prezzo di listino orario più basso, la serie F presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di elaborazione di Azure (ACU, Azure Compute Unit) per ogni core. 
  
    La serie F introduce inoltre un nuovo standard di denominazione delle dimensioni delle VM per Azure. Per questa serie e per le dimensioni delle VM rese disponibili in futuro, il valore numerico dopo la lettera indicante il nome di famiglia corrisponderà al numero di core della CPU. Funzionalità aggiuntive, ad esempio ottimizzate per l'archiviazione Premium, verranno designate da lettere dopo il numero di core della CPU. Questo formato di denominazione verrà usato per le dimensioni di VM future ma non cambierà in modo retroattivo i nomi delle dimensioni delle VM esistenti che sono state rilasciate.
* Le macchine virtuali serie G offrono la maggiore quantità di memoria e vengono eseguite su host con processori della famiglia Intel Xeon E5 V3.
* Le VM serie DS, DSv2, Fs e GS possono utilizzare l'archiviazione Premium, che offre alte prestazioni e bassa latenza per carichi di lavoro con uso intensivo di I/O. Usano le unità SSD (Solid State Drive) per ospitare i dischi della macchina virtuale e offrono una cache del disco SSD locale. L'archiviazione Premium è disponibile solo in determinate aree geografiche. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).
* Le VM di serie A possono essere distribuite su una vasta gamma di tipi di hardware e processori. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale.
* La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.

La dimensione della macchina virtuale influisce sul prezzo. Influisce, inoltre, sull’elaborazione, sulla memoria e sulla capacità di archiviazione della macchina virtuale. I costi di archiviazione vengono calcolati separatamente in base alle pagine usate nell'account di archiviazione. Per altre informazioni, vedere [Dettagli prezzi per le macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/). 

Le considerazioni seguenti potrebbero essere utili all’utente per scegliere una dimensione:

* Le dimensioni delle serie A8-A11 e H sono note anche come *istanze a elevato uso di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. La serie A8-A11 usa Intel Xeon E5-2670 @ 2,6 GHZ, mentre la serie H usa Intel Xeon E5-2667 v3 @ 3,2 GHz. Per informazioni e considerazioni dettagliate sull'uso di queste dimensioni, vedere [Informazioni sulle VM serie H e serie A a elevato uso di calcolo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 
* Le serie Dv2, D, G e DS/GS sono ideali per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale o requisiti di memoria superiori.  Offrono una potente combinazione per molte applicazioni di livello aziendale.
* Le VM serie F sono un'ottima scelta per i carichi di lavoro che richiedono CPU più veloci ma che non necessitano della stessa memoria o unità SSD locale per core di CPU.  Carichi di lavoro come server Web, analisi, giochi ed elaborazione batch trarranno vantaggio dal valore della serie F.
* Alcuni degli host fisici nei data center di Azure potrebbero non supportare macchine virtuali di dimensioni superiori, ad esempio da A5 ad A11. Di conseguenza, potrebbe essere visualizzato il messaggio di errore **Impossibile configurare la macchina virtuale <machine name>** o **Impossibile creare la macchina virtuale <machine name>** quando si configura una macchina virtuale esistente in base a una nuova dimensione, si crea una nuova macchina virtuale in una rete virtuale creata prima del 16 aprile 2013 o si aggiunge una nuova macchina a un servizio cloud esistente. Vedere [Errore: "Impossibile configurare la macchina virtuale"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) nel forum di supporto per le soluzioni alternative per ogni scenario di distribuzione.  
* La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni . Per aumentare la quota, contattare il supporto tecnico di Azure.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni
È stato creato il concetto di unità di elaborazione di Azure (ACU, Azure Compute Unit) per mettere a punto un modo per confrontare le prestazioni di calcolo (CPU) degli SKU di Azure. In questo modo, sarà più semplice identificare lo SKU più adatto a soddisfare le proprie esigenze di prestazioni.  L'unità ACU adotta come standard una macchina virtuale Small (Standard_A1), a cui attribuisce il valore 100. Per tutte le altre SKU sarà quindi possibile valutare la maggiore velocità di elaborazione con cui sono in grado di eseguire un benchmark standard. 

> [!IMPORTANT]
> L'unità ACU costituisce solo un'indicazione di riferimento.  I risultati possono variare in base al carico di lavoro. 
> 
> 

<br>

| Famiglia SKU | ACU/Core |
| --- | --- |
| [Standard_A0](#a-series) |50 |
| [Standard_A1-4](#a-series) |100 |
| [Standard_A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [DS1-14](#ds-series) |160 |
| [DS1-15v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1-5](#g-series) |180 - 240* |
| [GS1-5](#gs-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

Le unità ACU contrassegnate con * usano la tecnologia Intel® Turbo per aumentare la frequenza della CPU e offrire un miglioramento delle prestazioni.  L'entità dell'aumento di prestazioni può variare in base alle dimensioni della macchina virtuale, al carico di lavoro e agli altri carichi di lavoro in esecuzione sullo stesso host.

## <a name="size-tables"></a>Tabelle delle dimensioni
Le tabelle seguenti illustrano le dimensioni e le capacità offerte da ogni dimensione.

* La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano dischi misurati in GB (1.000^3 byte) con dischi misurati in GiB (1.024^3), tenere presente che i valori di capacità specificati in GiB potrebbero apparire inferiori. Ad esempio, 1.023 GiB = 1.098,4 GB
* La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
* I dischi dati possono operare in modalità memorizzata nella cache o non memorizzata nella cache.  Per il funzionamento dei dischi dati memorizzati nella cache, la modalità di cache host è impostata su **ReadOnly** o su **ReadWrite**.  Per il funzionamento dei dischi dati non memorizzati nella cache, la modalità di cache host è impostata su **None**.
* La larghezza di banda della rete massima è la larghezza di banda aggregata massima allocata e assegnata a ogni tipo di VM. La larghezza di banda massima fornisce indicazioni per selezionare il tipo di VM corretto per garantire la disponibilità della capacità di rete adeguata. Quando si passa da bassa, moderata, alta e molto alta, la velocità effettiva aumenta di conseguenza. Le prestazioni di rete effettive dipenderanno da molti fattori, tra cui carichi di rete e dell’applicazione e le impostazioni di rete dell’applicazione.

## <a name="a-series"></a>Serie A
| Dimensione | Core CPU | Memoria: GiB | Unità HDD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0,768 |20 |1 |1x500 |1/bassa |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |1/moderata |
| Standard_A2 |2 |3,5 GB |135 |4 |4x500 |1/moderata |
| Standard_A3 |4 |7 |285 |8 |8x500 |2/alta |
| Standard_A4 |8 |14 |605 |16 |16x500 |4/alta |
| Standard_A5 |2 |14 |135 |4 |4x500 |1/moderata |
| Standard_A6 |4 |28 |285 |8 |8x500 |2/alta |
| Standard_A7 |8 |56 |605 |16 |16x500 |4/alta |

<br>

## <a name="a-series---compute-intensive-instances"></a>Serie A - Istanze a elevato utilizzo di calcolo
Per informazioni e considerazioni sull'uso di queste dimensioni, vedere [Informazioni sulle VM serie H e serie A a elevato uso di calcolo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).

| Dimensione | Core CPU | Memoria: GiB | Unità HDD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2/alta |
| Standard_A9* |16 |112 |382 |16 |16x500 |4/molto alta |
| Standard_A10 |8 |56 |382 |16 |16x500 |2/alta |
| Standard_A11 |16 |112 |382 |16 |16x500 |4/molto alta |

*Con supporto di RDMA

<br>

## <a name="d-series"></a>Serie D
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 |1 |3,5 |50 |2 |2x500 |1/moderata |
| Standard_D2 |2 |7 |100 |4 |4x500 |2/alta |
| Standard_D3 |4 |14 |200 |8 |8x500 |4/alta |
| Standard_D4 |8 |28 |400 |16 |16x500 |8/alta |
| Standard_D11 |2 |14 |100 |4 |4x500 |2/alta |
| Standard_D12 |4 |28 |200 |8 |8x500 |4/alta |
| Standard_D13 |8 |56 |400 |16 |16x500 |8/alta |
| Standard_D14 |16 |112 |800 |32 |32x500 |8/molto alta |

<br>

## <a name="dv2-series"></a>Serie Dv2
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3,5 |50 |2 |2x500 |1/moderata |
| Standard_D2_v2 |2 |7 |100 |4 |4x500 |2/alta |
| Standard_D3_v2 |4 |14 |200 |8 |8x500 |4/alta |
| Standard_D4_v2 |8 |28 |400 |16 |16x500 |8/alta |
| Standard_D5_v2 |16 |56 |800 |32 |32x500 |8/estremamente alta |
| Standard_D11_v2 |2 |14 |100 |4 |4x500 |2/alta |
| Standard_D12_v2 |4 |28 |200 |8 |8x500 |4/alta |
| Standard_D13_v2 |8 |56 |400 |16 |16x500 |8/alta |
| Standard_D14_v2 |16 |112 |800 |32 |32x500 |8/estremamente alta |
| Standard_D15_v2 |20 |140 |1.000 |40 |40x500 |8/estremamente alta |

<br>

## <a name="ds-series*"></a>Serie DS*
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Max velocità effettiva del disco nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4.000/32 (43) |3.200/32 |1/moderata |
| Standard_DS2 |2 |7 |14 |4 |8.000/64 (86) |6.400/64 |2/alta |
| Standard_DS3 |4 |14 |28 |8 |16.000/128 (172) |12.800/128 |4/alta |
| Standard_DS4 |8 |28 |56 |16 |32.000/256 (344) |25.600/256 |8/alta |
| Standard_DS11 |2 |14 |28 |4 |8.000/64 (72) |6.400/64 |2/alta |
| Standard_DS12 |4 |28 |56 |8 |16.000/128 (144) |12.800/128 |4/alta |
| Standard_DS13 |8 |56 |112 |16 |32.000/256 (288) |25.600/256 |8/alta |
| Standard_DS14 |16 |112 |224 |32 |64.000/512 (576) |51.200/512 |8/molto alta |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="dsv2-series*"></a>Serie DSv2*
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Max velocità effettiva del disco nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4.000/32 (43) |3.200/48 |1/moderata |
| Standard_DS2_v2 |2 |7 |14 |4 |8.000/64 (86) |6.400/96 |2/alta |
| Standard_DS3_v2 |4 |14 |28 |8 |16.000/128 (172) |12.800/192 |4/alta |
| Standard_DS4_v2 |8 |28 |56 |16 |32.000/256 (344) |25.600/384 |8/alta |
| Standard_DS5_v2 |16 |56 |112 |32 |64.000/512 (688) |51.200/768 |8/estremamente alta |
| Standard_DS11_v2 |2 |14 |28 |4 |8.000/64 (72) |6.400/96 |2/alta |
| Standard_DS12_v2 |4 |28 |56 |8 |16.000/128 (144) |12.800/192 |4/alta |
| Standard_DS13_v2 |8 |56 |112 |16 |32.000/256 (288) |25.600/384 |8/alta |
| Standard_DS14_v2 |16 |112 |224 |32 |64.000/512 (576) |51.200/768 |8/estremamente alta |
| Standard_DS15_v2 |20 |140 GB |280 |40 |80.000/640 (720) |64.000/960 |8/estremamente alta |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS v2 può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="f-series"></a>Serie F
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |2 |16 |2 |2x500 |1/moderata |
| Standard_F2 |2 |4 |32 |4 |4x500 |2/alta |
| Standard_F4 |4 |8 |64 |8 |8x500 |4/alta |
| Standard_F8 |8 |16 |128 |16 |16x500 |8/alta |
| Standard_F16 |16 |32 |256 |32 |32x500 |8/estremamente alta |

<br>

## <a name="fs-series*"></a>Serie Fs*
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Max velocità effettiva del disco nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4.000/32 (12) |3.200/48 |1/moderata |
| Standard_F2s |2 |4 |8 |4 |8.000/64 (24) |6.400/96 |2/alta |
| Standard_F4s |4 |8 |16 |8 |16.000/128 (48) |12.800/192 |4/alta |
| Standard_F8s |8 |16 |32 |16 |32.000/256 (96) |25.600/384 |8/alta |
| Standard_F16s |16 |32 |64 |32 |64.000/512 (192) |51.200/768 |8/estremamente alta |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie Fs può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="g-series"></a>Serie G
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4x500 |1/alta |
| Standard_G2 |4 |56 |768 |8 |8x500 |2/alta |
| Standard_G3 |8 |112 |1.536 |16 |16x500 |4/molto alta |
| Standard_G4 |16 |224 |3.072 |32 |32x500 |8/estremamente alta |
| Standard_G5 |32 |448 |6.144 |64 |64 x 500 |8/estremamente alta |

<br>

## <a name="gs-series*"></a>Serie GS*
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Max velocità effettiva del disco nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10.000/100 (264) |5.000/125 |1/alta |
| Standard_GS2 |4 |56 |528 |8 |20.000/200 (528) |10.000/250 |2/alta |
| Standard_GS3 |8 |112 |1.056 |16 |40.000/400 (1.056) |20.000/500 |4/molto alta |
| Standard_GS4 |16 |224 |2.112 |32 |80.000/800 (2,112) |40.000/1.000 |8/estremamente alta |
| Standard_GS5 |32 |448 |4.224 |64 |160.000/1.600 (4.224) |80.000/2.000 |8/estremamente alta |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie GS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. 

<br>

## <a name="h-series"></a>Serie H
Le macchine virtuali serie H di Azure sono le VM high performance computing di prossima generazione che puntano a risolvere esigenze di calcolo di fascia alta, come modellazione molecolare e fluidodinamica computazionale. Queste VM a 8 e 16 core sono basate sulla tecnologia del processore Intel Haswell E5-2667 V3 con memoria DDR4 e archiviazione basata su SSD locale. 

Oltre alla sostanziale potenza della CPU, la serie H offre diverse opzioni per rete RDMA a bassa latenza con FDR InfiniBand e diverse configurazioni di memoria a supporto di requisiti di calcolo a elevato uso di memoria.

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16x500 |8/alta |
| Standard_H16 |16 |112 |2000 |32 |32x500 |8/molto alta |
| Standard_H8m |8 |112 |1000 |16 |16x500 |8/alta |
| Standard_H16m |16 |224 |2000 |32 |32x500 |8/molto alta |
| Standard_H16r* |16 |112 |2000 |32 |32x500 |8/molto alta |
| Standard_H16mr* |16 |224 |2000 |32 |32x500 |8/molto alta |

*Con supporto di RDMA

<br>

## <a name="n-series-(preview)"></a>Serie N (anteprima)
Le dimensioni NC e NV sono dette anche istanze abilitate tramite GPU. Si tratta di macchine virtuali specializzate dotate di schede GPU NVIDIA e ottimizzate per diversi scenari e casi d'uso. Le dimensioni NV sono ottimizzate e progettate per le operazioni di visualizzazione remota, streaming, giochi, codifica e scenari VDI che utilizzano framework come OpenGL e DirectX. Le dimensioni NC sono più adatte per applicazioni a elevato utilizzo di calcolo e reti, nonché algoritmi, tra cui applicazioni e simulazioni basate su CUDA e OpenCL. 

### <a name="nv-instances"></a>Istanze NV
Le istanze NV sono basate su GPU NVIDIA Tesla M60 e NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti potranno visualizzare i propri dati o le proprie simulazioni. Gli utenti potranno visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore e inoltre eseguire singoli carichi di lavoro di precisione, come la codifica e il rendering. Il modello Tesla M60 ospita 4.096 core CUDA in una dual GPU con un massimo di 36 flussi H264 da 1080p.

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 |1 x NVIDIA M60 |
| Standard_NV12 |12 |112 |680 |2 x NVIDIA M60 |
| Standard_NV24 |24 |224 |1.440 |4 x NVIDIA M60 |

### <a name="nc-instances"></a>Istanze NC
Le istanze NC sono basate su GPU NVIDIA Tesla K80. Gli utenti possono ora elaborare i dati molto più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, analisi dettagliate e altro ancora. Il modello Tesla K80 ospita 4.992 core CUDA in una dual GPU con prestazioni che raggiungono 2,91 teraflop di precisione doppia e 8,93 teraflop di precisione singola. 

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 |380 |1 x NVIDIA K80 |
| Standard_NC12 |12 |112 |680 |2 x NVIDIA K80 |
| Standard_NC24 |24 |224 |1.440 |4 x NVIDIA K80 |

<br>

## <a name="notes:-standard-a0---a4-using-cli-and-powershell"></a>Note: Standard A0 - A4 che utilizza l’interfaccia della riga di comando e PowerShell
Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in PowerShell e nell’interfaccia della riga di comando:

* Standard_A0 è ExtraSmall 
* Standard_A1 è Small
* Standard_A2 è Medium
* Standard_A3 è Large
* Standard_A4 è ExtraLarge

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../articles/azure-subscription-service-limits.md).
* Per altre informazioni, vedere [Informazioni sulle VM serie H e serie A a elevato uso di calcolo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) per carichi di lavoro come High-Performance Computing (HPC).

<!--HONumber=Oct16_HO2-->


