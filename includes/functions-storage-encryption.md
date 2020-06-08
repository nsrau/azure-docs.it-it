---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648788"
---
Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per altre informazioni, vedere [Crittografia di Archiviazione di Azure per dati inattivi](../articles/storage/common/storage-service-encryption.md).

Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile specificare chiavi gestite dal cliente da usare per la crittografia dei dati BLOB e di file. Queste chiavi devono essere presenti in Azure Key Vault affinché le funzioni possano accedere all'account di archiviazione. Per altre informazioni, vedere [Crittografia dei dati inattivi con le chiavi gestite dal cliente](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  