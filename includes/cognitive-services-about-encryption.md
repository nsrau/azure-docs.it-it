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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372172"
---
## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di Servizi cognitivi

I dati vengono crittografati e decrittografati utilizzando la crittografia [AES conforme](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Crittografia e decrittografia sono trasparenti, il che significa che la crittografia e l'accesso sono gestiti per voi. I dati sono al sicuro per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft.By default, your subscription uses Microsoft-managed encryption keys. Se si usa un piano tariffario che supporta le chiavi gestite dal cliente, è possibile visualizzare le impostazioni di crittografia per la risorsa nella sezione **Crittografia** del portale di [Azure,](https://portal.azure.com)come illustrato nell'immagine seguente.

![Visualizzare le impostazioni di crittografia](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Per le sottoscrizioni che supportano solo chiavi di crittografia gestite da Microsoft, non sarà disponibile una sezione **Crittografia.For** subscriptions that only support Microsoft-managed encryption keys, you will not have an Encryption section.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di AzureCustomer-managed keys with Azure Key Vault

C'è anche un'opzione per gestire l'abbonamento con le proprie chiavi. Le chiavi gestite dal cliente (CMK), note anche come Bring your own key (BYOK), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere i dati.
