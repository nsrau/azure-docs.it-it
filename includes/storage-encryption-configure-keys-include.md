---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115437"
---
Archiviazione di Azure supporta la crittografia dei dati inattivi con chiavi gestite da Microsoft o le chiavi gestite dal cliente. Chiavi gestite dall'utente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.

Usare Azure Key Vault per gestire le chiavi e controllarne l'uso. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure è possibile usare le API di Azure Key Vault per generare le chiavi. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../articles/key-vault/key-vault-overview.md)
