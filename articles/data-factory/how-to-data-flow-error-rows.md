---
title: Gestire le righe di errore con il mapping di flussi di dati in Azure Data Factory
description: Informazioni su come gestire gli errori di troncamento SQL in Azure Data Factory usando i flussi di dati di mapping.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 3f8ac2d1434019548b01d8468015a543d89d0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254413"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Gestire le righe degli errori di troncamento SQL in Data Factory flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uno scenario comune in Data Factory quando si usa il mapping di flussi di dati consiste nel scrivere i dati trasformati in un database nel database SQL di Azure. In questo scenario, è possibile che venga troncata una condizione di errore comune che è necessario prevenire rispetto a. Attenersi alla seguente procedura per fornire la registrazione di colonne che non rientrano in una colonna di stringhe di destinazione, consentendo al flusso di dati di continuare in questi scenari.

## <a name="scenario"></a>Scenario

1. È presente una tabella di database di destinazione con una ```nvarchar(5)``` colonna denominata "Name".

2. All'interno del flusso di dati, si vuole eseguire il mapping dei titoli dei film dal sink alla colonna "nome" di destinazione.

    ![Flusso di dati film 1](media/data-flow/error4.png)
    
3. Il problema è che il titolo del film non rientrerà in una colonna di sink che può contenere solo 5 caratteri. Quando si esegue questo flusso di dati, verrà visualizzato un errore simile al seguente: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Questo video illustra un esempio di impostazione della logica di gestione delle righe con errori nel flusso di dati:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Come progettare intorno a questa condizione

1. In questo scenario, la lunghezza massima della colonna "Name" è di cinque caratteri. Verrà quindi aggiunta una trasformazione Suddivisione condizionale che consentirà di registrare le righe con "titoli" con una lunghezza superiore a cinque caratteri, consentendo al tempo stesso le altre righe che possono rientrare nello spazio per la scrittura nel database.

    ![suddivisione condizionale](media/data-flow/error1.png)

2. Questa trasformazione Suddivisione condizionale definisce la lunghezza massima di "title" da cinque. Qualsiasi riga minore o uguale a cinque entra nel ```GoodRows``` flusso. Tutte le righe maggiori di cinque verranno inserite nel ```BadRows``` flusso.

3. A questo punto è necessario registrare le righe non riuscite. Aggiungere una trasformazione sink al ```BadRows``` flusso per la registrazione. Qui, si eseguirà il mapping automatico di tutti i campi in modo che sia disponibile la registrazione del record completo della transazione. Si tratta di un output di file CSV con valori delimitati da testo in un singolo file nell'archivio BLOB. Chiameremo il file di log "badrows.csv".

    ![Righe non valide](media/data-flow/error3.png)
    
4. Il flusso di dati completato è riportato di seguito. È ora possibile suddividere le righe di errore per evitare gli errori di troncamento SQL e inserire le voci in un file di log. Nel frattempo, le righe con esito positivo possono continuare a scrivere nel database di destinazione.

    ![flusso di dati completo](media/data-flow/error2.png)

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.
