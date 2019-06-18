---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147904"
---
*Preparare la cache*  
Il disco con memorizzazione nella cache dell'host di tipo ReadOnly è in grado di ottenere valori di IOPS più elevati rispetto al limite del disco. Per ottenere queste prestazioni di lettura massime dalla cache dell'host, è prima di tutto necessario preparare la cache del disco. Ciò assicura che le operazioni di I/O di lettura che lo strumento di benchmarking eseguirà sul volume CacheReads raggiungano effettivamente la cache e non direttamente il disco. I riscontri nella cache producono IOPS aggiuntivi da un singolo disco abilitato per la cache.

> [!IMPORTANT]
> è necessario preparare la cache prima di eseguire il benchmarking, ogni volta che la VM viene riavviata.

## <a name="tools"></a>Strumenti

### <a name="iometer"></a>Iometer

[Scaricare lo strumento Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) nella VM.

#### <a name="test-file"></a>File di test

Iometer usa un file di test archiviato nel volume in cui eseguire il test di benchmarking. Gestisce le operazioni di lettura e scrittura sul file di test per misurare i valori di IOPS e velocità effettiva del disco. Iometer crea questo file di test se non ne è stato fornito uno. Creare un file di test di 200 GB denominato iobw.tst nei volumi CacheReads e NoCacheWrites.

#### <a name="access-specifications"></a>Specifiche di accesso

Le specifiche, la dimensione della richiesta I/O, la percentuale di letture/scritture e la percentuale di operazioni casuali/sequenziali vengono configurate tramite la scheda "Access Specifications" in Iometer. Creare una specifica di accesso per ogni scenario illustrato di seguito. Creare le specifiche di accesso, quindi salvarle con un nome appropriato, ad esempio RandomWrites\_8K, RandomReads\_8K. Selezionare la specifica corrispondente durante l'esecuzione dello scenario di test.

Un esempio di specifiche di accesso per uno scenario con valori massimi di IOPS di scrittura è riportato di seguito,  
    ![Esempio di specifiche di accesso per valori massimi di IOPS di scrittura](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Specifiche per il valore massimo di IOPS di test

Per illustrare il valore massimo di IOPS, usare una dimensione minore della richiesta. Usare una dimensione di richiesta pari a 8 K e creare specifiche per letture e scritture casuali.

| Specifica di accesso | Dimensione della richiesta | % di casuali | % di letture |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 K |100 |0 |
| RandomReads\_8K |8 K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Specifiche per il valore massimo di velocità effettiva di test

Per illustrare il valore massimo di velocità effettiva, usare una dimensione maggiore della richiesta. Usare una dimensione di richiesta pari a 64 K e creare specifiche per letture e scritture casuali.

| Specifica di accesso | Dimensione della richiesta | % di casuali | % di letture |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Esecuzione del test di Iometer

Seguire questa procedura per preparare la cache.

1. Creare le specifiche di accesso con i valori seguenti.

   | Name | Dimensione della richiesta | % di casuali | % di letture |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Eseguire il test di Iometer per l'inizializzazione del disco della cache con i parametri seguenti. Usare tre thread di lavoro per il volume di destinazione e una profondità della coda pari a 128. Impostare la durata relativa al tempo di esecuzione del test su 2 ore nella scheda "Test Setup".

   | Scenario | Volume di destinazione | Name | Duration |
   | --- | --- | --- | --- |
   | Inizializzare il disco della cache |CacheReads |RandomWrites\_1MB |2 ore |
1. Eseguire il test di Iometer per la preparazione del disco della cache con i parametri seguenti. Usare tre thread di lavoro per il volume di destinazione e una profondità della coda pari a 128. Impostare la durata relativa al tempo di esecuzione del test su 2 ore nella scheda "Test Setup".

   | Scenario | Volume di destinazione | Name | Durata |
   | --- | --- | --- | --- |
   | Preparare il disco della cache |CacheReads |RandomReads\_1MB |2 ore |

Dopo la preparazione del disco della cache, procedere con gli scenari di test elencati di seguito. Per eseguire il test di Iometer, usare almeno tre thread di lavoro per **ogni** volume di destinazione. Per ogni thread di lavoro selezionare il volume di destinazione, impostare la profondità della coda e selezionare una delle specifiche di test salvate, come illustrato nella tabella seguente, per eseguire lo scenario di test corrispondente. La tabella illustra anche i risultati previsti per IOPS e velocità effettiva quando si eseguono questi test. Per tutti gli scenari vengono usati una dimensione di I/O ridotta pari a 8 KB e un valore elevato per la profondità della coda pari a 128.

| Scenario di test | Volume di destinazione | NOME | Risultato |
| --- | --- | --- | --- |
| Max. IOPS di lettura |CacheReads |RandomWrites\_8K |50\.000 IOPS |
| Max. IOPS di scrittura |NoCacheWrites |RandomReads\_8K |64\.000 IOPS |
| Max. IOPS combinate |CacheReads |RandomWrites\_8K |100\.000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Max. letture MB/sec |CacheReads |RandomWrites\_64K |524 MB/sec |
| Max. scritture MB/sec |NoCacheWrites |RandomReads\_64K |524 MB/sec |
| MB/sec combinati |CacheReads |RandomWrites\_64K |1000 MB/sec |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Le schermate seguenti illustrano i risultati dei test di Iometer per scenari combinati di IOPS e velocità effettiva.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Valori massimi per IOPS di letture e scritture combinate

![Valori massimi per IOPS di letture e scritture combinate](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Velocità effettiva massima di letture e scritture combinate

![Velocità effettiva massima di letture e scritture combinate](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO è uno strumento popolare per il benchmarking dell'archiviazione sulle VM Linux. Offre la flessibilità necessaria per selezionare diverse dimensioni di I/O e letture e scritture sequenziali o casuali. Genera thread di lavoro o processi per l'esecuzione delle operazioni I/O specificate. È possibile specificare il tipo di operazioni I/O che ogni thread di lavoro deve eseguire usando i file processo. È stato creato un file processo per ogni scenario illustrato negli esempi seguenti. È possibile cambiare le specifiche di questi file processo per il benchmarking di diversi carichi di lavoro in esecuzione sull'Archiviazione Premium. Negli esempi viene usata una VM DS 14 Standard che esegue **Ubuntu**. Usare la stessa configurazione descritta all'inizio della sezione Benchmarking e preparare la cache prima di eseguire i test di benchmarking.

Prima di iniziare, [scaricare FIO](https://github.com/axboe/fio) e installarlo nella macchina virtuale.

Eseguire il comando seguente per Ubuntu:

```
apt-get install fio
```

Vengono usati quattro thread di lavoro per la gestione delle operazioni di scrittura e quattro thread di lavoro per la gestione delle operazioni di lettura sui dischi. I ruoli di lavoro di scrittura indirizzano il traffico verso il volume "nocache", che include 10 dischi con cache impostata su "None". I ruoli di lavoro di lettura indirizzano il traffico verso il volume "readcache", che include un disco con cache impostata su "ReadOnly".

#### <a name="maximum-write-iops"></a>IOPS massime di scrittura

Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS di scrittura. Assegnare al file il nome "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:  

* Profondità della coda elevata pari a 256.  
* Dimensione di blocco ridotta pari a 8 KB.  
* Più thread che eseguono scritture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:  

```
sudo fio --runtime 30 fiowrite.ini
```

Durante l'esecuzione del test, è possibile visualizzare il numero di operazioni IOPS di scrittura gestite dalla macchina virtuale e dai dischi Premium. Come illustrato nell'esempio seguente, la VM DS14 fornisce il limite massimo di IOPS di scrittura pari a 50.000 IOPS.  
    ![Numero di operazioni IOPS di scrittura gestite dalla macchina virtuale e dai dischi Premium](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>IOPS massime di lettura

Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS di lettura. Assegnare al file il nome "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:

* Profondità della coda elevata pari a 256.  
* Dimensione di blocco ridotta pari a 8 KB.  
* Più thread che eseguono scritture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:

```
sudo fio --runtime 30 fioread.ini
```

Durante l'esecuzione del test, è possibile visualizzare il numero di operazioni IOPS di lettura gestite dalla macchina virtuale e dai dischi Premium. Come illustrato nell'esempio seguente, la VM DS14 fornisce un valore superiore a 64.000 IOPS di lettura. Ciò dipende da una combinazione delle prestazioni del disco e della cache.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>IOPS massime di lettura e scrittura

Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS combinate di lettura e scrittura. Assegnare al file il nome "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:

* Profondità della coda elevata pari a 128.  
* Dimensione di blocco ridotta pari a 4 KB.  
* Più thread che eseguono scritture e letture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:

```
sudo fio --runtime 30 fioreadwrite.ini
```

Durante l'esecuzione del test, è possibile visualizzare il numero di operazioni IOPS combinate di lettura e scrittura gestite dalla macchina virtuale e dai dischi Premium. Come illustrato nell'esempio seguente, la VM DS14 fornisce un valore superiore a 100.000 IOPS combinate di lettura e scrittura. Ciò dipende da una combinazione delle prestazioni del disco e della cache.  
    ![IOPS combinate di lettura e scrittura](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Velocità effettiva massima combinata

Per ottenere la velocità effettiva massima combinata di lettura e scrittura, usare una dimensione di blocco superiore e una profondità della coda elevata con più thread che eseguono letture e scritture. È possibile usare una dimensione di blocco pari a 64 KB e una profondità della coda pari a 128.
