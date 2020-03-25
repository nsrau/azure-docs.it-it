---
title: Stati della sottoscrizione di Azure
description: Questo articolo descrive i diversi stati di una sottoscrizione di Azure.
keywords: stato sottoscrizione di azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78270228"
---
# <a name="azure-subscription-states"></a>Stati della sottoscrizione di Azure

Questo articolo descrive i vari stati di una sottoscrizione di Azure. Questi stati vengono visualizzati come **Stato** nelle aree delle sottoscrizioni del portale di Azure.

| Stato sottoscrizione | Descrizione |
|-------------| ----------------|
| **Attivo** | La sottoscrizione di Azure è attiva. È possibile usare la sottoscrizione per distribuire nuove risorse e gestire quelle esistenti.|
| **Eliminata** | La sottoscrizione di Azure è stata eliminata insieme a tutti i dati e le risorse sottostanti. |
| **Disabilitato** | La sottoscrizione di Azure è disabilitata e non può più essere usata per creare o gestire le risorse di Azure. In questo stato, le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati, la risorsa di archiviazione diventa di sola lettura e altri servizi vengono disabilitati. È possibile che una sottoscrizione venga disabilitata per i motivi seguenti: Il credito potrebbe essere scaduto. Potrebbe essere stato raggiunto il limite di spesa. La scadenza di una fattura è stata superata. Il limite della carta di credito è stato superato. Oppure è stata disabilitata o annullata in modo esplicito. A seconda del tipo, una sottoscrizione può rimanere disabilitata per un periodo compreso tra 1 e 90 giorni. Al termine di questo periodo, verrà eliminata definitivamente. Per altre informazioni, vedere [Riattivare una sottoscrizione di Azure disabilitata](subscription-disabled.md). |
| **Scaduta** | La sottoscrizione di Azure è scaduta perché è stata annullata. È possibile riattivare una sottoscrizione scaduta. Per altre informazioni, vedere [Riattivare una sottoscrizione di Azure disabilitata](subscription-disabled.md).|
| **Scaduta** | Per la sottoscrizione di Azure è presente un pagamento in sospeso. La sottoscrizione è ancora attiva, ma il mancato pagamento delle quote può causare la disabilitazione della sottoscrizione. Per altre informazioni, vedere [Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure](resolve-past-due-balance.md). |
