---
title: Database di Azure per PostgreSQL-server flessibile (anteprima)-manutenzione pianificata-portale di Azure
description: Informazioni su come configurare le impostazioni di manutenzione pianificata per un server di database di Azure per PostgreSQL-flessibile dal portale di Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: be6040b8b84a4b86746d62bd2f1c07f0ffea0a3b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336293"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>Gestire le impostazioni di manutenzione pianificata per database di Azure per PostgreSQL-server flessibile
 
È possibile specificare le opzioni di manutenzione per ogni server flessibile nella sottoscrizione di Azure. Le opzioni includono la pianificazione di manutenzione e le impostazioni di notifica per gli eventi di manutenzione imminenti e completati.

> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è in versione di anteprima.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per PostgreSQL-server flessibile](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Specificare le opzioni di pianificazione di manutenzione
 
1. Nella pagina Server PostgreSQL, sotto l'intestazione **Impostazioni** , scegliere **manutenzione** per aprire le opzioni di manutenzione pianificata.
2. La pianificazione predefinita (gestita dal sistema) è un giorno casuale della settimana e la finestra di 60 minuti per la manutenzione inizia tra le 23 e le 07:00 ora locale del server. Se si desidera personalizzare la pianificazione, scegliere **pianificazione personalizzata**. È quindi possibile selezionare un giorno della settimana preferito e una finestra di 60 minuti per l'ora di inizio della manutenzione.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Notifiche relative agli eventi di manutenzione pianificata
 
È possibile usare integrità dei servizi di Azure per [visualizzare le notifiche](../../service-health/service-notifications.md) relative alla manutenzione pianificata imminente e eseguita sul server flessibile. È anche possibile [configurare](../../service-health/resource-health-alert-monitor-guide.md) gli avvisi in integrità dei servizi di Azure per ricevere notifiche sugli eventi di manutenzione.
 
## <a name="next-steps"></a>Passaggi successivi  
 
* Informazioni sulla [manutenzione pianificata nel database di Azure per PostgreSQL-server flessibile](concepts-maintenance.md)
* Informazioni sull' [integrità dei servizi di Azure](../../service-health/overview.md)
