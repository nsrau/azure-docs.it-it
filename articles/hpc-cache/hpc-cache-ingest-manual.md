---
title: Inserimento dati anteprima cache HPC di Azure-copia manuale
description: Come usare i comandi CP per spostare i dati in una destinazione di archiviazione BLOB nella cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: e1ca6fa4ea1ae4a5bf5996e88d32e1e00416f067
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299980"
---
# <a name="azure-hpc-cache-preview-data-ingest---manual-copy-method"></a>Inserimento di dati nella cache HPC di Azure (anteprima)-metodo di copia manuale

Questo articolo fornisce istruzioni dettagliate per la copia manuale dei dati in un contenitore di archiviazione BLOB per l'uso con la cache HPC di Azure. USA operazioni parallele multithread per ottimizzare la velocità di copia.

Per altre informazioni sullo spostamento dei dati nell'archiviazione BLOB per la cache HPC di Azure, vedere [spostare i dati nell'archivio BLOB di Azure per la cache HPC](hpc-cache-ingest.md)di Azure.

## <a name="simple-copy-example"></a>Esempio di copia semplice

Si può creare manualmente una copia a thread multipli in un client eseguendo simultaneamente più comandi di copia in background su un set predefinito di file o percorsi.

Il comando Linux/UNIX ``cp`` include l'argomento ``-p`` per conservare i metadati mtime e di proprietà. L'aggiunta di questo argomento ai comandi riportati di seguito è facoltativa. L'aggiunta dell'argomento aumenta il numero di chiamate file system inviate dal client al file system di destinazione per la modifica dei metadati.

Questo semplice esempio copia due file in parallelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Dopo aver eseguito questo comando, il comando `jobs` mostrerà che sono in esecuzione due thread.

## <a name="copy-data-with-predictable-file-names"></a>Copiare dati con nomi di file stimabili

Se i nomi di file sono stimabili, è possibile usare espressioni per creare thread di copia paralleli. 

Ad esempio, se la directory contiene 1.000 file numerati in sequenza da `0001` a `1000`, è possibile usare le espressioni seguenti per creare dieci thread paralleli, ognuno dei quali copia 100 file:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Copiare dati con nomi di file non strutturati

Se la struttura di denominazione dei file non è stimabile, è possibile raggruppare i file in base ai nomi di directory. 

Questo esempio raccoglie intere directory a cui inviare comandi ``cp`` eseguiti come attività in background:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Dopo aver raccolto i file, è possibile eseguire comandi di copia parallela per copiare in modo ricorsivo le sottodirectory e tutti il relativi contenuti:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Quando aggiungere punti di montaggio

Quando si dispone di un numero sufficiente di thread paralleli rispetto a una singola destinazione file system punto di montaggio, sarà presente un punto in cui l'aggiunta di più thread non offre una maggiore velocità effettiva. (La velocità effettiva verrà misurata in byte al secondo o file al secondo, in base al tipo di dati.) Peggio ancora, il threading eccessivo può talvolta causare una riduzione della velocità effettiva.  

Quando si verifica questo problema, è possibile aggiungere punti di montaggio lato client ad altri indirizzi di montaggio della cache HPC di Azure, usando lo stesso percorso di montaggio file system remoto:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

L'aggiunta di punti di montaggio lato client consente di diramare gli ulteriori comandi di copia aggiuntiva verso i punti di montaggio `/mnt/destination[1-3]` aggiuntivi, ottenendo un maggiore parallelismo.  

Ad esempio, se i file sono molto grandi, è possibile definire i comandi di copia in modo da usare percorsi di destinazione diversi, inviando altri comandi in parallelo dal client che esegue la copia.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

Nell'esempio precedente, tutti e tre i punti di montaggio di destinazione sono interessati dai processi di copia file del client.

## <a name="when-to-add-clients"></a>Quando aggiungere client

Per concludere, una volta raggiunto il limite di capacità del client, l'aggiunta di altri thread di copia o punti di montaggio non produrrà alcun incremento in termini di file per secondo o byte per secondo. In questo caso è possibile distribuire un altro client con lo stesso set di punti di montaggio, che eseguirà un proprio set di processi di copia dei file. 

Esempio:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Creare manifesti di file

Dopo aver compreso gli approcci precedenti (più thread di copia per destinazione, più destinazioni per client, più client per origine accessibile alla rete file system), considerare questa raccomandazione: creare manifesti di file e quindi usarli con i comandi di copia tra più client.

Questo scenario usa il comando UNIX ``find`` per creare manifesti di file o directory:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Reindirizzare il risultato a un file: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Quindi è possibile scorrere nel manifesto, usando comandi BASH per contare i file e determinare le dimensioni delle sottodirectory:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Infine, è necessario creare gli effettivi comandi di copia file per i client.  

Se si hanno quattro client, usare questo comando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Se hai cinque client, usare un comando simile al seguente:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Per sei... Estrapolare in base alle esigenze.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Si otterranno *N* file risultanti, uno per ognuno degli *N* client i cui nomi percorso si trovano nelle directory di quarto livello ottenute come parte dell'output del comando `find`. 

Usare ogni file per compilare il comando di copia:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Il comando precedente fornirà *N* file, ognuno con un comando di copia per riga, che può essere eseguito come script BASH nel client. 

L'obiettivo è eseguire contemporaneamente più thread di questi script per client, in parallelo in più client.
