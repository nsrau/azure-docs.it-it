---
title: Spostare i dati in un contenitore cloud di cache HPC di Azure
description: Come popolare l'archivio BLOB di Azure per l'uso con la cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a206b63b03bcb3bb17e201487f0e00bcb3926151
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582237"
---
# <a name="move-data-to-azure-blob-storage"></a>Spostare i dati nell'archivio BLOB di Azure

Se il flusso di lavoro include lo stato di trasferimento dei dati nell'archiviazione BLOB di Azure, assicurarsi di usare una strategia efficiente. È possibile precaricare i dati in un nuovo contenitore BLOB prima di definirli come destinazione di archiviazione oppure aggiungere il contenitore e quindi copiare i dati usando la cache HPC di Azure.

Questo articolo illustra i modi migliori per spostare i dati nell'archivio BLOB per l'uso con la cache HPC di Azure.

Tenere presenti i seguenti fattori:

* Cache HPC di Azure usa un formato di archiviazione specializzato per organizzare i dati nell'archivio BLOB. Questo è il motivo per cui una destinazione di archiviazione BLOB deve essere un nuovo contenitore vuoto o un contenitore BLOB usato in precedenza per i dati della cache HPC di Azure. ([VFXT per Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) usa anche questa file System cloud).

* La copia dei dati attraverso la cache HPC di Azure in una destinazione di archiviazione back-end è più efficiente quando si usano più client e operazioni parallele. Un semplice comando Copy da un client sposterà lentamente i dati.

È disponibile un'utilità basata su Python per caricare il contenuto in un contenitore di archiviazione BLOB. Per altre informazioni, leggere [precaricare i dati nell'archivio BLOB](#pre-load-data-in-blob-storage-with-clfsload) .

Se non si vuole usare l'utilità di caricamento o si vuole aggiungere contenuto a una destinazione di archiviazione esistente, seguire i suggerimenti per l'inserimento di dati paralleli in [copiare i dati tramite la cache HPC di Azure](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Pre-caricare i dati nell'archivio BLOB con CLFSLoad

È possibile utilizzare il <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Usare l'utilità CLFSLoad per copiare i dati in un nuovo contenitore di archiviazione BLOB prima di aggiungerli come destinazione di archiviazione. Questa utilità viene eseguita in un singolo sistema Linux e scrive i dati nel formato proprietario necessario per la cache HPC di Azure. CLFSLoad è il modo più efficiente per popolare un contenitore di archiviazione BLOB da usare con la cache.

L'utilità CLFSLoad è disponibile in base alla richiesta del team di cache HPC di Azure. Chiedere al team il contatto o aprire un ticket di [supporto](hpc-cache-support-ticket.md) per richiedere assistenza.

Questa opzione funziona solo con nuovi contenitori vuoti. Creare il contenitore prima di usare CLFSLoad.

Informazioni dettagliate sono incluse nella distribuzione CLFSLoad, disponibile su richiesta dal team di cache HPC di Azure. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

Panoramica generale del processo:

1. Preparare un sistema Linux (VM o fisico) con Python versione 3,6 o successiva. È consigliabile usare Python 3,7 per ottenere prestazioni migliori.
1. Installare il software CLFSLoad nel sistema Linux.
1. Eseguire il trasferimento dalla riga di comando di Linux.

L'utilità CLFSLoad ha bisogno delle informazioni seguenti:

* ID dell'account di archiviazione che contiene il contenitore di archiviazione BLOB
* Nome del contenitore di archiviazione BLOB vuoto
* Un token di firma di accesso condiviso che consente all'utilità di scrivere nel contenitore
* Un percorso locale per l'origine dati, ovvero una directory locale che contiene i dati da copiare o un percorso locale a un sistema remoto montato con i dati

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copiare dati tramite la cache HPC di Azure

Se non si vuole usare l'utilità CLFSLoad o se si vuole aggiungere una grande quantità di dati a una destinazione di archiviazione BLOB esistente, è possibile copiarla nella cache. La cache HPC di Azure è progettata per gestire più client simultaneamente, quindi per copiare i dati attraverso la cache, è consigliabile usare Scritture parallele da più client.

![Diagramma che mostra lo spostamento dati multi-client a thread multipli: in alto a sinistra, da un'icona per la risorsa di archiviazione hardware locale partono più frecce. Le frecce puntano a quattro computer client. Da ogni computer client tre frecce puntano verso la cache HPC di Azure. Dalla cache HPC di Azure, più frecce puntano all'archiviazione BLOB.](media/hpc-cache-parallel-ingest.png)

I comandi ``cp`` o ``copy`` usati in genere per trasferire i dati da un sistema di archiviazione a un altro sono processi a thread singolo che copiano un solo file alla volta. Ciò significa che il file server inserisce un solo file alla volta, ovvero uno spreco di risorse della cache.

Questa sezione illustra le strategie per la creazione di un sistema di copia di file multiclient multithread per spostare i dati nell'archivio BLOB con la cache HPC di Azure. Spiega i concetti relativi al trasferimento di file e le decisioni da prendere per una copia dei dati efficiente usando più client e semplici comandi di copia.

Illustra inoltre alcune utilità che possono essere di ausilio. L'utilità ``msrsync`` può essere usata per automatizzare parzialmente il processo di suddivisione di un set di dati in contenitori e uso dei comandi rsync. Lo script ``parallelcp`` è un'altra utilità che legge la directory di origine e invia automaticamente i comandi di copia.

### <a name="strategic-planning"></a>Pianificazione strategica

Quando si crea una strategia per copiare dati in parallelo, è necessario comprendere i compromessi in termini di dimensioni dei file, numero di file e profondità di directory.

* Quando i file sono di piccole dimensioni, la metrica di interesse è file al secondo.
* Quando i file sono di grandi dimensioni (10 MiBi o oltre), la metrica di interesse è byte al secondo.

Ogni processo di copia ha una velocità effettiva e una velocità di trasferimento dei file, che può essere misurata cronometrando la lunghezza del comando di copia e considerando le dimensioni del file e il numero di file. Spiegare come misurare questi valori non rientra nell'ambito di questo documento, ma è fondamentale comprendere se si gestiranno file piccoli o grandi dimensioni.

Le strategie per l'inserimento di dati paralleli con la cache HPC di Azure includono:

* Copia manuale: è possibile creare manualmente una copia multithread in un client eseguendo più di un comando copy contemporaneamente in background su set predefiniti di file o percorsi. Per informazioni dettagliate, vedere Inserimento di [dati nella cache HPC di Azure-metodo di copia manuale](hpc-cache-ingest-manual.md) .

* La copia parzialmente automatica con ``msrsync`` - ``msrsync`` è un'utilità wrapper che esegue più processi di ``rsync`` paralleli. Per informazioni dettagliate, vedere Metodo di inserimento [dei dati nella cache HPC di Azure](hpc-cache-ingest-msrsync.md).

* Copia tramite script con ``parallelcp``: informazioni su come creare ed eseguire uno script di copia parallela nel metodo di inserimento di [dati nella cache HPC di Azure](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato l'archiviazione, informazioni su come i client possono montare la cache.

* [Accedere al sistema di cache HPC di Azure](hpc-cache-mount.md)
