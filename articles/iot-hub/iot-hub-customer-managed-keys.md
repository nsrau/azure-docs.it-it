---
title: Crittografia dei dati inattivi dell'hub Internet degli Azure tramite chiavi gestite dal cliente | Microsoft Docs
description: Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub Internet
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976575"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per l'hub Internet

L'hub Internet delle cose supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), note anche come BYOK (Bring your own key). L'hub Internet degli Azure è la crittografia dei dati inattivi e in transito, poiché viene scritto nei data center e ne viene decrittografato l'accesso. Per impostazione predefinita, l'hub Internet usa chiavi gestite da Microsoft per crittografare i dati inattivi. Con CMK è possibile ottenere un altro livello di crittografia sulla crittografia predefinita e scegliere di crittografare i dati inattivi con una chiave di crittografia della chiave, gestiti tramite il [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Questo offre la flessibilità necessaria per creare, ruotare, disabilitare e revocare i controlli di accesso. Se BYOK è configurato per l'hub Internet delle cose, viene fornita anche la crittografia doppia, che offre un secondo livello di protezione, consentendo al contempo di controllare la chiave di crittografia tramite il Azure Key Vault.

Questa funzionalità richiede la creazione di un nuovo hub Internet (livello Basic o standard). Per provare questa funzionalità, contattaci attraverso il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.


## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più sull'hub Internet](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
