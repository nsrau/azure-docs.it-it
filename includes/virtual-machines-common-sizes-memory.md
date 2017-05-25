

* Le serie Dv2, D, G e DS/GS sono ideali per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale o requisiti di memoria superiori.  Offrono una potente combinazione per molte applicazioni di livello aziendale.

Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswell) e grazie alla tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.


## <a name="gs-series"></a>Serie GS*

ACU: 180 - 240

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva massima del disco locale e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10.000/100 (264) |5.000/125 |2/alta |
| Standard_GS2 |4 |56 |112 |8 |20.000/200 (528) |10.000/250 |2/alta |
| Standard_GS3 |8 |112 |224 |16 |40.000/400 (1.056) |20.000/500 |4/molto alta |
| Standard_GS4 |16 |224 |448 |32 |80.000/800 (2,112) |40.000/1.000 |8/estremamente alta |
| Standard_GS5** |32 |448 |896 |64 |160.000/1.600 (4.224) |80.000/2.000 |8/estremamente alta |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie GS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md). 

**L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>

## <a name="g-series"></a>Serie G

ACU: 180 - 240

| Dimensione         | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Velocità effettiva massima del disco locale: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Larghezza di banda della rete/scheda NIC max |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4/4 x 500                       | 2/alta                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8/8 x 500                       | 2/alta                     |
| Standard_G3  | 8         | 112         | 1.536          | 24000 / 375 / 187                                        | 16/16 x 500                     | 4/molto alta                |
| Standard_G4  | 16        | 224         | 3.072          | 48000 / 750 / 375                                        | 32/32 x 500                     | 8/estremamente alta           |
| Standard_G5* | 32        | 448         | 6.144          | 96000 / 1500 / 750                                       | 64/64 x 500                     | 8/estremamente alta           |

*L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>


## <a name="dsv2-series"></a>Serie DSv2*

ACU: 210 - 250

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva massima del disco locale e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 |2 |14 |28 |4 |8.000/64 (72) |6.400/96 |2/alta |
| Standard_DS12_v2 |4 |28 |56 |8 |16.000/128 (144) |12.800/192 |4/alta |
| Standard_DS13_v2 |8 |56 |112 |16 |32.000/256 (288) |25.600/384 |8/alta |
| Standard_DS14_v2 |16 |112 |224 |32 |64.000/512 (576) |51.200/768 |8/estremamente alta |
| Standard_DS15_v2*** |20 |140 |280 |40 |80.000/640 (720) |64.000/960 |8 estremamente alta* |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS v2 può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

* In alcune aree, per le dimensioni Standard_DS15_v2 è disponibile la rete accelerata. Per altre informazioni sull'uso e la disponibilità, vedere [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (Rete accelerata disponibile in anteprima) e [Rete accelerata per macchine virtuali](../articles/virtual-network/virtual-network-create-vm-accelerated-networking.md).

***L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>
Bps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

## <a name="dv2-series"></a>Serie Dv2

ACU: 210 - 250

| Dimensione              | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Velocità effettiva massima del disco locale: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Larghezza di banda della rete/scheda NIC max |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4/4 x 500                         | 2/alta                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8/8 x 500                         | 4/alta                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16/16 x 500                       | 8/alta                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32/32 x 500                       | 8/estremamente alta           |
| Standard_D15_v2** | 20        | 140         | 1.000          | 60000 / 937 / 468                                        | 40/40 x 500                       | 8/estremamente alta*          |

* In alcune aree, per le dimensioni Standard_D15_v2 è disponibile la rete accelerata. Per altre informazioni sull'uso e la disponibilità, vedere [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (Rete accelerata disponibile in anteprima) e [Rete accelerata per macchine virtuali](../articles/virtual-network/virtual-network-create-vm-accelerated-networking.md).

**L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

<br>

## <a name="ds-series"></a>Serie DS*

ACU: 160

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva massima del disco locale e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |4 |8.000/64 (72) |6.400/64 |2/alta |
| Standard_DS12 |4 |28 |56 |8 |16.000/128 (144) |12.800/128 |4/alta |
| Standard_DS13 |8 |56 |112 |16 |32.000/256 (288) |25.600/256 |8/alta |
| Standard_DS14 |16 |112 |224 |32 |64.000/512 (576) |51.200/512 |8/molto alta |


MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

*La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

## <a name="d-series"></a>Serie D

ACU: 160

| Dimensione         | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Velocità effettiva massima del disco locale: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Larghezza di banda della rete/scheda NIC max |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4/4 x 500                         | 2/alta                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8/8 x 500                         | 4/alta                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16/16 x 500                       | 8/alta                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32/32 x 500                       | 8/molto alta                |
<br>


<br>

