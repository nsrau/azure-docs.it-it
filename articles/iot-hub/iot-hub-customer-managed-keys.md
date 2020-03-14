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
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370577"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub Internet

L'hub Internet delle cose supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), note anche come BYOK (Bring your own key), il supporto per l'hub Azure. L'hub Internet degli Azure fornisce la crittografia dei dati inattivi e in transito. Per impostazione predefinita, l'hub Internet usa chiavi gestite da Microsoft per crittografare i dati. Con il supporto di CMK, i clienti possono ora scegliere di crittografare i dati inattivi con una chiave di crittografia della chiave, gestita dai clienti, usando il [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Questa funzionalità richiede la creazione di un nuovo hub Internet (livello Basic o standard), in una delle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali o US Gov. Per provare questa funzionalità, contattaci attraverso il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più sull'hub Internet](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
