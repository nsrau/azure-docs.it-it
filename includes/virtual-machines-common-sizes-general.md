
<!-- A-series, Av2-series, D-series, Dv2-series, DS-series*, DSv2-series* -->

Le VM di serie A e Av2 possono essere distribuite su una vasta gamma di tipi di hardware e processori. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale.

Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswell) e grazie alla tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

Le dimensioni del livello Basic sono destinate principalmente ai carichi di lavoro di sviluppo e alle altre applicazioni che non necessitano di bilanciamento del carico, scalabilità automatica o macchine virtuali con utilizzo intensivo della memoria. Per informazioni sulle dimensioni di macchine virtuali ottimali per applicazioni di produzione, vedere (Dimensioni per le macchine virtuali)[virtual-machines-size-specs.md] e per informazioni sui prezzi delle VM, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/).

> [!NOTE]
> Tutte le dimensioni di macchine virtuali di Azure supporteranno almeno due schede di interfaccia di rete a partire dal 15 aprile. Per informazioni sulle aree specifiche, controllare la disponibilità di un [aggiornamento del servizio](https://azure.microsoft.com/en-us/updates/). Questi aggiornamenti sono indicati di seguito con un carattere "^".

## <a name="dsv2-series"></a>Serie DSv2*

ACU: 210-250

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Max velocità effettiva del disco nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4.000/32 (43) |3.200/48 |2^ moderata |
| Standard_DS2_v2 |2 |7 |14 |4 |8.000/64 (86) |6.400/96 |2/alta |
| Standard_DS3_v2 |4 |14 |28 |8 |16.000/128 (172) |12.800/192 |4/alta |
| Standard_DS4_v2 |8 |28 |56 |16 |32.000/256 (344) |25.600/384 |8/alta |
| Standard_DS5_v2 |16 |56 |112 |32 |64.000/512 (688) |51.200/768 |8/estremamente alta |



## <a name="dv2-series"></a>Serie Dv2

ACU: 210-250

| Dimensione              | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Velocità effettiva massima del disco locale: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Larghezza di banda della rete/scheda NIC max |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2/2 x 500                         | 2^ / moderata                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4/4 x 500                         | 2/alta                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8/8 x 500                         | 4/alta                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16/16 x 500                       | 8/alta                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 32/32 x 500                       | 8/estremamente alta           |

<br>

## <a name="ds-series"></a>Serie DS*
| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva massima del disco locale e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4.000/32 (43) |3.200/32 |2^ / moderata |
| Standard_DS2 |2 |7 |14 |4 |8.000/64 (86) |6.400/64 |2/alta |
| Standard_DS3 |4 |14 |28 |8 |16.000/128 (172) |12.800/128 |4/alta |
| Standard_DS4 |8 |28 |56 |16 |32.000/256 (344) |25.600/256 |8/alta |

<br>
## <a name="d-series"></a>Serie D 

ACU: 160

| Dimensione         | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Velocità effettiva massima del disco locale: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Larghezza di banda della rete/scheda NIC max |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2/2 x 500                         | 2^ / moderata                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4/4 x 500                         | 2/alta                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8/8 x 500                         | 4/alta                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16/16 x 500                       | 8/alta                     |

<br>


## <a name="av2-series"></a>Serie Av2

ACU: 100

| Dimensione            | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Velocità effettiva massima del disco locale: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Larghezza di banda della rete/scheda NIC max |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2/2 x 500                         | 2^ / moderata                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4/4 x 500                         | 2/moderata                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8/8 x 500                         | 4/alta                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16/16 x 500                       | 8/alta                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4/4 x 500                         | 2/moderata                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8/8 x 500                         | 4/alta                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16/16 x 500                       | 8/alta                     |

<br>

## <a name="a-series"></a>Serie A

ACU: 50-100

| Dimensione | Core CPU | Memoria: GiB | Unità HDD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0* |1 |0,768 |20 |1 |1x500 |2^ / bassa |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |2^ / moderata |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2^ / moderata |
| Standard_A3 |4 |7 |285 |8 |8x500 |2/alta |
| Standard_A4 |8 |14 |605 |16 |16x500 |4/alta |
| Standard_A5 |2 |14 |135 |4 |4x500 |2^ / moderata |
| Standard_A6 |4 |28 |285 |8 |8x500 |2/alta |
| Standard_A7 |8 |56 |605 |16 |16x500 |4/alta |
<br>

*La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 che usa l'interfaccia della riga di comando e PowerShell
Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in PowerShell e nell'interfaccia della riga di comando:

* Standard_A0 è ExtraSmall 
* Standard_A1 è Small
* Standard_A2 è Medium
* Standard_A3 è Large
* Standard_A4 è ExtraLarge

## <a name="basic-a"></a>Basic A

|Dimensioni – Dimensioni\Nome |Core CPU|Memoria|NIC (Max)|Dimensioni massime per il disco temporaneo |Max. dischi dati da 1023 GB ciascuno|Max. IOPS (300 per disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2^| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2^| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2^| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2^| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2^| 240 GB |16|16x300|
