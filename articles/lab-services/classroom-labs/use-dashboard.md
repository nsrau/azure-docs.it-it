---
title: Usare il dashboard per un Lab della classe in Azure Lab Services | Microsoft Docs
description: Informazioni su come usare il dashboard per un Lab della classe in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d1e34a493b747383ce479bcad638098b41e59d2b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588187"
---
# <a name="dashboard-for-classroom-labs"></a>Dashboard per laboratori in aula
Questo articolo descrive la visualizzazione dashboard di un Lab della classe in Azure Lab Services. 

![Dashboard](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Riquadro costi e fatturazione
Questo riquadro fornisce i dettagli di stima dei costi seguenti:

| Impostazione | Valore | 
| ------- | ----- | 
| Ore quota | Numero massimo di ore in cui un utente può usare la VM al di fuori delle ore pianificate. |
| Ore pianificate | Ore che verranno ricorrete in base alla pianificazione impostata nel Lab. Questo valore è disponibile solo se è presente una data da/a impostata su tutti gli eventi di pianificazione. |
| Ore/utente | Somma delle ore di quota e delle ore pianificate. |
| Numero massimo utenti | Numero massimo di utenti nel Lab in base a tutte le macchine virtuali da richiedere. |
| Ore x utenti | Ore/utente moltiplicato per il numero di utenti. |
| Quota regolata | Somma delle ore di quota aggiunte a utenti specifici. |
| Ore totali * $/ora | Costo per ora in base alle dimensioni della macchina virtuale selezionata. Questo è basato sul prezzo normale con pagamento in base al consumo. |
| Costo stimato totale | Questo è il prezzo massimo per questo Lab in base alle impostazioni correnti. |

## <a name="template-tile"></a>Riquadro modello
In questo riquadro vengono visualizzate le informazioni seguenti:

- Data di creazione del modello 
- Data dell'ultima pubblicazione del modello 

Contiene anche un collegamento per passare alla pagina del **modello** in cui è possibile [gestire la macchina virtuale modello](how-to-create-manage-template.md) per la classe. 

## <a name="virtual-machine-pool-tile"></a>Riquadro pool di macchine virtuali

In questo riquadro vengono visualizzate le informazioni seguenti:

- Numero di macchine virtuali assegnate a studenti (utenti)
- Numero di macchine virtuali che non sono ancora state assegnate agli studenti

Include anche un collegamento per passare alla pagina del **pool** di macchine virtuali in cui è possibile [gestire il pool di macchine virtuali](how-to-set-virtual-machine-passwords.md) nel Lab. 

## <a name="users-tile"></a>Riquadro utenti

In questo riquadro vengono visualizzate le informazioni seguenti:

- Numero di utenti registrati alla classe
- Numero di utenti aggiunti al Lab ma non registrati alla classe 

Include anche un collegamento per passare alla pagina **utenti** in cui è possibile [gestire gli utenti](how-to-configure-student-usage.md) per il Lab. 

## <a name="schedules-tile"></a>Riquadro pianificazioni
Gli eventi pianificati correnti per il Lab sono visualizzati nel riquadro. Include anche un collegamento per passare alla pagina di **pianificazione** in cui è possibile [creare e gestire le pianificazioni](how-to-create-schedules.md). Il riquadro Mostra i dettagli per solo due eventi pianificati e il numero di eventi pianificati rimanenti per il Lab. 

![Eventi pianificati](../media/use-dashboard/scheduled-events.png)

