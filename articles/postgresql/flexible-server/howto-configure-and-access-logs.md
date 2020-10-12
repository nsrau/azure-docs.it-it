---
title: Configurare e accedere ai log-server flessibile-database di Azure per PostgreSQL
description: Come accedere ai log di database per database di Azure per PostgreSQL-server flessibile
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0b7b09696cbbe12a57d066e452b4c8ea7a7b8f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938845"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Configurare e accedere ai log nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

I log PostgreSQL sono disponibili in ogni nodo di un server flessibile. È possibile spedire i log a un server di archiviazione o a un servizio di analisi. I log possono essere usati per identificare, risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali.

## <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

È possibile abilitare le impostazioni di diagnostica per il server Postgres usando il portale di Azure, l'interfaccia della riga di comando, l'API REST e PowerShell. La categoria di log da selezionare è **PostgreSQLLogs**.

Per abilitare i log delle risorse usando il portale di Azure:

1. Nel portale passare a impostazioni di *diagnostica* nel menu di navigazione del server postgres.
   
2. Selezionare *Aggiungi impostazioni di diagnostica*.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Pulsante Aggiungi impostazioni di diagnostica":::

3. Assegnare un nome a questa impostazione. 

4. Selezionare l'endpoint preferito (account di archiviazione, Hub eventi, log Analytics). 

5. Selezionare il tipo di log **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="Pulsante Aggiungi impostazioni di diagnostica":::

7. Salvare l’impostazione.

Per abilitare i log delle risorse usando PowerShell, l'interfaccia della riga di comando o l'API REST, vedere l'articolo [impostazioni di diagnostica](../../azure-monitor/platform/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Accedere ai log delle risorse

Il modo in cui si accede ai log dipende dall'endpoint scelto. Per archiviazione di Azure, vedere l'articolo relativo all' [account di archiviazione dei log](../../azure-monitor/platform/resource-logs-collect-storage.md) . Per gli hub eventi, vedere l'articolo [flusso di log di Azure](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) .

Per i log di monitoraggio di Azure, i log vengono inviati all'area di lavoro selezionata. I log Postgres usano la modalità di raccolta **AzureDiagnostics** , in modo che possano essere sottoposti a query dalla tabella AzureDiagnostics. I campi della tabella sono descritti di seguito. Per altre informazioni sull'esecuzione di query e avvisi, vedere Panoramica delle [query dei log di monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md) .

Di seguito sono riportate le query che è possibile provare a iniziare. È possibile configurare gli avvisi in base alle query.

Cerca tutti i log Postgres per un determinato server nell'ultimo giorno

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Cerca tutti i tentativi di connessione non localhost

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

La query precedente visualizzerà i risultati nelle ultime 6 ore per ogni registrazione del server Postgres in questa area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione alle query di log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
- Informazioni su [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)
