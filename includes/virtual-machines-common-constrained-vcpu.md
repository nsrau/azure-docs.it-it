

Alcuni carichi di lavoro di database, ad esempio SQL Server o Oracle, richiedono un utilizzo elevato di memoria, spazio di archiviazione e larghezza di banda di I/O, ma non un numero elevato di core. Molti carichi di lavoro di database non sono a elevato utilizzo di CPU. Azure offre alcune dimensioni di macchina virtuale in cui è possibile limitare il numero di vCPU per ridurre i costi relativi alle licenze software, mantenendo la stessa memoria, archiviazione e larghezza di banda di I/O.

Il numero di vCPU virtuali può essere vincolato a metà o un quarto delle dimensioni originali della macchina virtuale. Queste nuove dimensioni di macchina virtuale hanno un suffisso che specifica il numero di vCPU attive, semplificandone l'identificazione.

Ad esempio, le attuali dimensioni di VM Standard_GS5 prevedono 32 vCPU, 448 GB di RAM, 64 dischi (fino a 256 TB) e 80.000 operazioni di I/O al secondo o 2 GB/s di larghezza di banda di I/O. La nuove dimensioni di VM Standard_GS5-16 e Standard_GS5-8 prevedono rispettivamente 8 e 16 vCPU attive, con le stesse specifiche delle dimensioni Standard_GS5 in termini di memoria, archiviazione e larghezza di banda di I/O.

I costi di licenza addebitati per SQL Server o Oracle sono vincolati al nuovo numero di vCPU e gli addebiti di altri prodotti dovrebbero essere effettuati in base al nuovo numero di vCPU. Questo si traduce in un aumento del 50-75% nel rapporto tra specifiche della macchina virtuale e vCPU attive (fatturabili). Queste nuove dimensioni di macchina virtuale sono disponibili solo in Azure e questo consente ai carichi di lavoro di eseguire il push di un utilizzo più elevato della CPU a una frazione del costo di licenza (per core). Attualmente il costo di calcolo, che include il costo di licenza per il sistema operativo, è lo stesso delle dimensioni originali. Per altre informazioni, vedere [Azure VM sizes for more cost-effective database workloads](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/) (Nuove dimensioni per le macchine virtuali di Azure per carichi di lavoro di database più convenienti).


| NOME                | vCPU | Specifiche           |
|---------------------|------|-----------------|
| Standard_M64-32ms   | 32   | Come M64ms   |
| Standard_M64-16ms   | 16   | Come M64ms   |
| Standard_M128-64ms  | 64   | Come M128ms  |
| Standard_M128-32ms  | 32   | Come M128ms  |
| Standard_E4-2s_v3   | 2    | Come E4s_v3  |
| Standard_E8-4s_v3   | 4    | Come E8s_v3  |
| Standard_E8-2s_v3   | 2    | Come E8s_v3  |
| Standard_E16-8s_v3  | 8    | Come E16s_v3 |
| Standard_E16-4s_v3  | 4    | Come E16s_v3 |
| Standard_E32-16_v3  | 16   | Come E32s_v3 |
| Standard_E32-8s_v3  | 8    | Come E32s_v3 |
| Standard_E64-32s_v3 | 32   | Come E64s_v3 |
| Standard_E64-16s_v3 | 16   | Come E64s_v3 |
| Standard_GS4-8      | 8    | Come GS4     |
| Standard_GS4-4      | 4    | Come GS4     |
| Standard_GS5-16     | 16   | Come GS5     |
| Standard_GS5-8      | 8    | Come GS5     |
| Standard_DS11-1_v2  | 1    | Come DS11_v2 |
| Standard_DS12-2_v2  | 2    | Come DS12_v2 |
| Standard_DS12-1_v2  | 1    | Come DS12_v2 |
| Standard_DS13-4_v2  | 4    | Come DS13_v2 |
| Standard_DS13-2_v2  | 2    | Come DS13_v2 |
| Standard_DS14-8_v2  | 8    | Come DS14_v2 |
| Standard_DS14-4_v2  | 4    | Come DS14_v2 |
