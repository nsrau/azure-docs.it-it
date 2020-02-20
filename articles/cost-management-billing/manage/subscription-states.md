---
title: Stati della sottoscrizione di Azure
description: Descrive i diversi stati di una sottoscrizione di Azure
keywords: stato sottoscrizione di azure
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200538"
---
# <a name="azure-subscription-states"></a>Stati della sottoscrizione di Azure
Questo articolo descrive i vari stati di una sottoscrizione di Azure. Questi stati vengono visualizzati come "Stato" nei pannelli delle sottoscrizioni.

| Stato sottoscrizione | Descrizione |
|-------------| ----------------|
| **Attivo** | La sottoscrizione di Azure è attiva. È possibile usare questa sottoscrizione per distribuire nuove risorse e gestire quelle esistenti.|
| **Scaduta** | Per la sottoscrizione di Azure è presente un pagamento in sospeso. La sottoscrizione è ancora attiva, ma il mancato pagamento delle quote può causare la disabilitazione della sottoscrizione. [Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Disabilitato** | La sottoscrizione di Azure è disabilitata e non può più essere usata per creare o gestire le risorse di Azure. In questo stato, le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati, la risorsa di archiviazione diventa di sola lettura e altri servizi vengono disabilitati. La sottoscrizione può essere disabilitata perché il credito potrebbe essere scaduto, potrebbe essere stato raggiunto il limite di spesa, è scaduto un pagamento, è stato raggiunto il limite della carta di credito oppure la sottoscrizione è stata disabilitata/annullata esplicitamente. A seconda del tipo di sottoscrizione e del motivo della disabilitazione, una sottoscrizione può rimanere disabilitata da 1 a 90 giorni, periodo dopo il quale viene eliminata definitivamente. [Riattivare una sottoscrizione di Azure disabilitata.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Eliminata** | La sottoscrizione di Azure è stata eliminata insieme a tutti i dati e le risorse sottostanti. |
