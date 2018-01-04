
GPU con ottimizzazione per la macchina virtuale le dimensioni sono specializzate macchine virtuali disponibili con uno o più GPU NVIDIA. Queste dimensioni sono progettate per i carichi di lavoro con utilizzo intensivo di calcolo con utilizzo intensivo di grafica e visualizzazione. Questo articolo fornisce informazioni relative a numero e tipo di GPU, Vcpu, i dischi dati e schede di rete, nonché archiviazione velocità effettiva e la rete della larghezza di banda per ogni dimensione di questo raggruppamento. 

* **NC NCv2 e ND** dimensioni sono ottimizzate per applicazioni complesse e risorse di rete e algoritmi, tra cui applicazioni basate su CUDA e OpenCL e simulazioni, AI e formazione. 
* **NV** dimensioni sono ottimizzate e progettate per la visualizzazione remota, streaming, giochi, codifica e scenari VDI usando Framework, ad esempio OpenGL e DirectX.  


## <a name="nc-instances"></a>Istanze NC

Le istanze di NC vengono compilate in base il [NVIDIA Tesla R80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) scheda. Gli utenti possono vengono frammentati tra i dati più veloce sfruttando CUDA per le applicazioni di esplorazione di energia, simulazioni di arresto anomalo, raggio inseriti nella traccia per il rendering, formazione e altro ancora. La configurazione di NC24r fornisce una bassa latenza e l'interfaccia di rete con velocità effettiva elevata ottimizzato per carichi di lavoro di calcolo parallelo fortemente accoppiata.


| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1.440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1.440 | 4 | 64 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA

## <a name="ncv2-instances"></a>Istanze di NCv2

Le istanze di NCv2 sono la prossima generazione delle macchine NC-series, con tecnologia [P100 Tesla NVIDIA](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU. Queste GPU possono fornire più di 2 volte le prestazioni di calcolo della serie di contesto dei nomi corrente. I clienti possono ora usufruire di queste GPU aggiornate per carichi di lavoro HPC tradizionali, ad esempio modellazione di serbatoi per, DNA sequenziazione, analisi proteine, le simulazioni Monte Carlo e altri. Come serie di contesto dei nomi, la serie di NCv2 offre una configurazione con una bassa latenza e l'interfaccia di rete con velocità effettiva elevata ottimizzato per carichi di lavoro di calcolo parallelo fortemente accoppiata.

> [!IMPORTANT]
> Per questo gruppo di dimensioni, la quota di CPU virtuali (core) nella sottoscrizione impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di CPU virtuali](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa serie in un [area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24r_v2 * |24 |1448 | 1344 | 4 | 32 |

1 GPU = una scheda P100.

*Con supporto di RDMA

## <a name="nd-instances"></a>Istanze ND

Le macchine virtuali serie ND sono una novità per la famiglia di GPU progettata per carichi di lavoro AI e formazione. Offrono prestazioni ottimali per il training e l'inferenza. Le istanze ND sono basate sulla [P40 Tesla NVIDIA](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU. Queste istanze forniscono prestazioni eccellenti per e con precisione singola operazioni a virgola mobile, per carichi di lavoro AI che utilizzano Microsoft cognitivi Toolkit, TensorFlow, Caffe e altri Framework. La serie ND offre anche dimensioni di memoria di GPU significativamente superiori (24 GB), ottimali per modelli di rete neurale molto più grandi. Ad esempio NC-series, la serie di ND offre una configurazione con una rete a bassa latenza, velocità effettiva elevata secondaria tramite RDMA e la connettività InfiniBand per poter eseguire i processi di formazione su larga scala che si estende su molte GPU.

> [!IMPORTANT]
> Per questo gruppo di dimensioni, la quota di CPU virtuali (core) per ogni area nella sottoscrizione impostata inizialmente su 0. [Richiedere un aumento della quota di CPU virtuali](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa serie in un [area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6 |6 |112 | 336 | 1 | 12 |
| Standard_ND12 |12 |224 | 672 | 2 | 24 |
| Standard_ND24 |24 |448 | 1344 | 4 | 32 |
| Standard_ND24r * |24 |1448 | 1344 | 4 | 32 |

1 GPU = una scheda P40.

*Con supporto di RDMA

## <a name="nv-instances"></a>Istanze NV



Le istanze di NV sono basate sulla [M60 Tesla NVIDIA ](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU e NVIDIA griglia tecnologia per desktop accelerated applicazioni e dei desktop virtuali in cui i clienti sono in grado di visualizzare i dati o simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. 

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1.440 | 4 | 64 |

1 GPU = Pari a metà scheda M60.


