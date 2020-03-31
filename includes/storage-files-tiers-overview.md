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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597822"
---
File di Azure offre due diversi livelli di archiviazione, premium e standard, per consentire di adattare le azioni ai requisiti di prestazioni e prezzo dello scenario:

- **Condivisioni file Premium:** le condivisioni file Premium sono supportate da unità a stato solido (SSD) e vengono distribuite nel tipo di account di archiviazione FileStorage.Premium file shares: Premium file shares are backed by solid-state drives (SSDs) and are deployed in the **FileStorage storage account** type. Le condivisioni file Premium offrono prestazioni elevate coerenti e bassa latenza, entro millisecondi a una cifra per la maggior parte delle operazioni di I/O, per carichi di lavoro che richiedono un uso intensivo di I/O.Premium file shares provide consistent high performance and low tency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. Questo li rende adatti per un'ampia gamma di carichi di lavoro come database, hosting di siti Web e ambienti di sviluppo. Le condivisioni file premium sono disponibili solo in un modello di fatturazione con provisioning. Per ulteriori informazioni sul modello di fatturazione di cui è stato eseguito il provisioning per le condivisioni file premium, vedere [Informazioni sul provisioning per le condivisioni file premium.](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)
- **Condivisioni file standard:** le condivisioni file standard sono supportate da unità disco rigido (HDD) e vengono distribuite nel tipo di account di archiviazione di tipo **GPv2 (versione 2).** Le condivisioni file standard offrono prestazioni affidabili per i carichi di lavoro di I/O meno sensibili alla variabilità delle prestazioni, ad esempio condivisioni file generiche e ambienti di sviluppo/test. Le condivisioni file standard sono disponibili solo in un modello di fatturazione con pagamento in base al consumo.