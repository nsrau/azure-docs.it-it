---
title: Crittografia dei dati dell'hub IoT di Azure inattivi tramite chiavi gestite dal cliente Documenti Microsoft
description: Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub IoT
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370577"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub IoT

L'hub IoT supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), noto anche come Bring your own key (BYOK), supporto per Azure IoT Hub. Azure IoT Hub offre la crittografia dei dati inattivi e in transito. Per impostazione predefinita, l'hub IoT usa le chiavi gestite da Microsoft per crittografare i dati. Con il supporto CMK, i clienti possono ora scegliere di crittografare i dati inattivi con una chiave di crittografia della chiave, gestita dai clienti, usando l'insieme di credenziali delle chiavi di [Azure](https://azure.microsoft.com/services/key-vault/).

Questa funzionalità richiede la creazione di un nuovo hub IoT (livello di base o standard), in una delle seguenti aree geografiche: Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali o GOv degli Stati Uniti. Per provare questa funzionalità, contattaci tramite il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sull'hub IoT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
