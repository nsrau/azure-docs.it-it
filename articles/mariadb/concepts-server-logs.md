---
title: Log del server per Database di Azure per MariaDB
description: Descrive i log disponibili nel Database di Azure per MariaDB e i parametri disponibili per l'abilitazione di diversi livelli di registrazione.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a26f61eb199d8f370e1a9dd010932dc868b74ae4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041259"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Log del server in Database di Azure per MariaDB
Nel Database di Azure per MariaDB, il log delle query lente è disponibile per gli utenti. L'accesso al log delle transazioni non è supportato. Il log delle query lente può essere usato per identificare eventuali colli di bottiglia delle prestazioni e procedere alla risoluzione dei problemi.

Per altre informazioni sul log delle query lente, vedere la documentazione MariaDB per il [log delle query lente](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Accesso ai log server
È possibile elencare e scaricare i log del server del Database di Azure per MariaDB usando il portale di Azure e l'interfaccia della riga di comando di Azure.

Nel portale di Azure, selezionare il server Database di Azure per MariaDB. Nell'intestazione **Monitoraggio** selezionare la pagina **Log del server**.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>Conservazione dei log
I log sono disponibili per un massimo di sette giorni dalla data di creazione. Se le dimensioni totali dei log disponibili superano 7 GB, i file meno recenti vengono eliminati fino a quando non è disponibile dello spazio.

I log vengono ruotati ogni 24 ore o 7 GB, a seconda del valore raggiunto per primo.

## <a name="configure-logging"></a>Configurare la registrazione
Per impostazione predefinita il log delle query lente è disabilitato. Per abilitarlo, impostare slow_query_log su ON.

Altri parametri che è possibile modificare includono:

- **long_query_time**: se una query richiede più tempo del valore di long_query_time (in secondi), la query viene registrata. Il valore predefinito è 10 secondi.
- **log_slow_admin_statements**: se è ON include le istruzioni a livello amministrativo come ALTER_TABLE e ANALYZE_TABLE nelle istruzioni scritte in slow_query_log.
- **log_queries_not_using_indexes**: determina se le query che non usano gli indici vengono registrate in slow_query_log
- **log_throttle_queries_not_using_indexes**: Questo parametro limita il numero di query non di indice che possono essere scritte nel log di query lente. Questo parametro ha effetto quando log_queries_not_using_indexes è impostato su ON.

Vedere [documentazione riguardante il log delle query lente](https://mariadb.com/kb/en/library/slow-query-log-overview/) di MariaDB per una descrizione completa dei parametri del log delle query lente.

## <a name="next-steps"></a>Fasi successive
- [Come configurare e accedere ai log del server usando il portale di Azure](howto-configure-server-logs-portal.md).
