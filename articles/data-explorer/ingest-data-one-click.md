---
title: Usare l'inserimento con un clic per inserire dati in Esplora dati di Azure
description: Panoramica dell'inserimento (caricamento) di dati in Esplora dati di Azure con un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521569"
---
# <a name="what-is-one-click-ingestion"></a>Che cos'è l'inserimento con un clic? 

L'inserimento con un clic consente di inserire rapidamente i dati e ricevere automaticamente suggerimenti su tabelle e strutture di mapping, in base a un'origine dati in Esplora dati di Azure. 

Usando l'interfaccia utente Web di Esplora dati di Azure, è possibile inserire i dati dallo spazio di archiviazione (file BLOB), da un file locale o da un contenitore (fino a 10.000 BLOB). È anche possibile definire una griglia di eventi in un contenitore per l'inserimento continuo. I dati possono essere inseriti in una tabella nuova o esistente in formato JSON, CSV e [altri](#file-formats). L'inserimento con un clic può suggerire una struttura per una nuova tabella e un nuovo mapping di tabelle, in base all'origine dati, e fornisce una piattaforma intuitiva per modificare la struttura di una tabella e di un mapping di tabelle esistenti. L'inserimento con un clic inserirà i dati nella tabella entro pochi minuti.

L'inserimento con un clic è particolarmente utile quando si inseriscono i dati per la prima volta o quando non si ha familiarità con lo schema dei dati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Creare [un database e un cluster di Esplora dati di Azure](create-cluster-database-portal.md).
* Accedere all'[interfaccia utente Web di Esplora dati di Azure](https://dataexplorer.azure.com/) e [aggiungere una connessione al cluster](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Formati di file

L'inserimento con un clic supporta l'inserimento di una nuova tabella dai dati di origine in uno dei formati seguenti:
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>Inserimento guidato con un clic

L'inserimento guidato con un clic illustra i passaggi dell'intero processo. 

> [!Note]
> Questa sezione descrive la procedura guidata in generale. Le opzioni selezionate variano a seconda che si inseriscano i dati in una tabella nuova o esistente. Per altre informazioni, vedere:
    > * Inserire dati in una [nuova tabella](one-click-ingestion-new-table.md)
    > * Inserire dati in una [tabella esistente](one-click-ingestion-existing-table.md) 
    
1. Per accedere alla procedura guidata, fare clic con il pulsante destro del mouse sulla riga del *database* o della *tabella* nel menu sinistro dell'interfaccia utente Web di Esplora dati di Azure e scegliere **Ingest new data (preview)** (Inserisci nuovi dati (anteprima)).

    ![Selezionare l'inserimento con un clic nell'interfaccia utente Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. Questa procedura guidata illustra le opzioni seguenti:
       * Inserire dati in una [tabella esistente](one-click-ingestion-existing-table.md)
       * Inserire dati in una [nuova tabella](one-click-ingestion-new-table.md)
       * Inserire dati da: * archiviazione BLOB * un file locale * un contenitore
       * Immettere le dimensioni del campione, da 1 a 10.000 righe (solo da contenitore)
       
1. Una volta selezionata correttamente l'origine dati, viene visualizzata un'anteprima dei dati. 
    Se si inseriscono dati da un contenitore, è possibile filtrarli in modo che vengano inseriti solo i file con estensioni o prefissi specifici. Ad esempio, è possibile inserire solo i file con nomi che iniziano con la parola *Europa* oppure solo i file con estensione *json*. 

1. Fare clic su **Modifica schema**. Se si inseriscono dati in una tabella specifica, è possibile mappare le colonne di origine a quelle di destinazione e decidere se includerne o meno i nomi.

1. Avviare il processo di inserimento dati.

> [!Note]
> Se l'origine dati è un contenitore, si noti che il criterio di aggregazione (in batch) per l'inserimento di dati di Esplora dati di Azure è progettato per ottimizzare il processo. Per impostazione predefinita, il criterio è configurato su 5 minuti o su 500 MB di dati, quindi si potrebbe riscontrare una latenza. Per le opzioni di aggregazione, vedere [criteri di creazione di batch](/azure/kusto/concepts/batchingpolicy). Quando si inseriscono dati da altre origini, l'inserimento avrà effetto immediato.

## <a name="next-steps"></a>Passaggi successivi

* Decidere se si userà l'inserimento con un clic per inserire i dati in [una tabella esistente](one-click-ingestion-existing-table.md) o in [una nuova tabella](one-click-ingestion-new-table.md)
* [Eseguire query sui dati nell'interfaccia utente Web di Esplora dati di Azure](/azure/data-explorer/web-query-data)
* [Scrivere query per Esplora dati di Azure con il linguaggio di query Kusto](/azure/data-explorer/write-queries)