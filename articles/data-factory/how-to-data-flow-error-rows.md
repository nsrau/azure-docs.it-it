---
title: Gestire le righe di errore con il mapping dei flussi di dati in Azure Data FactoryHandle error rows with mapping data flows in Azure Data Factory
description: Informazioni su come gestire gli errori di troncamento SQL in Azure Data Factory usando i flussi di dati di mapping.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 4f65421a6457d4bf4d438ce9d035d46476829da2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414365"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Gestire le righe di errore di troncamento SQL nei flussi di dati di mapping di Data FactoryHandle SQL truncation error rows in Data Factory mapping data flows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uno scenario comune in Data Factory quando si usano i flussi di dati di mapping consiste nello scrivere i dati trasformati in un database SQL di Azure.A common scenario in Data Factory when using mapping data flows, is to write your transformed data to an Azure SQL database. In questo scenario, una condizione di errore comune che è necessario impedire è possibile troncamento della colonna. Seguire questi passaggi per fornire la registrazione delle colonne che non rientrano in una colonna di stringa di destinazione, consentendo al flusso di dati di continuare in tali scenari.

## <a name="scenario"></a>Scenario

1. Si dispone di una tabella di ```nvarchar(5)``` database SQL di Azure di destinazione che ha una colonna denominata "nome".

2. All'interno del nostro flusso di dati, vogliamo mappare i titoli dei film dal nostro lavandine alla colonna "name" di destinazione.

    ![Flusso di dati filmato 1](media/data-flow/error4.png)
    
3. Il problema è che il titolo del film non rientra affatto all'interno di una colonna sink che può contenere solo 5 caratteri. Quando si esegue questo flusso di dati, si riceverà un errore simile al seguente:When you execute this data flow, you will receive an error like this one:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Come progettare intorno a questa condizione

1. In questo scenario, la lunghezza massima della colonna "nome" è di cinque caratteri. Quindi, aggiungiamo una trasformazione di divisione condizionale che ci permetterà di registrare le righe con "titoli" che sono più lunghi di cinque caratteri, consentendo anche il resto delle righe che possono adattarsi in quello spazio per scrivere nel database.

    ![suddivisione condizionale](media/data-flow/error1.png)

2. Questa trasformazione di divisione condizionale definisce la lunghezza massima di "titolo" a cinque. Qualsiasi riga minore o uguale a cinque ```GoodRows``` verrà inserita nel flusso. Qualsiasi riga maggiore di cinque verrà ```BadRows``` inserita nel flusso.

3. Ora dobbiamo registrare le righe che non sono riuscite. Aggiungere una trasformazione ```BadRows``` sink al flusso per la registrazione. Qui, faremo "mappe automatico" tutti i campi in modo che abbiamo la registrazione del record di transazione completo. Si tratta di un output di file CSV delimitato da testo in un singolo file nell'archivio BLOB. Chiameremo il file di registro "badrows.csv".

    ![Righe non andate male](media/data-flow/error3.png)
    
4. Il flusso di dati completato è illustrato di seguito. Ora siamo in grado di dividere le righe di errore per evitare gli errori di troncamento SQL e inserire tali voci in un file di log. Nel frattempo, le righe riuscite possono continuare a scrivere nel database di destinazione.

    ![flusso di dati completo](media/data-flow/error2.png)

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati utilizzando il mapping delle trasformazioni dei [flussi](concepts-data-flow-overview.md)di dati .
