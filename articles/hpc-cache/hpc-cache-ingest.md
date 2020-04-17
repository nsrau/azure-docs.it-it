---
title: Spostare i dati in un contenitore cloud della cache HPC di AzureMove data to an Azure HPC Cache cloud container
description: Come popolare l'archiviazione BLOB di Azure per l'uso con la cache HPC di AzureHow to populate Azure Blob storage for use with Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537934"
---
# <a name="move-data-to-azure-blob-storage"></a>Spostare i dati nell'archiviazione BLOB di AzureMove data to Azure Blob storage

Se il flusso di lavoro include lo spostamento dei dati nell'archivio BLOB di Azure, assicurarsi di usare una strategia efficiente. È possibile precaricare i dati in un nuovo contenitore BLOB prima di definirlo come destinazione di archiviazione oppure aggiungere il contenitore e quindi copiare i dati usando la cache HPC di Azure.You can either preload data in a new Blob container before defining it as a storage target, or add the container and then copy your data using Azure HPC Cache.

Questo articolo illustra i modi migliori per spostare i dati nell'archivio BLOB per usarli con la cache HPC di Azure.This article explains the best ways to move data to Blob storage for use with Azure HPC Cache.

Tenete a mente questi fatti:

* La cache HPC di Azure usa un formato di archiviazione specializzato per organizzare i dati nell'archiviazione BLOB. Questo è il motivo per cui una destinazione di archiviazione BLOB deve essere un nuovo contenitore vuoto o un contenitore BLOB usato in precedenza per i dati della cache HPC di Azure.This is why a Blob storage target must either be a new, empty container, or a Blob container that was previously used for Azure HPC Cache data.

* La copia dei dati tramite la cache HPC di Azure in una destinazione di archiviazione back-end è più efficiente quando si usano più client e operazioni parallele. Un semplice comando di copia da un client sposterà i dati lentamente.

È disponibile un'utilità basata su Python per caricare il contenuto in un contenitore di archiviazione BLOB. Per altre informazioni, leggere [Pre-load data in Blob storage (Informazioni in base al caricamento preliminare nell'archiviazione BLOB).](#pre-load-data-in-blob-storage-with-clfsload)

Se non si vuole usare l'utilità di caricamento o se si vuole aggiungere contenuto a una destinazione di archiviazione esistente, seguire i suggerimenti per l'inserimento di dati paralleli in Copiare i [dati tramite la](#copy-data-through-the-azure-hpc-cache)cache HPC di Azure.

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Precaricare i dati nell'archivio BLOB con CLFSLoadPre-load data in Blob storage with CLFSLoad

È possibile usare l'utilità Avere CLFSLoad per copiare i dati in un nuovo contenitore di archiviazione BLOB prima di aggiungerlo come destinazione di archiviazione. Questa utilità viene eseguita in un singolo sistema Linux e scrive i dati nel formato proprietario necessario per la cache HPC di Azure.This utility runs on a single Linux system and writes data in the proprietary format needed for Azure HPC Cache. CLFSLoad è il modo più efficiente per popolare un contenitore di archiviazione BLOB da usare con la cache.

L'utilità Avere CLFSLoad è disponibile su richiesta del team della cache HPC di Azure.The Avere CLFSLoad utility is available by request from your Azure HPC Cache team. Chiedi al tuo team di contattarlo o apri un ticket di [supporto](hpc-cache-support-ticket.md) per richiedere assistenza.

Questa opzione funziona solo con nuovi contenitori vuoti. Creare il contenitore prima di utilizzare Avere CLFSLoad.

Informazioni dettagliate sono incluse nella distribuzione di Avere CLFSLoad, disponibile su richiesta del team della cache HPC di Azure.Detailed information is included in the Avere CLFSLoad distribution, which is available on request from the Azure HPC Cache team.

Una panoramica generale del processo:

1. Preparare un sistema Linux (VM o fisico) con Python versione 3.6 o successiva. Python 3.7 è consigliato per migliorare le prestazioni.
1. Installare il software Avere-CLFSLoad nel sistema Linux.
1. Eseguire il trasferimento dalla riga di comando Linux.

L'utilità Avere CLFSLoad richiede le seguenti informazioni:

* ID dell'account di archiviazione che contiene il contenitore di archiviazione BLOBThe storage account ID that contains your Blob storage container
* Nome del contenitore di archiviazione BLOB vuoto
* Un token di firma di accesso condiviso che consente all'utilità di scrivere nel contenitore
* Un percorso locale dell'origine dati, ovvero una directory locale contenente i dati da copiare o un percorso locale a un sistema remoto montato con i dati

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copiare i dati tramite la cache HPC di AzureCopy data through the Azure HPC Cache

Se non si vuole usare l'utilità Avere CLFSLoad o se vuoi aggiungere una grande quantità di dati a una destinazione di archiviazione BLOB esistente, puoi copiarli nella cache. Cache HPC di Azure è progettata per servire più client contemporaneamente, pertanto per copiare i dati tramite la cache, è consigliabile usare scritture parallele da più client.

![Diagramma che mostra lo spostamento dati multi-client a thread multipli: in alto a sinistra, da un'icona per la risorsa di archiviazione hardware locale partono più frecce. Le frecce puntano a quattro computer client. Da ogni computer client tre frecce puntano verso la cache HPC di Azure.From each client machine three arrows point towards the Azure HPC Cache. Dalla cache HPC di Azure, più frecce puntano all'archiviazione BLOB.](media/hpc-cache-parallel-ingest.png)

I ``cp`` ``copy`` comandi o utilizzati in genere per trasferire dati da un sistema di archiviazione a un altro sono processi a thread singolo che copiano un solo file alla volta. Ciò significa che il file server sta ingerendo un solo file alla volta, che è uno spreco delle risorse della cache.

Questa sezione illustra le strategie per la creazione di un sistema di copia di file multi-client multithreading per spostare i dati nell'archiviazione BLOB con la cache HPC di Azure.This section explains strategies for creating a multi-client, multi-threaded file copying system to move data to Blob storage with Azure HPC Cache. Spiega i concetti relativi al trasferimento di file e le decisioni da prendere per una copia dei dati efficiente usando più client e semplici comandi di copia.

Illustra inoltre alcune utilità che possono essere di ausilio. L'utilità ``msrsync`` può essere usata per automatizzare parzialmente il processo di suddivisione di un set di dati in contenitori e uso dei comandi rsync. Lo script ``parallelcp`` è un'altra utilità che legge la directory di origine e invia automaticamente i comandi di copia.

### <a name="strategic-planning"></a>Pianificazione strategica

Quando si crea una strategia per copiare dati in parallelo, è necessario comprendere i compromessi in termini di dimensioni dei file, numero di file e profondità di directory.

* Quando i file sono di piccole dimensioni, la metrica di interesse è file al secondo.
* Quando i file sono di grandi dimensioni (10 MiBi o oltre), la metrica di interesse è byte al secondo.

Ogni processo di copia ha una velocità effettiva e una velocità di trasferimento dei file, che può essere misurata cronometrando la lunghezza del comando di copia e considerando le dimensioni del file e il numero di file. Spiegare come misurare questi valori non rientra nell'ambito di questo documento, ma è fondamentale comprendere se si gestiranno file piccoli o grandi dimensioni.

Le strategie per l'inserimento di dati paralleli con la cache HPC di Azure includono:Strategies for parallel data ingest with Azure HPC Cache include:

* Copia manuale: è possibile creare manualmente una copia multithread su un client eseguendo più di un comando di copia contemporaneamente in background su set predefiniti di file o percorsi. Leggere [Azure HPC Cache Data ingest - metodo](hpc-cache-ingest-manual.md) di copia manuale per i dettagli.

* La copia parzialmente automatizzata ``msrsync``  -  ``msrsync`` con è ``rsync`` un'utilità wrapper che esegue più processi paralleli. Per informazioni dettagliate, leggere [Archiviazione dati cache HPC di Azure - metodo msrsync](hpc-cache-ingest-msrsync.md).

* Copia tramite ``parallelcp`` script con - Informazioni su come creare ed eseguire uno script di copia parallela nell'inserimento dei dati della [cache HPC di Azure - metodo di script](hpc-cache-ingest-parallelcp.md)di copia parallela .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato lo spazio di archiviazione, scopri come i client possono montare la cache.

* [Accedere al sistema di cache HPC di AzureAccess the Azure HPC Cache system](hpc-cache-mount.md)
