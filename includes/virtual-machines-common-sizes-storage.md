
La serie LS è ottimizzata per carichi di lavoro che richiedono l'archiviazione locale a bassa latenza, come i database NoSQL, ad esempio Cassandra, MongoDB, Cloudera e Redis. La serie LS offre fino a 32 core di CPU e usa il [processore Intel® Xeon® della famiglia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Si tratta delle stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni core della CPU.  

## <a name="ls-series"></a>Serie Ls

ACU: 180-240
 
| Dimensione          | Core CPU | Memoria: GiB | Unità SSD locale: GiB | Valore massimo per dischi di dati | Velocità effettiva massima del disco locale e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Larghezza di banda della rete/scheda NIC max | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | ND/ND (0)          | 5.000/125                               | 2/alta       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | ND/ND (0)          | 10.000/250                              | 4/molto alta  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | ND/ND (0)          | 20.000/500                              | 8/estremamente alta | 
| Standard_L32s** | 32 | 256  | 5,630 | 64             | ND/ND (0)          | 40.000/1.000                            | 8/estremamente alta | 
 
MBps = 10^6 byte al secondo e GiB = 1024^3 byte. 

*La massima velocità effettiva del disco (IOPS o Mbps) possibile con una VM serie LS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md). 

**L'istanza è isolata e prevede hardware dedicato per un singolo cliente.