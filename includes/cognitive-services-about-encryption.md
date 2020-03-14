---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372172"
---
## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi

I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione USA chiavi di crittografia gestite da Microsoft. Se si usa un piano tariffario che supporta chiavi gestite dal cliente, è possibile visualizzare le impostazioni di crittografia per la risorsa nella sezione **crittografia** del [portale di Azure](https://portal.azure.com), come illustrato nella figura seguente.

![Visualizzare le impostazioni di crittografia](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Per le sottoscrizioni che supportano solo le chiavi di crittografia gestite da Microsoft, non è presente una sezione di **crittografia** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

È anche possibile gestire la sottoscrizione con le proprie chiavi. Le chiavi gestite dal cliente (CMK), note anche come Bring your own key (BYOK), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.
