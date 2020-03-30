---
title: Inserimento dei dati della cache HPC di Azure - script di copia parallelaAzure HPC Cache data ingest - parallel copy script
description: Come usare uno script di copia parallela per spostare i dati in una destinazione di archiviazione BLOB nella cache HPC di AzureHow to use a parallel copy script to move data to a Blob storage target in Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90e05ad3d42b1009b631630fe476669a9f418d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74166887"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Inserimento dei dati della cache HPC di Azure - metodo di script di copia parallelaAzure HPC Cache data ingest - parallel copy script method

Questo articolo fornisce istruzioni ``parallelcp`` per creare lo script e usarlo per spostare i dati in un contenitore di archiviazione BLOB per l'uso con la cache HPC di Azure.This article gives instructions for creating the script and using it to move data to a Blob storage container for use with Azure HPC Cache.

Per altre informazioni sullo spostamento dei dati nell'archivio BLOB per la cache HPC di Azure, vedere [Spostare i dati nell'archiviazione BLOB](hpc-cache-ingest.md)di Azure.To learn more about moving data to Blob storage for your Azure HPC Cache, read Move data to Azure Blob storage .

## <a name="create-the-parallelcp-script"></a>Creare lo script parallelcpCreate the parallelcp script

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

## <a name="parallel-copy-example"></a>Esempio di copia parallela

Questo esempio usa lo script ``glibc`` di copia parallela per eseguire la compilazione usando i file di origine nella cache HPC di Azure.This example uses the parallel copy script to compile using source files in the Azure HPC Cache.

I file di origine vengono memorizzati nella cache nel punto di montaggio della cache HPC di Azure e i file oggetto vengono archiviati nel disco rigido locale.

In questo esempio viene utilizzato ``-j`` lo ``make`` script di copia parallela con l'opzione e per ottenere la parallelizzazione.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
