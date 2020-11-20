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
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967177"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Crittografia dei dati inattivi con chiavi gestite dal cliente per il servizio Device provisioning

## <a name="overview"></a>Panoramica

Il servizio Device provisioning (DPS) supporta la crittografia dei dati inattivi con chiavi gestite dal cliente (CMK), note anche come BYOK (Bring your own key). DPS fornisce la crittografia dei dati inattivi e in transito mentre viene scritta nei data center e la decrittografa per conto dell'utente, al momento dell'accesso. Per impostazione predefinita, DPS USA chiavi gestite da Microsoft per crittografare i dati inattivi. Con CMK è possibile ottenere un ulteriore livello di crittografia sulla base della crittografia della piattaforma predefinita, scegliendo di crittografare i dati inattivi con una chiave di crittografia chiave, gestiti tramite il [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Questo offre la flessibilità necessaria per creare, ruotare, disabilitare e revocare chiavi. Se CMK è configurato per il DPS, significa che la crittografia doppia è abilitata con due livelli di protezione che proteggono attivamente i dati. 

Questa funzionalità richiede la creazione di un nuovo DPS. Per provare questa funzionalità, contattaci attraverso il [supporto tecnico Microsoft](https://azure.microsoft.com/support/create-ticket/). Condividere il nome della società e l'ID sottoscrizione quando si contatta il supporto tecnico Microsoft.


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul servizio Device provisioning](./index.yml)

* [Altre informazioni su Azure Key Vault](../key-vault/general/overview.md)