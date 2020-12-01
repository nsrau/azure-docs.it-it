---
title: Gestire le righe di errore con il mapping di flussi di dati in Azure Data Factory
description: Informazioni su come gestire gli errori di troncamento SQL in Azure Data Factory usando i flussi di dati di mapping.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: c8b0ae7058aecc1813d720a3fbb2a1a1f967cf40
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352603"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Gestire le righe degli errori di troncamento SQL in Data Factory flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uno scenario comune in Data Factory quando si usa il mapping di flussi di dati consiste nel scrivere i dati trasformati in un database nel database SQL di Azure. In questo scenario, è possibile che venga troncata una condizione di errore comune che è necessario prevenire rispetto a.

Esistono due metodi principali per gestire normalmente gli errori durante la scrittura dei dati nel sink di database nei flussi di dati ADF:

* Impostare la [gestione delle righe con errori](./connector-azure-sql-database.md#error-row-handling) di sink su "continua in caso di errore" durante l'elaborazione dei dati del database. Si tratta di un metodo catch-all automatizzato che non richiede la logica personalizzata nel flusso di dati.
* In alternativa, attenersi alla procedura seguente per fornire la registrazione delle colonne che non rientrano in una colonna di stringhe di destinazione, consentendo il proseguimento del flusso di dati.

> [!NOTE]
> Quando si Abilita la gestione automatica delle righe con errori, invece del metodo riportato di seguito per la scrittura della logica di gestione degli errori, si verifica una lieve riduzione delle prestazioni e un passaggio aggiuntivo eseguito da ADF per eseguire un'operazione a 2 fasi per intercettare gli errori.

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

5. Se si sceglie l'opzione di gestione delle righe con errori nella trasformazione sink e si imposta "righe errore di output", ADF genererà automaticamente un output del file CSV dei dati della riga insieme ai messaggi di errore segnalati dal driver. Non è necessario aggiungere manualmente tale logica al flusso di dati con l'opzione alternativa. Con questa opzione si verifica una lieve riduzione delle prestazioni, in modo che ADF possa implementare una metodologia a 2 fasi per intercettare gli errori e registrarli.

    ![completare il flusso di dati con righe di errore](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.