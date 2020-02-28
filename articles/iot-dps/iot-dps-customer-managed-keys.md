---
title: Crittografia dei dati inattivi del servizio Device provisioning di Azure tramite chiavi gestite dal cliente | Microsoft Docs
description: Crittografia dei dati inattivi con chiavi gestite dal cliente per il servizio Device provisioning
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675143"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per il servizio Device provisioning

## <a name="overview"></a>Panoramica

Il servizio Device provisioning (DPS) supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), note anche come BYOK (Bring your own key). DPS fornisce la crittografia dei dati inattivi e in transito. Per impostazione predefinita, DPS USA chiavi gestite da Microsoft per crittografare i dati. Con il supporto di CMK, i clienti possono ora scegliere di crittografare i dati inattivi con una chiave di crittografia della chiave, gestita dai clienti, usando il [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Questa funzionalità richiede la creazione di un nuovo DPS, in una delle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2 o Stati Uniti centro-meridionali. Per provare questa funzionalità, contattaci attraverso il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul servizio Device provisioning](https://docs.microsoft.com/azure/iot-dps/)

* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)