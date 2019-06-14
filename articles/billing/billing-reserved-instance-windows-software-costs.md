---
title: Costi del software per la prenotazione di Azure | Microsoft Docs
description: Informazioni sui contatori relativi al software non inclusi nei costi dell'istanza di macchina virtuale riservata di Azure.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 340cba65a1faac247678cd187f106157ba566f3e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371173"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Costi del software non inclusi nelle istanze di macchina virtuale riservate di Azure

Se non si ha un vantaggio Azure Hybrid nelle istanze di macchina virtuale riservate, viene addebitato per i contatori relativi al software elencati nella sezione seguente.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Costi dei contatori relativi al software Windows non inclusi nella prenotazione

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

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Costi dei contatori relativi al software dei servizi cloud non inclusi nella prenotazione

| ID contatore | Nome del contatore nel file di uso |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Licenza vCPU servizi cloud 1|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Licenza vCPU servizi cloud 2|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Licenza vCPU servizi cloud 4|
|13103090-ca72-4825-ab12-7f16c4931d95|Licenza vCPU servizi cloud 8|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Licenza vCPU servizi cloud 16|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Licenza vCPU servizi cloud 20|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Licenza vCPU servizi cloud 32|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Licenza vCPU servizi cloud 64|
|7a803026-244c-4659-834c-11e6b2d6b76f|Licenza vCPU servizi cloud 80|

## <a name="rates-for-azure-meters"></a>Tariffe per i contatori di Azure

È possibile ottenere il costo di ognuno di questi contatori tramite l'API RateCard di Azure. Per informazioni su come ottenere le tariffe per un contatore di Azure specifico, vedere l'argomento relativo a come [ottenere informazioni su prezzi e metadati per le risorse usate in una sottoscrizione di Azure](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle prenotazioni per Azure, vedere gli articoli seguenti:

- [Quali sono le prenotazioni per Azure?](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le prenotazioni per Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
