---
title: Recuperare i token di firma di accesso condiviso nel codice | Azure Key Vault
description: Un account di archiviazione gestito consente una perfetta integrazione tra Azure Key Vault e un account di archiviazione di Azure. Questo esempio usa Azure SDK per .NET per gestire i token di firma di accesso condiviso.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: afe7d5ce3dd1756ddb9e33fe402fb2eb699ce8f7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007418"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Recuperare i token della firma di accesso condiviso nel codice

È possibile gestire l'account di archiviazione con i token di firma di accesso condiviso archiviati nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Concedere l'accesso limitato alle risorse di Archiviazione di Azure tramite la firma di accesso condiviso](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Per proteggere l'account di archiviazione è consigliabile usare il [controllo degli accessi in base al ruolo](../../storage/common/storage-auth-aad.md), che offre una maggiore sicurezza e facilità d'uso rispetto all'autorizzazione con chiave condivisa.

Questo articolo fornisce esempi di codice .NET che crea una definizione di firma di accesso condiviso e recupera i token di firma di accesso condiviso. Vedere l'esempio [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) per i dettagli completi, incluso il client generato per gli account di archiviazione gestiti da Key Vault. Per informazioni su come creare e archiviare token di firma di accesso condiviso, vedere [Gestire le chiavi dell'account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure](overview-storage-keys.md) o [Gestire le chiavi dell'account di archiviazione con Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Esempi di codice

Nell'esempio seguente viene creato un modello di firma di accesso condiviso:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Con questo modello è possibile creare una definizione di firma di accesso condiviso usando 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Dopo la creazione della definizione di firma di accesso condiviso, è possibile recuperare i token di firma di accesso condiviso come i segreti usando un `SecretClient`. È necessario anteporre al nome del segreto il nome dell'account di archiviazione seguito da un trattino:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Se il token di firma di accesso condiviso sta per scadere, è possibile recuperare nuovamente lo stesso segreto per generarne uno nuovo.

## <a name="next-steps"></a>Passaggi successivi
- Vedere altre informazioni su come [Concedere l'accesso limitato alle risorse di Archiviazione di Azure tramite la firma di accesso condiviso](../../storage/common/storage-sas-overview.md).
- Vedere altre informazioni su come [Gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure](overview-storage-keys.md) o [Azure PowerShell](overview-storage-keys-powershell.md).
- Vedere l'esempio [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) completo.
- Altri [esempi di Key Vault](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-key-vault)
- Vedere [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
