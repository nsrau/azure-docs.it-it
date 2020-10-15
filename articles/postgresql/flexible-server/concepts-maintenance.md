---
title: Manutenzione pianificata-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive la funzionalità di manutenzione pianificata nel database di Azure per PostgreSQL-server flessibile.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336310"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Manutenzione pianificata in Database di Azure per PostgreSQL - Server flessibile
 
Database di Azure per PostgreSQL: il server flessibile esegue una manutenzione periodica per mantenere il database gestito sicuro, stabile e aggiornato. Durante la manutenzione, il server ottiene nuove funzionalità, aggiornamenti e patch.
 
> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è in anteprima
 
## <a name="selecting-a-maintenance-window"></a>Selezione di una finestra di manutenzione
 
È possibile pianificare la manutenzione in un determinato giorno della settimana e in un intervallo di tempo in tale giorno. In alternativa, è possibile consentire al sistema di selezionare automaticamente un giorno e un'ora di intervallo di tempo. In entrambi i casi, il sistema segnalerà cinque giorni prima di eseguire qualsiasi operazione di manutenzione. Il sistema consente inoltre di stabilire quando viene avviata la manutenzione e quando viene completata correttamente.
 
Le notifiche relative alla manutenzione pianificata imminente possono essere:
 
* Inviato tramite posta elettronica a un indirizzo specifico
* Inviato tramite posta elettronica a un ruolo Azure Resource Manager
* Inviato in un messaggio di testo (SMS) ai dispositivi mobili
* Push come notifica a un'app di Azure
* Recapitato come messaggio vocale
 
Quando si specificano le preferenze per il programma di manutenzione, è possibile scegliere un giorno della settimana e un intervallo di tempo. Se non lo si fa, il sistema sceglierà un'ora tra le 23:00 e le 07:00 nell'ora dell'area del server. È possibile definire pianificazioni diverse per ogni server flessibile nella sottoscrizione di Azure. 
 
> [!IMPORTANT]
> Normalmente, per un server, trascorrono almeno 30 giorni tra un evento di manutenzione pianificata eseguito correttamente e l'altro.
>
> Tuttavia, nel caso di un aggiornamento critico di emergenza, ad esempio una vulnerabilità grave, la finestra di notifica potrebbe essere inferiore a cinque giorni. È possibile applicare l'aggiornamento critico al server anche se è stata eseguita correttamente una manutenzione pianificata negli ultimi 30 giorni.

È possibile aggiornare le impostazioni di pianificazione in qualsiasi momento. Se è stata pianificata una manutenzione per il server flessibile e si aggiornano le preferenze di pianificazione, l'evento corrente continuerà come pianificato e la modifica delle impostazioni di pianificazione diventerà effettiva al completamento del processo. 

Se l'evento di manutenzione viene annullato dal sistema o non viene completato correttamente, il sistema creerà rispettivamente una notifica relativa all'evento di manutenzione annullato o non riuscito. Il tentativo successivo di eseguire la manutenzione verrà pianificato in base alle impostazioni di pianificazione correnti e si riceverà una notifica per cinque giorni prima.
 
## <a name="next-steps"></a>Passaggi successivi
 
* Informazioni su come [modificare la pianificazione di manutenzione](how-to-maintenance-portal.md)
* Informazioni su come [ricevere notifiche sulla manutenzione futura](../../service-health/service-notifications.md) con l'integrità dei servizi di Azure
* Informazioni su come [configurare gli avvisi relativi agli eventi di manutenzione pianificata imminente](../../service-health/resource-health-alert-monitor-guide.md)
