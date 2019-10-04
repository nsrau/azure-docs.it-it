---
title: Azure HPC cache anteprima inserimento dati-script di copia parallela
description: Come usare uno script di copia parallela per spostare i dati in una destinazione di archiviazione BLOB nella cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 852b4e692a4316c7701c8c179039104bee561949
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180962"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Cache HPC di Azure (anteprima) inserimento dati-metodo script copia parallela

Questo articolo fornisce le istruzioni per creare ``parallelcp`` lo script e usarlo per spostare i dati in un contenitore di archiviazione BLOB per l'uso con la cache HPC di Azure.

Per altre informazioni sullo spostamento dei dati nell'archiviazione BLOB per la cache HPC di Azure, vedere [spostare i dati nell'archivio BLOB di Azure per la cache HPC](hpc-cache-ingest.md)di Azure.

## <a name="create-the-parallelcp-script"></a>Creare lo script parallelcp

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

Questo esempio usa lo script di copia parallela ``glibc`` per compilare usando i file di origine nella cache HPC di Azure.

I file di origine vengono memorizzati nella cache nel punto di montaggio della cache HPC di Azure e i file oggetto vengono archiviati sul disco rigido locale.

In questo esempio viene usato lo script di copia parallela ``make`` con l'opzione ``-j`` e per ottenere la parallelizzazione.

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
