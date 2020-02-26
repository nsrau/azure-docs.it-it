---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597822"
---
File di Azure offre due diversi livelli di archiviazione, Premium e standard, per consentire di adattare le proprie condivisioni ai requisiti di prestazioni e prezzi dello scenario:

- **Condivisioni file Premium**: le condivisioni file Premium sono supportate da unità SSD (Solid-State Drive) e vengono distribuite nel tipo di **account di archiviazione filestorage** . Le condivisioni file Premium offrono prestazioni elevate e bassa latenza coerenti, all'interno di millisecondi a una sola cifra per la maggior parte delle operazioni di i/o, per i carichi di lavoro con In questo modo sono adatti per un'ampia gamma di carichi di lavoro, ad esempio database, hosting di siti Web e ambienti di sviluppo. Le condivisioni file premium sono disponibili solo in un modello di fatturazione con provisioning. Per altre informazioni sul modello di fatturazione con provisioning per le condivisioni file Premium, vedere [informazioni sul provisioning per le condivisioni file Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Condivisioni file standard**: le condivisioni file standard sono supportate da unità disco rigido (HDD) e vengono distribuite nel tipo di **account di archiviazione per utilizzo generico versione 2 (GPv2)** . Le condivisioni file standard offrono prestazioni affidabili per i carichi di lavoro di i/o meno sensibili alla variabilità delle prestazioni, ad esempio le condivisioni file per utilizzo generico e gli ambienti di sviluppo/test. Le condivisioni file standard sono disponibili solo in un modello di fatturazione con pagamento in base al consumo.