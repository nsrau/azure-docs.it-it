---
title: Log-iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Come accedere ai log di database per database di Azure per PostgreSQL-iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099042"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Log in database di Azure per PostgreSQL-iperscalabilità (CITUS)

I log di PostgreSQL sono disponibili in ogni nodo di un gruppo di server con iperscalabilità (CITUS). È possibile spedire i log a un server di archiviazione o a un servizio di analisi. I log possono essere usati per identificare, risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali.

## <a name="accessing-logs"></a>Accesso ai log

Per accedere ai log di PostgreSQL per un coordinatore o un nodo di lavoro iperscalabile (CITUS), aprire il nodo nella portale di Azure:

![elenco di nodi](media/howto-hyperscale-logging/choose-node.png)

Per il nodo selezionato, aprire **impostazioni di diagnostica**e fare clic su **+ Aggiungi impostazione di diagnostica**.

![Pulsante Aggiungi impostazioni di diagnostica](media/howto-hyperscale-logging/diagnostic-settings.png)

Selezionare un nome per le nuove impostazioni di diagnostica e selezionare la casella **PostgreSQLLogs** .  Scegliere le destinazioni che devono ricevere i log.

![Scegliere i log PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione alle query di log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- Informazioni su [Hub eventi di Azure](/azure/event-hubs/event-hubs-about)
