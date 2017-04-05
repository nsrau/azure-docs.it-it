**Dischi di macchina virtuale non gestiti Premium: limiti per account**

| Risorsa | Limite predefinito |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per account (ingresso + uscita<sup>1</sup>) |<=50 Gbps |

<sup>1</sup>Si intendono in *ingresso* tutti i dati (richieste) inviati a un account di archiviazione. *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.

**Dischi di macchina virtuale non gestiti Premium: limiti per disco**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |1024 GiB (1 TB) |
| Operazioni IOPS al secondo max per disco |500 |2300 |5000 |
| Velocità effettiva massima per disco |100 MB/s | 150 MB/s |200 MB/s |
| Numero massimo di dischi per account di archiviazione |280 |70 |35 |

**Dischi di macchina virtuale non gestiti Premium: limiti per macchina virtuale**

| Risorsa | Limite predefinito |
| --- | --- |
| Max operazioni IOPS per VM |80.000 IOPS con VM GS5<sup>1</sup> |
| Max velocità effettiva per VM |2000 MB/s con VM GS5<sup>1</sup> |

<sup>1</sup>Consultare [Dimensioni VM](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per conoscere i limiti delle VM di altre dimensioni. 

