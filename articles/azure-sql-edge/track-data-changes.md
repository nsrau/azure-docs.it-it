---
title: Rilevare le modifiche ai dati in Azure SQL Edge (anteprima)
description: Informazioni sul rilevamento delle modifiche e Change Data Capture in Azure SQL Edge (anteprima).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6d0a081f2b0adb143a6b37a647a00014846f8fe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669597"
---
# <a name="track-data-changes-in-azure-sql-edge-preview"></a>Rilevare le modifiche ai dati in Azure SQL Edge (anteprima)

Azure SQL Edge supporta le due funzionalità SQL Server che consentono di tenere traccia delle modifiche ai dati in un database: [rilevamento](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) delle modifiche e [Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Queste funzionalità consentono alle applicazioni di determinare le modifiche del linguaggio di modifica dei dati (operazioni di inserimento, aggiornamento ed eliminazione) apportate alle tabelle utente in un database. È possibile abilitare Change Data Capture e il rilevamento delle modifiche nello stesso database. Non richiedono considerazioni particolari.

Un importante requisito per l'efficienza di alcune applicazioni è costituito dalla possibilità di eseguire query relative a dati che sono stati modificati in un database. In genere, per determinare le modifiche apportate ai dati, gli sviluppatori di applicazioni devono implementare un metodo di rilevamento personalizzato utilizzando una combinazione di trigger, colonne di tipo timestamp e tabelle aggiuntive. La creazione di queste applicazioni comporta in genere molte operazioni di implementazione e rende necessari aggiornamenti di schemi, provocando spesso un elevato overhead delle prestazioni.

Nel caso di una soluzione Internet, in cui è necessario spostare periodicamente i dati dal perimetro a un cloud o a un Data Center, il rilevamento delle modifiche può essere molto utile. Gli utenti possono eseguire query in modo rapido ed efficace solo sulle modifiche dall'ultima sincronizzazione e caricare le modifiche nel cloud o nel Data Center target. Per ulteriori informazioni, vedere la pagina relativa ai [vantaggi dell'utilizzo di Change Data Capture o del rilevamento delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Queste due funzionalità non sono uguali. Per ulteriori informazioni, vedere [differenze tra le funzionalità tra Change Data Capture e rilevamento delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Change Data Capture

Per comprendere i dettagli sul funzionamento di questa funzionalità, vedere [informazioni su Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Per informazioni su come abilitare o disabilitare questa funzionalità, vedere [abilitare e disabilitare Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Per amministrare e monitorare questa funzionalità, vedere [amministrazione e monitoraggio Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Per informazioni su come eseguire una query e usare i dati modificati, vedere [usare i dati delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server).

## <a name="change-tracking"></a>Change tracking

Per comprendere i dettagli sul funzionamento di questa funzionalità, vedere [informazioni sul rilevamento delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Per informazioni su come abilitare o disabilitare questa funzionalità, vedere [abilitare e disabilitare il rilevamento delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Per amministrare, monitorare e gestire questa funzionalità, vedere [amministrazione e](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)monitoraggio del rilevamento delle modifiche.

Per informazioni su come eseguire una query e usare i dati modificati, vedere [usare i dati delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Tabelle temporali

Azure SQL Edge supporta anche la funzionalità tabelle temporali di SQL Server. Questa funzionalità, nota anche come *tabelle temporali con controllo delle versioni di sistema*, offre il supporto incorporato per fornire informazioni sui dati archiviati nella tabella in qualsiasi momento. La funzionalità non fornisce semplicemente informazioni solo sui dati che risultano corretti nel momento attuale.

Una tabella temporale con controllo delle versioni di sistema è un tipo di tabella utente progettato per garantire una cronologia completa delle modifiche dei dati e per consentire un'analisi temporizzata semplice. Questo tipo di tabella temporale è definito tabella temporale con controllo delle versioni di sistema perché il periodo di validità per ogni riga viene gestito dal sistema, ovvero dal motore di database.

Ogni tabella temporale ha due colonne definite in modo esplicito, ciascuna con un `datetime2` tipo di dati. Queste colonne sono definite colonne *periodo* . Il sistema usa esclusivamente queste colonne periodo per registrare il periodo di validità di ogni riga ogni volta che viene modificata una riga.

Oltre alle colonne periodo, una tabella temporale contiene anche un riferimento a un'altra tabella con schema con mirroring. Il sistema usa questa tabella per archiviare automaticamente la versione precedente della riga ogni volta che una riga della tabella temporale viene aggiornata o eliminata. Questa tabella aggiuntiva è detta tabella di *cronologia* , mentre la tabella principale che archivia le versioni di riga correnti (effettive) viene definita tabella *corrente* o semplicemente come tabella temporale. Durante la creazione della tabella temporale, gli utenti possono specificare una tabella di cronologia esistente (deve essere conforme allo schema) oppure consentire al sistema di creare la tabella di cronologia predefinita.

Per altre informazioni, vedere [tabelle temporali](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).

## <a name="next-steps"></a>Passaggi successivi

- [Streaming dei dati in Azure SQL Edge (anteprima)](stream-data.md)
- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge di Azure (anteprima) ](onnx-overview.md)
- [Configurare la replica in Azure SQL Edge (anteprima)](configure-replication.md)
- [Eseguire il backup e il ripristino di database in Azure SQL Edge (anteprima)](backup-restore.md)



