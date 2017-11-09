---
title: Creare un processo di esportazione per Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come creare un processo di esportazione per il servizio Importazione/Esportazione di Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: bdeac373aa8270bd9de8f135ec7166d744fd83ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Creazione di un processo di esportazione per Importazione/Esportazione di Azure
La creazione di un processo di esportazione per il servizio Importazione/Esportazione di Microsoft Azure con l'API REST prevede i passaggi seguenti:

-   Selezione dei BLOB da esportare.

-   Acquisizione di una località di spedizione.

-   Creazione del processo di esportazione.

-   Spedizione delle unità vuote a Microsoft tramite un vettore supportato.

-   Aggiornamento del processo di esportazione con le informazioni del pacchetto.

-   Ricezione delle unità da Microsoft.

 Vedere [Uso del servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati all'archivio BLOB](storage-import-export-service.md) per una panoramica del servizio Importazione/Esportazione e un'esercitazione che illustra come usare il [portale di Azure](https://portal.azure.com/) per creare e gestire i processi di importazione ed esportazione.

## <a name="selecting-blobs-to-export"></a>Selezione dei BLOB da esportare
 Per creare un processo di esportazione, sarà necessario specificare un elenco di BLOB che si vuole esportare dall'account di archiviazione. È possibile selezionare i BLOB da esportare in modi diversi:

-   È possibile usare un percorso BLOB relativo per selezionare un singolo BLOB e tutti gli snapshot.

-   È possibile usare un percorso BLOB relativo per selezionare un singolo BLOB escludendo gli snapshot.

-   È possibile usare un percorso BLOB relativo e un tempo di snapshot per selezionare un singolo snapshot.

-   È possibile usare un prefisso BLOB per selezionare tutti i BLOB e gli snapshot con il prefisso specificato.

-   È possibile esportare tutti i BLOB e gli snapshot nell'account di archiviazione.

 Per altre informazioni sulla specifica dei BLOB da esportare, vedere l'operazione [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate).

## <a name="obtaining-your-shipping-location"></a>Acquisizione della località di spedizione
Prima di creare un processo di esportazione, è necessario ottenere il nome e l'indirizzo di una località di spedizione chiamando l'operazione [Get Location](https://portal.azure.com) o [List Locations](/rest/api/storageimportexport/listlocations). `List Locations` restituirà un elenco di località con gli indirizzi postali. È possibile selezionare una posizione dall'elenco restituito e spedire i dischi rigidi a tale indirizzo. È anche possibile usare l'operazione `Get Location` per ottenere direttamente l'indirizzo di spedizione di una posizione specifica.

Seguire i passaggi sotto per ottenere la posizione di spedizione:

-   Identificare il nome della località dell'account di archiviazione. Si può trovare questo valore nel campo **Località** nel **dashboard** dell'account di archiviazione del portale classico oppure lo si può cercare usando l'operazione dell'API Gestione dei servizi [Get Storage Account Properties](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Recuperare la località disponibile per elaborare questo account di archiviazione chiamando l'operazione `Get Location`.

-   Se la proprietà `AlternateLocations` della località contiene la località stessa, è possibile usare questa località. In caso contrario, chiamare di nuovo l'operazione `Get Location` con una delle posizione alternative. La località originale potrebbe essere chiusa temporaneamente per manutenzione.

## <a name="creating-the-export-job"></a>Creazione del processo di esportazione
 Per creare il processo di esportazione, chiamare l'operazione [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Sarà necessario specificare le informazioni seguenti:

-   Un nome per il processo.

-   nome dell'account di archiviazione.

-   Il nome della località di spedizione, ottenuto nel passaggio precedente.

-   Un tipo di processo (esportazione).

-   L'indirizzo mittente dove inviare le unità al termine del processo di esportazione.

-   L'elenco di BLOB (o i prefissi BLOB) da esportare.

## <a name="shipping-your-drives"></a>Spedizione delle unità
 Usare ora lo strumento Importazione/Esportazione di Azure per determinare il numero di unità necessarie da inviare, in base ai BLOB selezionati per l'esportazione e alle dimensioni delle unità. Per i dettagli vedere [Azure Import/Export Tool Reference](storage-import-export-tool-how-to-v1.md) (Informazioni di riferimento sullo strumento Importazione/Esportazione di Azure).

 Inserire le unità in un singolo pacchetto e spedirle all'indirizzo ottenuto nel passaggio precedente. Prendere nota del numero di tracciabilità del pacchetto per il passaggio successivo.

> [!NOTE]
>  È necessario spedire le unità con un vettore supportato, che fornirà un numero di tracciabilità per il pacchetto.

## <a name="updating-the-export-job-with-your-package-information"></a>Aggiornamento del processo di esportazione con le informazioni del pacchetto
 Dopo avere ottenuto il numero di tracciabilità, chiamare l'operazione [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) per aggiornare il nome del vettore e il numero di tracciabilità per il processo. È facoltativamente possibile specificare anche il numero di unità, l'indirizzo mittente e la data di spedizione.

## <a name="receiving-the-package"></a>Ricezione del pacchetto
 Dopo che il processo di esportazione è stato elaborato, le unità verranno restituite all'utente con i dati crittografati. È possibile recuperare la chiave BitLocker per ogni unità chiamando l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). È quindi possibile sbloccare l'unità usando la chiave. Il file manifesto di ogni unità contiene l'elenco di file dell'unità, oltre all'indirizzo BLOB originale per ogni file.

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)
