---
title: Database di Azure per MySQL-server flessibile (anteprima)-manutenzione pianificata-portale di Azure
description: Informazioni su come configurare le impostazioni di manutenzione pianificata per un server di database di Azure per MySQL-flexible dal portale di Azure.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: c8251eb2a89a7481ebc981f2b89668c363651b39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315015"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Gestire le impostazioni di manutenzione pianificata per database di Azure per MySQL-server flessibile
 
È possibile specificare le opzioni di manutenzione per ogni server flessibile nella sottoscrizione di Azure. Le opzioni includono la pianificazione di manutenzione e le impostazioni di notifica per gli eventi di manutenzione imminenti e completati.

> [!IMPORTANT]
> Database di Azure per MySQL: il server flessibile è in versione di anteprima.
 
## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per MySQL-server flessibile](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Specificare le opzioni di pianificazione di manutenzione
 
1. Nella pagina server MySQL, sotto l'intestazione **Impostazioni** , scegliere **manutenzione** per aprire le opzioni di manutenzione pianificata.
2. La pianificazione predefinita (gestita dal sistema) è un giorno casuale della settimana e la finestra di 60 minuti per la manutenzione inizia tra le 23 e le 07:00 ora locale del server. Se si desidera personalizzare la pianificazione, scegliere **pianificazione personalizzata**. È quindi possibile selezionare un giorno della settimana preferito e una finestra di 60 minuti per l'ora di inizio della manutenzione.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Notifiche relative agli eventi di manutenzione pianificata
 
È possibile usare integrità dei servizi di Azure per [visualizzare le notifiche](../../service-health/service-notifications.md) relative alla manutenzione pianificata imminente e eseguita sul server flessibile. È anche possibile [configurare](../../service-health/resource-health-alert-monitor-guide.md) gli avvisi in integrità dei servizi di Azure per ricevere notifiche sugli eventi di manutenzione.
 
## <a name="next-steps"></a>Passaggi successivi  
 
* Informazioni sulla [manutenzione pianificata in database di Azure per MySQL-server flessibile](concepts-maintenance.md)
* Informazioni sull' [integrità dei servizi di Azure](../../service-health/overview.md)
