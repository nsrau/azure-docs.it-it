---
title: Identificazione di database e tabelle per l&quot;estensione database - Azure | Documentazione Microsoft
description: Informazioni su come identificare database e tabelle candidati per Database Estensione.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: 0f1a7feea79c73d7b80343fcc9898509268d111c


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificare database e tabelle per l'estensione database eseguendo Gestione estensione database
Per identificare i database e le tabelle per l'estensione database, scaricare Gestione spazio aggiornamenti di SQL Server 2016 ed eseguire Gestione estensione database. L'ottimizzazione guidata Estensione database consente anche di identificare i problemi di blocco.

## <a name="download-and-install-upgrade-advisor"></a>Scaricare e installare Preparazione aggiornamento
È possibile scaricare e installare Gestione spazio aggiornamenti da [qui](http://go.microsoft.com/fwlink/?LinkID=613421). Questo strumento non è incluso nei supporti di installazione di SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Eseguire l'ottimizzazione guidata Database Estensione
1. Eseguire Preparazione aggiornamento.
2. Selezionare **Scenari**, quindi **RUN STRETCH DATABASE ADVISOR** (ESECUI STRETCH DATABASE ADVISOR).
3. Nel pannello **Run Stretch Database Advisor** (Esegui Stretch Database Advisor) fare clic su **SELECT DATABASES TO ANALYZE** (SELEZIONA DATABASE DA ANALIZZARE).
4. Nel pannello **Seleziona database** immettere o selezionare il nome del server e le informazioni di autenticazione. Fare clic su **Connect**.
5. Viene visualizzato un elenco dei database nel server selezionato. Selezionare i database che si desidera analizzare. Fare clic su **Seleziona**.
6. Nel pannello **Run Stretch Database Advisor** (Esegui Stretch Database Advisor) fare clic su **Esegui**.  Viene avviata l'analisi.

## <a name="review-the-results"></a>Esaminare i risultati
1. Al termine dell'analisi, nel pannello **Analyzed databases** (Database analizzati) selezionare uno dei database selezionati per visualizzare il pannello **Analysis results** (Risultati analisi).

   Il pannello **Risultati analisi** elenca le tabelle consigliate nel database selezionato che soddisfano i criteri di raccomandazione predefiniti.
2. Nell'elenco delle tabelle presente nel pannello **Analysis results** (Risultati analisi) selezionare una delle tabelle consigliate per visualizzare il pannello **Risultati tabella**.

   Eventuali problemi di blocco della tabella selezionata sono elencati nel pannello **Risultati tabella** . Per informazioni sui problemi di blocco rilevati dall'ottimizzazione guidata dell'estensione database, vedere [Limitazioni di Estensione database](sql-server-stretch-database-limitations.md).
3. Nell'elenco dei problemi di blocco del pannello **Risultati tabella** selezionare uno dei problemi per visualizzare ulteriori informazioni sul problema selezionato e alcune procedure di prevenzione consigliate. Implementare i passaggi suggeriti per configurare la tabella selezionata per Database Estensione.

## <a name="next-step"></a>Passaggio successivo
Abilitare Database Estensione.

* Per abilitare Database Estensione in un **database**, vedere [Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md).
* Per abilitare Estensione database in un'altra **tabella**quando l'estensione è già abilitata nel database, vedere [Abilitare Database Estensione per una tabella](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Vedere anche
[Limitazioni di Estensione database](sql-server-stretch-database-limitations.md)

[Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md)

[Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md)



<!--HONumber=Jan17_HO4-->


