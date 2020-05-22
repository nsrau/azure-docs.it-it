---
title: Rilevamento delle modifiche ai dati in SQL Edge di Azure (anteprima)
description: Informazioni sulle funzionalità Rilevamento modifiche e Change Data Capture in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594450"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Rilevamento delle modifiche ai dati in SQL Edge di Azure (anteprima)

SQL Edge di Azure supporta le due funzionalità di SQL Server che consentono di tenere traccia delle modifiche ai dati in un database: [Rilevamento modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) e [Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Tali funzionalità consentono alle applicazioni di determinare le modifiche DML (operazioni di inserimento, aggiornamento ed eliminazione) apportate alle tabelle utente in un database. Change Data Capture e Rilevamento modifiche possono essere abilitati sullo stesso database, non sono richieste considerazioni speciali.

Un importante requisito per l'efficienza di alcune applicazioni è costituito dalla possibilità di eseguire query relative a dati che sono stati modificati in un database. In genere, per determinare le modifiche apportate ai dati, gli sviluppatori di applicazioni devono implementare un metodo di rilevamento personalizzato utilizzando una combinazione di trigger, colonne di tipo timestamp e tabelle aggiuntive. La creazione di queste applicazioni comporta in genere molte operazioni di implementazione e rende necessari aggiornamenti di schemi, provocando spesso un elevato overhead delle prestazioni. Nel caso di una soluzione IoT, in cui è necessario spostare periodicamente i dati dal perimetro a un cloud o data center, il rilevamento delle modifiche può essere molto utile. Ciò consente di eseguire query in modo rapido ed efficace solo sulle modifiche delta dall'ultima sincronizzazione e di caricare tali modifiche nel cloud o nella destinazione nel data center. Per ulteriori dettagli sui vantaggi derivanti dall'uso di Rilevamento modifiche e Change Data Capture, vedere [Vantaggi dall'uso di Change Data Capture o Rilevamento modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Per comprendere le differenze di funzionalità tra Rilevamento modifiche e Change Data Capture, vedere [Differenze tra le funzionalità di Change Data Capture e Rilevamento modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Change Data Capture

Per informazioni dettagliate sul funzionamento di Change Data Capture, vedere [Informazioni su Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Per informazioni su come abilitare o disabilitare Change Data Capture, vedere [Abilitare e disabilitare Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

Per amministrare e monitorare Change Data Capture, vedere [Amministrare e monitorare Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Per informazioni su come eseguire query sui dati modificati e usarli, vedere [Usare i dati delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>Rilevamento modifiche

Per informazioni dettagliate sul funzionamento di Rilevamento modifiche, vedere [Informazioni su Rilevamento modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Per informazioni su come abilitare o disabilitare Rilevamento modifiche, vedere [Abilitare e disabilitare Rilevamento modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Per amministrare, monitorare e gestire Rilevamento modifiche, vedere [Amministrare e monitorare Rilevamento modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Per informazioni su come eseguire query sui dati modificati e usarli, vedere [Usare i dati delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>Tabelle temporali

Oltre a supportare le funzionalità Rilevamento modifiche e Change Data Capture di SQL Server, SQL Edge di Azure supporta anche la funzionalità Tabelle temporali di SQL Server. Le tabelle temporali (note anche come tabelle temporali con controllo delle versioni del sistema) sono una funzionalità di database che offre un supporto predefinito per la gestione delle informazioni sui dati archiviati nella tabella in qualsiasi momento anziché solo sui dati che risultano corretti nel momento attuale.

Una tabella temporale con controllo delle versioni di sistema è un tipo di tabella utente progettato per mantenere una cronologia completa delle modifiche dei dati e semplificare l'analisi temporizzata. Questo tipo di tabella temporale è nota come tabella temporale con controllo delle versioni del sistema perché il periodo di validità per ogni riga viene gestito dal sistema, ovvero il motore di database.

Ogni tabella temporale ha due colonne definite in modo esplicito, ciascuna con un tipo di dati datetime2. Queste colonne sono note come colonne periodo. Le colonne periodo vengono usate esclusivamente dal sistema per registrare il periodo di validità per ciascuna riga ogni volta che una riga viene modificata.

Oltre alle colonne periodo, una tabella temporale contiene anche un riferimento a un'altra tabella con schema con mirroring. Il sistema usa questa tabella per archiviare automaticamente la versione precedente della riga ogni volta che una riga della tabella temporale viene aggiornata o eliminata. Questa tabella aggiuntiva è detta tabella di cronologia, mentre la tabella principale che contiene le versioni attuali (effettive) delle righe è definita tabella corrente o semplicemente tabella temporale. Durante la creazione di una tabella temporale gli utenti possono specificare una tabella di cronologia esistente (deve essere conforme allo schema) oppure consentire al sistema di creare una tabella di cronologia predefinita.

Per altre informazioni sulle tabelle temporali, vedere [Tabelle temporali](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)

## <a name="see-also"></a>Vedere anche

- [Streaming dei dati in SQL Edge di Azure (anteprima) ](stream-data.md)
- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge di Azure (anteprima) ](onnx-overview.md)
- [Configurare la replica in SQL Edge di Azure (anteprima) ](configure-replication.md)
- [Eseguire il backup e il ripristino di database in SQL Edge di Azure (anteprima)](backup-restore.md)



