**Dischi di macchina virtuale: per i limiti dell'account**

| Risorsa | Limite predefinito |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per account (ingresso + uscita<sup>1</sup>) |<=50 Gbps |

<sup>1</sup>Si intendono in *ingresso* tutti i dati (richieste) inviati a un account di archiviazione. Si intendono in *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.

**Dischi di macchina virtuale: per i limiti di disco**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |1024 GiB (1 TB) |
| Operazioni IOPS al secondo max per disco |500 |2300 |5000 |
| Velocità effettiva massima per disco |100 MB al secondo |150 MB al secondo |200 MB al secondo |
| Numero massimo di dischi per account di archiviazione |280 |70 |35 |

**Dischi macchina virtuale: per i limiti VM**

| Risorsa | Limite predefinito |
| --- | --- |
| Max operazioni IOPS per VM |80\.000 IOPS con VM GS5<sup>1</sup> |
| Max velocità effettiva per VM |2\.000 MB/s con VM GS5<sup>1</sup> |

<sup>1</sup>Consultare [Dimensioni VM](../articles/virtual-machines/virtual-machines-linux-sizes.md) per conoscere i limiti delle VM di altre dimensioni.

<!---HONumber=AcomDC_0615_2016-->