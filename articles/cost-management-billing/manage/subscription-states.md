---
title: Stati della sottoscrizione di Azure
description: Questo articolo descrive i diversi stati di una sottoscrizione di Azure.
keywords: stato sottoscrizione di azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 70d03b4aeeaecee522552b381ce1309485719dc6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686684"
---
# <a name="azure-subscription-states"></a>Stati della sottoscrizione di Azure

Questo articolo descrive i vari stati di una sottoscrizione di Azure. Questi stati vengono visualizzati come **Stato** nelle aree delle sottoscrizioni del portale di Azure.

| Stato sottoscrizione | Descrizione |
|-------------| ----------------|
| **Attiva**/**Abilitata** | La sottoscrizione di Azure è attiva. È possibile usare la sottoscrizione per distribuire nuove risorse e gestire quelle esistenti.<br><br>Tutte le operazioni (PUT, PATCH, DELETE, POST, GET) sono disponibili per i provider di risorse [registrati per la sottoscrizione](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Eliminata** | La sottoscrizione di Azure è stata eliminata insieme a tutti i dati e le risorse sottostanti.<br><br>Non sono disponibili operazioni. |
| **Disabilitato** | La sottoscrizione di Azure è disabilitata e non può più essere usata per creare o gestire le risorse di Azure. In questo stato, le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati, la risorsa di archiviazione diventa di sola lettura e altri servizi vengono disabilitati. È possibile che una sottoscrizione venga disabilitata per i motivi seguenti: Il credito potrebbe essere scaduto. Potrebbe essere stato raggiunto il limite di spesa. La scadenza di una fattura è stata superata. Il limite della carta di credito è stato superato. Oppure è stata disabilitata o annullata in modo esplicito. A seconda del tipo, una sottoscrizione può rimanere disabilitata per un periodo compreso tra 1 e 90 giorni. Al termine di questo periodo, verrà eliminata definitivamente. Per altre informazioni, vedere [Riattivare una sottoscrizione di Azure disabilitata](subscription-disabled.md).<br><br>Le operazioni per la creazione o l'aggiornamento delle risorse (PUT, PATCH) sono disabilitate. Sono disabilitate anche le operazioni che accettano un'azione (POST). È possibile recuperare o eliminare risorse (GET, DELETE). Le risorse sono ancora disponibili. |
| **Scaduta** | La sottoscrizione di Azure è scaduta perché è stata annullata. È possibile riattivare una sottoscrizione scaduta. Per altre informazioni, vedere [Riattivare una sottoscrizione di Azure disabilitata](subscription-disabled.md).<br><br>Le operazioni per la creazione o l'aggiornamento delle risorse (PUT, PATCH) sono disabilitate. Sono disabilitate anche le operazioni che accettano un'azione (POST). È possibile recuperare o eliminare risorse (GET, DELETE).|
| **Scaduta** | Per la sottoscrizione di Azure è presente un pagamento in sospeso. La sottoscrizione è ancora attiva, ma il mancato pagamento delle quote può causare la disabilitazione della sottoscrizione. Per altre informazioni, vedere [Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure](resolve-past-due-balance.md).<br><br>Tutte le operazioni sono disponibili. |
| **Avvisato** | Lo stato della sottoscrizione di Azure è Avvisato. Benché sia possibile usarla normalmente, verrà disabilitata a breve se il motivo dell'avviso non viene risolto. Una sottoscrizione può avere stato Avvisato se è scaduta, è stata annullata dall'utente e così via.<br><br>Tutte le operazioni sono disponibili. |
