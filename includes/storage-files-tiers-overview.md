---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 034f5c487750600910cb32f73bcc6ea243b3ec6f
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563445"
---
File di Azure offre quattro diversi livelli di archiviazione, ovvero Premium, Ottimizzato per le transazioni, Accesso frequente e Accesso sporadico, che consentono di adattare le condivisioni ai requisiti dello scenario in termini di prestazioni e prezzi:

- **Premium**: le condivisioni file Premium si basano su unità SSD e vengono distribuite nel tipo di account di archiviazione **Archiviazione file**. Le condivisioni file Premium offrono prestazioni elevate omogenee e a bassa latenza, in millisecondi a cifra singola per la maggior parte delle operazioni di I/O, per i carichi di lavoro con I/O elevato. Le condivisioni file Premium sono adatte per una vasta gamma di carichi di lavoro, ad esempio database, hosting di siti Web e ambienti di sviluppo. Le condivisioni file Premium possono essere usate con entrambi i protocolli SMB (Server Message Block) e NFS (Network File System).
- **Ottimizzato per le transazioni**: le condivisioni file ottimizzate per le transazioni consentono di gestire carichi di lavoro con un numero elevato di transazioni che non necessitano della latenza offerta dalle condivisioni file Premium. Queste condivisioni file ottimizzate per le transazioni sono disponibili nell'hardware di archiviazione standard basato su unità disco rigido (HDD) e vengono distribuite nel tipo di account di archiviazione **per utilizzo generico versione 2 (GPv2)** . Questo livello di archiviazione ottimizzato per le transazioni viene in genere definito "standard", ma in realtà tale aggettivo si riferisce al tipo di supporto di archiviazione e non al livello. Di fatto anche il livello ad accesso frequente e quello ad accesso sporadico sono "standard", perché usano hardware di archiviazione standard.
- **Hot**: le condivisioni file ad accesso frequente offrono archiviazione ottimizzata per scenari di condivisione di file per utilizzo generico, ad esempio condivisioni di team e Sincronizzazione file di Azure. Le condivisioni file ad accesso frequente sono disponibili nell'hardware di archiviazione standard basato su unità disco rigido (HDD) e vengono distribuite nel tipo di account di archiviazione **per utilizzo generico versione 2 (GPv2)** .
- **Cool**: le condivisioni file ad accesso sporadico offrono un'archiviazione conveniente ottimizzata per scenari di archiviazione online. Sincronizzazione file di Azure può essere una scelta valida anche per carichi di lavoro con varianza inferiore. Le condivisioni file ad accesso sporadico sono disponibili nell'hardware di archiviazione standard basato su unità disco rigido (HDD) e vengono distribuite nel tipo di account di archiviazione **per utilizzo generico versione 2 (GPv2)** .

Le condivisioni file premium sono disponibili solo in un modello di fatturazione con provisioning. Per altre informazioni sul modello di fatturazione con provisioning per le condivisioni file Premium, vedere [Informazioni sul provisioning per le condivisioni file Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Le condivisioni file standard, ovvero quelle ottimizzate per le transazioni, ad accesso frequente e ad accesso sporadico, prevedono la fatturazione con pagamento in base al consumo.

Le condivisioni file ad accesso frequente e ad accesso sporadico sono disponibili in tutte le aree pubbliche di Azure, mentre quelle ottimizzate per le transazioni sono disponibili in tutte le aree di Azure, incluse le aree cloud nazionali.

Per distribuire una condivisione file ad accesso frequente o ad accesso sporadico, vedere [Creare una condivisione file ad accesso frequente o ad accesso sporadico](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 