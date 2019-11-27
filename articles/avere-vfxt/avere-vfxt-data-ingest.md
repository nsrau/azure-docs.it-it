---
title: Spostamento dei dati in Avere vFXT per Azure
description: Come aggiungere dati a un nuovo volume di archiviazione da usare con Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: rohogue
ms.openlocfilehash: 183ed719eb5396fe0e442e6b774d962d1ba48386
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480600"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Spostamento dei dati nel cluster vFXT - Inserimento di dati parallelo

Dopo aver creato un nuovo cluster vFXT, la prima attività da eseguire potrebbe essere spostare i dati nel nuovo volume di archiviazione. Tuttavia, se il metodo che si usa in genere per spostare i dati è l'esecuzione di un semplice comando di copia da un client, probabilmente le prestazioni di copia risulteranno lente. La copia a thread singolo non rappresenta una scelta ottimale per copiare dati nella risorsa di archiviazione back-end del cluster Avere vFXT.

Poiché il cluster Avere vFXT è una cache multi-client scalabile, il modo più rapido ed efficiente per copiare i dati consiste nell'usare più thread. Questa tecnica parallelizza l'inserimento dei file e degli oggetti.

![Diagramma che mostra lo spostamento dati multi-client a thread multipli: in alto a sinistra, da un'icona per la risorsa di archiviazione hardware locale partono più frecce. Le frecce puntano a quattro computer client. Da ogni computer client tre frecce puntano verso Avere vFXT. Da Avere vFXT, più frecce puntano all'archiviazione BLOB.](media/avere-vfxt-parallel-ingest.png)

I comandi ``cp`` o ``copy`` che vengono comunemente usati per trasferire dati da un sistema di archiviazione all'altro sono processi a thread singolo, che copiano un solo file alla volta. Questo significa che il file server riceve un solo file alla volta, il che rappresenta uno spreco di risorse del cluster.

Questo articolo illustra le strategie per creare un sistema di copia multi-client a thread multipli per spostare dati nel cluster Avere vFXT. Spiega i concetti relativi al trasferimento di file e le decisioni da prendere per una copia dei dati efficiente usando più client e semplici comandi di copia.

Illustra inoltre alcune utilità che possono essere di ausilio. L'utilità ``msrsync`` può essere utilizzata per automatizzare parzialmente il processo di suddivisione di un set di dati in bucket e l'utilizzo di ``rsync`` comandi. Lo script ``parallelcp`` è un'altra utilità che legge la directory di origine e invia automaticamente i comandi di copia. Inoltre, lo strumento ``rsync`` può essere utilizzato in due fasi per offrire una copia più veloce che fornisce ancora la coerenza dei dati.

Fare clic sul collegamento per passare a una sezione:

* [Esempio di copia manuale ](#manual-copy-example): spiegazione completa sull'uso dei comandi di copia
* [Esempio di rsync a due fasi](#use-a-two-phase-rsync-process)
* [Esempio di automazione parziale (msrsync)](#use-the-msrsync-utility)
* [Esempio di copia parallela](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Modello di macchina virtuale per l'inserimento dei dati

In GitHub è disponibile un modello di Resource Manager per creare automaticamente una macchina virtuale con gli strumenti di inserimento dati parallelo menzionati in questo articolo.

![diagramma che mostra più frecce che partono da ogni archivio BLOB, archiviazione hardware e origine file di Azure. Le frecce puntano a una macchina virtuale per l'inserimento dei dati da cui partono più frecce che puntano ad Avere vFXT](media/avere-vfxt-ingestor-vm.png)

La macchina virtuale per l'inserimento dei dati fa parte di un'esercitazione in cui la macchina virtuale appena creata monta il cluster Avere vFXT e scarica lo script di bootstrap dal cluster. Per informazioni dettagliate, leggere [Bootstrap a data ingestor VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) (Bootstrap di una macchina virtuale per l'inserimento dei dati).

## <a name="strategic-planning"></a>Pianificazione strategica

Quando si crea una strategia per copiare dati in parallelo, è necessario comprendere i compromessi in termini di dimensioni dei file, numero di file e profondità di directory.

* Quando i file sono di piccole dimensioni, la metrica di interesse è file al secondo.
* Quando i file sono di grandi dimensioni (10 MiBi o oltre), la metrica di interesse è byte al secondo.

Ogni processo di copia ha una velocità effettiva e una velocità di trasferimento dei file, che può essere misurata cronometrando la lunghezza del comando di copia e considerando le dimensioni del file e il numero di file. Spiegare come misurare questi valori non rientra nell'ambito di questo documento, ma è fondamentale comprendere se si gestiranno file piccoli o grandi dimensioni.

## <a name="manual-copy-example"></a>Esempio di copia manuale

Si può creare manualmente una copia a thread multipli in un client eseguendo simultaneamente più comandi di copia in background su un set predefinito di file o percorsi.

Il comando Linux/UNIX ``cp`` include l'argomento ``-p`` per conservare i metadati mtime e di proprietà. L'aggiunta di questo argomento ai comandi riportati di seguito è facoltativa. (L'aggiunta dell'argomento aumenta il numero di chiamate al file system inviate dal client al file system di destinazione per la modifica dei metadati.)

Questo semplice esempio copia due file in parallelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Dopo aver eseguito questo comando, il comando `jobs` mostrerà che sono in esecuzione due thread.

### <a name="predictable-filename-structure"></a>Struttura dei nomi file prevedibile

Se i nomi file sono prevedibili, è possibile usare espressioni per creare thread di copia paralleli.

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

### <a name="unknown-filename-structure"></a>Struttura dei nomi file sconosciuta

Se la struttura di denominazione dei file non è prevedibile, è possibile raggruppare file per nomi di directory.

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

### <a name="when-to-add-mount-points"></a>Quando aggiungere punti di montaggio

Quando si raggiunge un numero sufficiente di thread paralleli verso un singolo punto di montaggio del file system di destinazione, vi sarà un punto in cui l'aggiunta di più thread non offre una velocità effettiva maggiore. (La velocità effettiva viene misurata in file al secondo o in byte al secondo, a seconda del tipo di dati.) O peggio, il overthreading può a volte causare una riduzione della velocità effettiva.

In questo caso, è possibile aggiungere punti di montaggio lato client ad altri indirizzi IP del cluster vFXT, usando lo stesso percorso di montaggio del file system remoto:

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

### <a name="when-to-add-clients"></a>Quando aggiungere client

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

### <a name="create-file-manifests"></a>Creare manifesti di file

Dopo aver compreso gli approcci descritti in precedenza (più thread di copia per ogni destinazione, più destinazioni per client, più client per file system di origine accessibile dalla rete), considerare questo consiglio: creare manifesti di file e quindi usarli con i comandi di copia tra più client.

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
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

E per sei.... Estrapolare in base alle esigenze.

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

## <a name="use-a-two-phase-rsync-process"></a>Usare un processo rsync a due fasi

L'utilità di ``rsync`` standard non funziona bene per il popolamento dell'archiviazione cloud tramite il vFXT di sicurezza di rete per Azure perché genera un numero elevato di operazioni di creazione e ridenominazione dei file per garantire l'integrità dei dati. Tuttavia, è possibile usare in modo sicuro l'opzione ``--inplace`` con ``rsync`` per ignorare la procedura di copia più attenta se si segue con una seconda esecuzione che controlla l'integrità dei file.

Un'operazione di copia ``rsync`` standard crea un file temporaneo e lo compila con i dati. Se il trasferimento dei dati viene completato correttamente, il file temporaneo viene rinominato con il nome file originale. Questo metodo garantisce la coerenza anche se si accede ai file durante la copia. Questo metodo genera tuttavia più operazioni di scrittura, che rallentano lo spostamento dei file nella cache.

L'opzione ``--inplace`` scrive il nuovo file direttamente nella posizione finale. Non è garantito che i file siano coerenti durante il trasferimento, ma ciò non è importante se si sta comprimendo un sistema di archiviazione per usarlo in seguito.

La seconda operazione di ``rsync`` funge da verifica della coerenza sulla prima operazione. Poiché i file sono già stati copiati, la seconda fase è un'analisi veloce per assicurarsi che i file nella destinazione corrispondano ai file nell'origine. Se i file non corrispondono, vengono ricopiati.

È possibile eseguire entrambe le fasi insieme in un unico comando:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Questo metodo è un metodo semplice e a tempo effettivo per i set di dati fino al numero di file che possono essere gestiti da gestione directory interna. Si tratta in genere di 200 milioni file per un cluster a 3 nodi, 500 milioni file per un cluster a sei nodi e così via.

## <a name="use-the-msrsync-utility"></a>Usare l'utilità msrsync

Per spostare i dati in un core filer back-end per il cluster Avere si può anche usare lo strumento ``msrsync``. Questo strumento è progettato per ottimizzare l'utilizzo della larghezza di banda mediante l'esecuzione parallela di più processi ``rsync``. È disponibile da GitHub all'indirizzo <https://github.com/jbd/msrsync>.

``msrsync`` suddivide la directory di origine in contenitori separati e quindi esegue singoli processi ``rsync`` in ogni contenitore.

I test preliminari su una macchina virtuale con quattro core hanno indicato la massima efficienza utilizzando 64 processi. Usare l'opzione ``msrsync`` di ``-p`` per impostare il numero di processi su 64.

È anche possibile usare l'argomento ``--inplace`` con ``msrsync`` comandi. Se si usa questa opzione, provare a eseguire un secondo comando (come con [rsync](#use-a-two-phase-rsync-process), descritto in precedenza) per garantire l'integrità dei dati.

``msrsync`` possibile scrivere solo in e da volumi locali. L'origine e la destinazione devono essere accessibili montaggi locali nella rete virtuale del cluster.

Per usare ``msrsync`` per popolare un volume cloud di Azure con un cluster di inserimento, seguire queste istruzioni:

1. Installare ``msrsync`` e i relativi prerequisiti (rsync e Python 2,6 o versione successiva)
1. Determinare il numero totale di file e directory da copiare.

   Usare, ad esempio, l'utilità ``prime.py`` con gli argomenti ```prime.py --directory /path/to/some/directory``` (disponibili scaricando l'URL <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Se non si usa ``prime.py``, è possibile calcolare il numero di elementi con lo strumento di ``find`` GNU come indicato di seguito:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividere il numero di elementi per 64 per determinare il numero di elementi per ogni processo. Usare questo numero con l'opzione ``-f`` per impostare le dimensioni dei contenitori quando si esegue il comando.

1. Eseguire il comando ``msrsync`` per copiare i file:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Se si usa ``--inplace``, aggiungere una seconda esecuzione senza l'opzione per verificare che i dati vengano copiati correttamente:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ad esempio, questo comando è progettato per spostare 11.000 file in 64 processi da /test/source-repository a /mnt/vfxt/repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Usare lo script di copia parallela

Lo script ``parallelcp`` può anche essere utile per lo spostamento di dati nella risorsa di archiviazione back-end del cluster vFXT.

Lo script seguente aggiungerà l'eseguibile `parallelcp`. Questo script è progettato per Ubuntu, se si usa un'altra distribuzione è necessario installare ``parallel`` separatamente.

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Esempio di copia parallela

Questo esempio usa lo script di copia parallela per compilare ``glibc`` usando file di origine dal cluster Avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

I file di origine vengono archiviati nel punto di montaggio del cluster Avere e i file oggetto vengono archiviati nel disco rigido locale.

Questo script usa lo script di copia parallela precedente. L'opzione ``-j`` viene usata con ``parallelcp`` e ``make`` per ottenere la parallelizzazione.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
