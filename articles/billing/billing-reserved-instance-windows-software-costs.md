---
title: Costi del software Windows per le istanze di macchina virtuale riservate di Azure | Microsoft Docs
description: Informazioni sui contatori usati per il software Windows per le macchine virtuali Windows idonee all'istanza riservata.
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: b985e6e9575ffeedcac5bcb3f94a43d23fdbb85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>Costi del software Windows non inclusi nelle istanze riservate

Se non si dispone dell'offerta Azure Hybrid Use Benefit per l'istanza di macchina virtuale riservata in uso, i costi verranno addebitati in base ai contatori relativi al software Windows elencati nella tabella seguente:

| ID contatore | Nome del contatore nel file di uso | Usato da VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Burst istanze riservate di Windows Server (1 core) | Serie B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Burst istanze riservate di Windows Server (2 core) | Serie B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Burst istanze riservate di Windows Server (4 core) | Serie B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Burst istanze riservate di Windows Server (8 core) | Serie B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Istanze riservate di Windows Server (1 core) | Tutte tranne Serie B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Istanze riservate di Windows Server (2 core) | Tutte tranne Serie B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Istanze riservate di Windows Server (4 core) | Tutte tranne Serie B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Istanze riservate di Windows Server (6 core) | Tutte tranne Serie B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Istanze riservate di Windows Server (8 core) | Tutte tranne Serie B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Istanze riservate di Windows Server (12 core) | Tutte tranne Serie B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Istanze riservate di Windows Server (16 core) | Tutte tranne Serie B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Istanze riservate di Windows Server (20 core) | Tutte tranne Serie B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Istanze riservate di Windows Server (24 core) | Tutte tranne Serie B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Istanze riservate di Windows Server (32 core) | Tutte tranne Serie B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Istanze riservate di Windows Server (40 core) | Tutte tranne Serie B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Istanze riservate di Windows Server (64 core) | Tutte tranne Serie B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Istanze riservate di Windows Server (72 core) | Tutte tranne Serie B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Istanze riservate di Windows Server (128 core) | Tutte tranne Serie B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Istanze riservate di Windows Server (256 core) | Tutte tranne Serie B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Istanze riservate di Windows Server (96 core) | Tutte tranne Serie B |

È possibile ottenere il costo di ognuno di questi contatori tramite l'API RateCard di Azure. Per informazioni su come ottenere le tariffe per un contatore di Azure specifico, vedere l'argomento relativo a come [ottenere informazioni su prezzi e metadati per le risorse usate in una sottoscrizione di Azure](https://msdn.microsoft.com/library/azure/mt219004).