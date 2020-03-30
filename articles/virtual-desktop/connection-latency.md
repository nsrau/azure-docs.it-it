---
title: Latenza connessione utente Desktop virtuale Windows - Azure
description: Latenza di connessione per gli utenti di Desktop virtuale di Windows.Connection latency for Windows Virtual Desktop users.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128183"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determinare la latenza della connessione utente in Desktop virtuale di Windows

Windows Virtual Desktop è disponibile a livello globale. Gli amministratori possono creare macchine virtuali (VM) in qualsiasi area di Azure desiderata. La latenza di connessione varia a seconda della posizione degli utenti e delle macchine virtuali. I servizi Desktop virtuale di Windows verranno continuamente implementati in nuove aree geografiche per migliorare la latenza. 
 
Lo [strumento Di stimeor di Esperienza desktop virtuale di Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) consente di determinare la posizione migliore per ottimizzare la latenza delle macchine virtuali. Si consiglia di utilizzare lo strumento ogni due o tre mesi per assicurarsi che la posizione ottimale non sia stata modificata man mano che Windows Virtual Desktop viene implementato in nuove aree. 

## <a name="azure-traffic-manager"></a>Gestione traffico di Azure

Windows Virtual Desktop usa Gestione traffico di Azure, che controlla la posizione del server DNS dell'utente per trovare l'istanza del servizio Desktop virtuale Windows più vicina. È consigliabile che gli amministratori esaminino la posizione del server DNS dell'utente prima di scegliere il percorso per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

- Per verificare la posizione migliore per la latenza ottimale, vedere [lo strumento Disstima](https://azure.microsoft.com/services/virtual-desktop/assessment/)di Windows Virtual Desktop Experience .
- Per i piani tariffari, vedere Prezzi di [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Per iniziare con la distribuzione di Windows Virtual Desktop, consulta [il nostro tutorial](tenant-setup-azure-active-directory.md).