---
title: Crittografia dei dati del servizio di provisioning dei dispositivi di Azure inattivi tramite chiavi gestite dal cliente Documenti Microsoft
description: Crittografia dei dati inattivi con chiavi gestite dal cliente per il servizio Device Provisioning
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675143"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per il servizio Device Provisioning

## <a name="overview"></a>Panoramica

Device Provisioning Service (DPS) supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), noto anche come Bring your own key (BYOK). DPS fornisce la crittografia dei dati inattivi e in transito. Per impostazione predefinita, DPS utilizza chiavi gestite da Microsoft per crittografare i dati. Con il supporto CMK, i clienti possono ora scegliere di crittografare i dati inattivi con una chiave di crittografia della chiave, gestita dai clienti, usando l'insieme di credenziali delle chiavi di [Azure](https://azure.microsoft.com/services/key-vault/).

Questa funzionalità richiede la creazione di un nuovo DPS, in una delle seguenti aree: Stati Uniti orientali, Stati Uniti occidentali 2 o Stati Uniti centro-meridionali. Per provare questa funzionalità, contattaci tramite il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sul servizio Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)

* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)