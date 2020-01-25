---
title: Usare analisi di flusso di Azure
description: Suggerimenti per l'uso di Analisi di flusso di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721201"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Usare analisi di flusso di Azure con Azure sinapsi Analytics
Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per informazioni di base, vedere [Introduzione ad Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md). È possibile apprendere come creare una soluzione end-to-end con Analisi di flusso seguendo l’esercitazione [Introduzione all’uso di Analisi di flusso di Azure](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

In questo articolo si apprenderà come usare il database di data warehouse come sink di output per i processi di analisi di flusso.

## <a name="prerequisites"></a>Prerequisiti
Innanzitutto, eseguire i passaggi seguenti nell’esercitazione [Introduzione all'utilizzo di Analisi di flusso di Azure](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .  

1. Creare un input dell'hub eventi
2. Configurare e avviare l'applicazione di generazione di eventi
3. Eseguire il provisioning di un processo di Analisi dei flussi
4. Specificare la query e l'input del processo

Creare quindi un database di Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Specificare l'output del processo: database di Azure SQL Warehouse
### <a name="step-1"></a>Passaggio 1
Nel processo di analisi di flusso fare clic su **output** nella parte superiore della pagina e quindi fare clic su **Aggiungi**.

### <a name="step-2"></a>Passaggio 2
Selezionare database SQL.

### <a name="step-3"></a>Passaggio 3
Immettere i valori seguenti nella pagina successiva:

* *Alias di output*: immettere un nome descrittivo per l'output del processo.
* *Sottoscrizione*
  * Se il database SQL Data Warehouse esiste nella stessa sottoscrizione del processo di analisi di flusso, selezionare Usare il database SQL dalla sottoscrizione corrente.
  * Se il database è in una sottoscrizione diversa, selezionare Usare il database SQL da un'altra sottoscrizione.
* *Database*: specificare il nome di un database di destinazione.
* *Nome server*: specificare il nome del server per il database specificato. Per trovarlo, è possibile usare il portale di Azure.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Nome utente*: digitare il nome utente di un account con autorizzazioni di scrittura nel database.
* *Password*: fornire la password per l'account utente specificato.
* *Tabella*: specificare il nome della tabella di destinazione nel database.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Passaggio 4
Fare clic sul pulsante con il segno di spunta per aggiungere l'output del processo e per verificare che Analisi di flusso possa connettersi al database.

Quando la connessione al database ha esito positivo, viene visualizzata una notifica nel portale. È possibile fare clic su test per testare la connessione al database.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica dell'integrazione, vedere [integrazione di altri servizi](sql-data-warehouse-overview-integrate.md).
Per altri suggerimenti sullo sviluppo, vedere [decisioni di progettazione e tecniche di codifica per i data warehouse](sql-data-warehouse-overview-develop.md).

