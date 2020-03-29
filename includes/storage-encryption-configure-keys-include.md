---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895276"
---
Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivi. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.By default, data is encrypted with Microsoft-managed keys. Per un controllo aggiuntivo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati BLOB e dei file.

Le chiavi gestite dal cliente devono essere archiviate in un insieme di credenziali delle chiavi di Azure.Customer-managed keys must be stored in an Azure Key Vault. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni sulla crittografia di Archiviazione di Azure e sulla gestione delle chiavi, vedere Crittografia di Archiviazione di Azure per i [dati inattivi.](../articles/storage/common/storage-service-encryption.md) Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../articles/key-vault/key-vault-overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?
