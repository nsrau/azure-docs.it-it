---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566292"
---
Questo articolo presuppone quanto segue:

1. È già stata stabilita una connessione **VPN da sito a sito** o **ExpressRoute** tra la rete locale e la rete virtuale di Azure.
2. L'account utente ha le autorizzazioni necessarie per creare una nuova macchina virtuale nella sottoscrizione di Azure in cui è stato eseguito il failover delle macchine virtuali.
3. La sottoscrizione ha un minimo di 8 core disponibili per creare una nuova macchina virtuale del server di elaborazione.
4. È disponibile la **passphrase del server di configurazione**.

> [!TIP]
> Assicurarsi che sia possibile connettere la porta 443 del server di configurazione, in esecuzione in locale, dalla rete virtuale di Azure in cui è stato eseguito il failover delle macchine virtuali.
