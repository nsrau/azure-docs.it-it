---
title: File di inclusione
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284311"
---
Se è stata selezionata una delle VM preconfigurate con un sistema operativo e altri servizi facoltativi, è già stata selezionata una dimensione standard per le VM di Azure. Avviare una soluzione con un sistema operativo preconfigurato è l'approccio consigliato. Tuttavia, se si installa un sistema operativo manualmente, è necessario ridimensionare il disco rigido virtuale primario nell'immagine di macchina virtuale. Verificare che le dimensioni del disco del sistema operativo siano comprese nei limiti per Linux o Windows.

| Sistema operativo | Dimensioni VHD |
| --- | --- |
| Linux | da 30 a 1023 GB |
| Windows | da 30 a 250 GB |
|

Le immagini di base di Windows o SQL Server fornite come base approvata soddisfano già questi requisiti, quindi non modificare il formato o le dimensioni del disco rigido virtuale.

I dischi dati possono avere dimensioni fino a 1 TB. Nello stabilire le dimensioni, tenere presente che i clienti non possono ridimensionare i dischi rigidi virtuali all'interno di un'immagine in fase di distribuzione. Creare dischi rigidi virtuali del disco dati come dischi rigidi virtuali in formato fisso. Devono inoltre essere espandibili (sparse/dinamiche). I dischi dati inizialmente possono essere vuoti o contenere dati.