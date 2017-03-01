---
title: Database SQL di Azure - Controllo | Documentazione Microsoft
description: Il controllo del database SQL di Azure tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell&quot;account di archiviazione di Azure dell&quot;utente.
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>Nozioni di base sul controllo del database SQL
Il controllo del database SQL di Azure tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di archiviazione di Azure dell'utente.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Il controllo abilita e facilita il rispetto degli standard di conformità, ma non garantisce la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Panoramica del controllo del database SQL di Azure]
* [Configurare il controllo per il database]
* [Analizzare i log di controllo e i report]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Panoramica del controllo del database SQL di Azure
Il controllo del database SQL consente di:

* **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
* **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
* **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

Esistono due **metodi di controllo**:

* **Controllo BLOB**: i log vengono scritti nell'Archiviazione BLOB di Azure. Questo è un metodo di controllo più recente che fornisce prestazioni più elevate, supporta una maggiore granularità del controllo a livello di oggetto ed è più conveniente.
* **Controllo Tabella**: i log vengono scritti nell'archiviazione tabelle di Azure.

> [!IMPORTANT]
> L'introduzione del nuovo controllo BLOB offre un cambiamento radicale nel modo in cui i criteri di controllo del server vengono ereditati dal database. 

È possibile configurare il controllo per diversi tipi di categorie di eventi.

* Per configurare e gestire il controllo tramite il portale di Azure, vedere [Eseguire il controllo nel portale di Azure](sql-database-auditing-portal.md).
* Per configurare e gestire il controllo tramite PowerShell, vedere [Eseguire il controllo con PowerShell](sql-database-auditing-powershell.md).
* Per configurare e gestire il controllo tramite l'API REST, vedere [Eseguire il controllo con l'API REST](sql-database-auditing-rest.md).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Un criterio di controllo può essere definito per un database specifico o come criterio server predefinito. Un criterio di controllo del server predefinito si applica a tutti i database esistenti e ai nuovi database creati in un server.

## <a name="blob-auditing"></a>Controllo BLOB

Se abilitato, il controllo BLOB del server si applica sempre al database, ovvero il controllo viene eseguito su tutti i database del server a prescindere dai fattori seguenti:
    - Le impostazioni di controllo del database.
    - Casella di controllo "Eredita impostazioni dal server" selezionata o deselezionata nel pannello del database.

> [!IMPORTANT]
> L'abilitazione del controllo BLOB nel database in aggiunta all'abilitazione nel server **non** sovrascrive o modifica le impostazioni del controllo BLOB del server: i due controlli coesisteranno. In altre parole, il database verrà controllato due volte in parallelo, una volta con i criteri del server e una volta con i criteri del database.

È consigliabile evitare di abilitare contemporaneamente il controllo BLOB del server e il controllo BLOB del database ad eccezione dei casi seguenti:
 * È necessario usare un *account di archiviazione* o *periodo di conservazione* diverso per un database specifico.
 * Per un database specifico, si vogliono controllare tipi o categorie di eventi diversi da quelli controllati per gli altri database nel server, ad esempio se è necessario controllare gli inserimenti di tabella solo per un database specifico.

In caso contrario è consigliabile abilitare solo il controllo BLOB a livello di server e lasciare il controllo a livello di database disabilitato per tutti i database.

## <a name="table-auditing"></a>Controllo Tabella

Se abilitato, il controllo tabelle a livello di server si applica al database solo se è selezionata la casella di controllo "Eredita le impostazioni dal server" nel pannello del database. La casella di controllo è selezionata per impostazione predefinita per i database nuovi ed esistenti.

- Se la casella di controllo è *selezionata*, tutte le modifiche apportate alle impostazioni di controllo nel database **sostituiranno** le impostazioni del server per il database.

- Se la casella di controllo è *deselezionata* e il controllo a livello di database è disabilitato, il database non verrà controllato.

## <a name="analyze-audit-logs-and-reports"></a>Analizzare i log di controllo e i report
I log di controllo vengono aggregati nell'account di archiviazione di Azure selezionato durante la configurazione.

È possibile esplorare i log di controllo con uno strumento come [Esplora archivi di Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Passaggi successivi

* Per configurare e gestire il controllo tramite il portale di Azure, vedere [Configurare il controllo nel portale di Azure](sql-database-auditing-portal.md).
* Per configurare e gestire il controllo tramite PowerShell, vedere [Configurare il controllo con PowerShell](sql-database-auditing-powershell.md).
* Per configurare e gestire il controllo tramite l'API REST, vedere [Configurare il controllo con l'API REST](sql-database-auditing-rest.md).


<!--Anchors-->
[Panoramica del controllo del database SQL di Azure]: #subheading-1
[Configurare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e i report]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

