---
title: Usare il dashboard per un lab della classe in Azure Lab Services. Documenti Microsoft
description: Informazioni su come usare il dashboard per un lab della classe in Azure Lab Services.Learn how to use dashboard for a classroom lab in Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538941"
---
# <a name="dashboard-for-classroom-labs"></a>Dashboard per i laboratori in aula
Questo articolo descrive la visualizzazione dashboard di un lab della classe in Azure Lab Services.This article describes the dashboard view of a classroom lab in Azure Lab Services. 

![Dashboard](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Riquadro Costi e fatturazione
Questo riquadro fornisce i seguenti dettagli di stima dei costi:

| Impostazione | valore | 
| ------- | ----- | 
| Ore di quota | Numero massimo di ore in cui un utente può usare la macchina virtuale al di fuori delle ore pianificate. |
| Ore pianificate | Ore che verranno sostenute in base alla pianificazione impostata nel lab. Questo valore è disponibile solo se è presente un da/per data impostato su tutti gli eventi di pianificazione. |
| Ore/utente | Somma delle ore di quota e delle ore pianificate. |
| Numero massimo di utenti | Numero massimo di utenti nel lab in base a tutte le macchine virtuali da rivendicare. |
| Ore x utenti | Ore/utente moltiplicato per il numero di utenti. |
| Quota rettificata | Somma delle ore di quota aggiunte a utenti specifici. |
| Ore totali : /ora | Il costo orario in base alle dimensioni della macchina virtuale selezionata. Questo si basa sulla paga regolare come si va prezzo. |
| Costo totale stimato | Questo è il prezzo massimo per questo lab in base alle impostazioni correnti. |

## <a name="template-tile"></a>Riquadro del modello
In questo riquadro vengono visualizzate le informazioni seguenti:You see the following information on this tile:

- Data di creazione del modello 
- Data dell'ultima pubblicazione del modello 

Ha anche un collegamento per passare alla pagina **modello** in cui è possibile [gestire la macchina virtuale del modello](how-to-create-manage-template.md) per la classe. 

## <a name="virtual-machine-pool-tile"></a>Riquadro del pool di macchine virtualiVirtual machine Pool tile

In questo riquadro vengono visualizzate le informazioni seguenti:You see the following information on this tile:

- Numero di macchine virtuali assegnate agli studenti (utenti)
- Numero di macchine virtuali non ancora assegnate agli studenti

Include inoltre un collegamento per passare alla pagina **Pool di macchine virtuali** in cui è possibile gestire il pool di macchine [virtuali](how-to-set-virtual-machine-passwords.md) nel lab. 

## <a name="users-tile"></a>Riquadro Utenti

In questo riquadro vengono visualizzate le informazioni seguenti:You see the following information on this tile:

- Numero di utenti registrati alla classe
- Numero di utenti aggiunti al lab ma non registrati nella classe 

Dispone inoltre di un collegamento per passare alla pagina **Utenti** in cui è possibile [gestire gli utenti](how-to-configure-student-usage.md) per il lab. 

## <a name="schedules-tile"></a>Riquadro Pianificazioni
Vengono visualizzati gli eventi pianificati correnti per il lab nel riquadro. Ha anche un link per passare alla pagina **Pianificazione** in cui è possibile [creare e gestire le pianificazioni.](how-to-create-schedules.md) Il riquadro mostra i dettagli solo per due eventi pianificati e il numero di eventi pianificati rimanenti per il lab. 

![Eventi pianificati](../media/use-dashboard/scheduled-events.png)

