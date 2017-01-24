---
title: Creazione di un processo di importazione per il servizio Importazione/Esportazione di Azure | Microsoft Docs
description: Informazioni su come creare un&quot;importazione per il servizio di Importazione/Esportazione di Microsoft Azure
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: 
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 348757809fb5820e2c5c2d5806ec3c74353f3fc8


---
# <a name="creating-an-import-job"></a>Creazione di un processo di importazione

La creazione di un processo di importazione per il servizio di Importazione/Esportazione di Microsoft Azure utilizzando l'API REST prevede i passaggi seguenti:

-   Preparazione delle unità con lo strumento di Importazione/Esportazione di Azure.

-   Ottenimento della posizione a cui si desidera spedire l'unità.

-   Creazione del processo di importazione.

-   Spedizione delle unità vuote a Microsoft tramite un vettore supportato.

-   Aggiornamento del processo di importazione con i dettagli di spedizione.

 Vedere [Uso del servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](storage-import-export-service.md) per una panoramica del servizio Importazione/Esportazione e un'esercitazione che illustra come usare il [portale di Azure](https://portal.azure.com/) per creare e gestire i processi di importazione ed esportazione.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Preparazione delle unità con lo strumento di Importazione/Esportazione di Azure

I passaggi per preparare le unità per un processo di importazione sono uguali se si crea il processo tramite il portale o l'API REST.

Di seguito è riportata una breve panoramica della preparazione dell'unità. Per istruzioni complete, vedere il [Riferimento dello strumento di importazione/esportazione di Azure](storage-import-export-tool-how-to-v1.md). È possibile scaricare lo strumento Importazione/Esportazione di Azure [qui](http://go.microsoft.com/fwlink/?LinkID=301900).

La preparazione dell'unità comporta:

-   Identificazione dei dati da importare.

-   Identificazione dei BLOB di destinazione in Archiviazione di Azure.

-   Uso dello strumento di Importazione/Esportazione di Azure per copiare i dati in uno o più dischi rigidi.

 Lo strumento di Importazione/Esportazione di Azure genererà inoltre un file manifesto per ciascuna unità preparata. Contiene un file manifesto:

-   Enumerazione di tutti i file destinati al caricamento e il mapping tra questi file sui BLOB.

-   Checksum dei segmenti di ciascun file.

-   Informazioni sui metadati e proprietà da associare a ogni BLOB.

-   Elenco delle azioni da intraprendere se un BLOB che viene caricato ha lo stesso nome di un BLOB esistente nel contenitore. Le opzioni possibili sono: a) sovrascrivere il BLOB con il file, b) mantenere il BLOB esistente e ignorare il caricamento del file, c) aggiungere un suffisso al nome in modo che non sia in conflitto con altri file.

## <a name="obtaining-your-shipping-location"></a>Acquisizione della posizione di spedizione

Prima di creare un processo di importazione, è necessario ottenere il nome e l'indirizzo di una posizione di spedizione chiamando l'operazione [List Locations](/rest/api/storageimportexport/listlocations) (Elenca posizioni). `List Locations` restituirà un elenco di posizione con gli indirizzi postali. È possibile selezionare una posizione dall'elenco restituito e spedire i dischi rigidi a tale indirizzo. È anche possibile usare l'operazione `Get Location` per ottenere direttamente l'indirizzo di spedizione di una posizione specifica.

 Seguire i passaggi sotto per ottenere la posizione di spedizione:

-   Identificare il nome della posizione dell'account di archiviazione. Si può trovare questo valore nel campo **Posizione** nel **dashboard** dell'account di archiviazione del portale di Azure oppure lo si può cercare usando l'operazione dell'API Gestione dei servizi [Get Storage Account Properties](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties) (Ottieni proprietà dell'account di archiviazione ).

-   Recuperare la posizione disponibile per elaborare questo account di archiviazione chiamando l'operazione `Get Location`.

-   Se la proprietà `AlternateLocations` della posizione contiene la posizione stessa, è possibile usare questa posizione. In caso contrario, chiamare di nuovo l'operazione `Get Location` con una delle posizione alternative. La posizione originale potrebbe essere chiusa temporaneamente per manutenzione.

## <a name="creating-the-import-job"></a>Creazione del processo di importazione
Per creare il processo di importazione, chiamare l'operazione [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (Inserisci processo). Sarà necessario specificare le informazioni seguenti:

-   Un nome per il processo.

-   nome dell'account di archiviazione.

-   Il nome della posizione di spedizione, ottenuto nel passaggio precedente.

-   Un tipo di processo (importazione).

-   L'indirizzo mittente dove inviare le unità al termine del processo di importazione.

-   Elenco delle unità nel processo. Per ogni unità, è necessario includere le seguenti informazioni ottenute durante la fase di preparazione dell'unità:

    -   ID dell'unità

    -   La chiave di BitLocker

    -   Il percorso relativo del file manifesto nel disco rigido

    -   Hash MD5 del file manifesto codificato in base 16

## <a name="shipping-your-drives"></a>Spedizione delle unità
È necessario spedire le unità all'indirizzo ottenuto nel passaggio precedente e fornire il numero di tracciabilità del pacchetto nel servizio di Importazione/Esportazione.

> [!NOTE]
>  È necessario spedire le unità con un vettore supportato, che fornirà un numero di tracciabilità per il pacchetto.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Aggiornamento del processo di importazione con le informazioni sulla spedizione
Dopo avere ottenuto il numero di tracciabilità, chiamare l'operazione [Update Job Properties](/api/storageimportexport/jobs#Jobs_Update) (Aggiorna proprietà processo) per aggiornare il nome del vettore, il numero di tracciabilità per il processo e il numero di account del vettore per la spedizione per reso. Facoltativamente è possibile specificare il numero di unità e la data di spedizione.

## <a name="see-also"></a>Vedere anche
[Using the Import/Export service REST API](storage-import-export-using-the-rest-api.md) (Uso dell'API REST del servizio Importazione/Esportazione)



<!--HONumber=Dec16_HO3-->


