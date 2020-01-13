---
title: Crittografia dei dati inattivi dell'hub Internet degli Azure tramite chiavi gestite dal cliente | Microsoft Docs
description: Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub Internet
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 1bb55d593878026bb3e57014a317b4fc0158d734
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913134"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub Internet

L'hub Internet delle cose supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), note anche come BYOK (Bring your own key), il supporto per l'hub Azure. L'hub Internet degli Azure fornisce la crittografia dei dati inattivi e in transito. Per impostazione predefinita, l'hub Internet usa chiavi gestite da Microsoft per crittografare i dati. Con il supporto di CMK, i clienti possono ora scegliere di crittografare i dati inattivi con una chiave di crittografia della chiave, gestita dai clienti, usando il [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Questa funzionalità richiede la creazione di un nuovo hub Internet (livello Basic o standard), in una delle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2 o Stati Uniti centro-meridionali. Per provare questa funzionalità, contattaci attraverso il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più sull'hub Internet](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)