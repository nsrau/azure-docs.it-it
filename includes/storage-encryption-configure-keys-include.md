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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895276"
---
Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati di BLOB e file.

Le chiavi gestite dal cliente devono essere archiviate in un Azure Key Vault. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni sulla crittografia di archiviazione di Azure e sulla gestione delle chiavi, vedere [crittografia di archiviazione di Azure per dati](../articles/storage/common/storage-service-encryption.md)inattivi. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
