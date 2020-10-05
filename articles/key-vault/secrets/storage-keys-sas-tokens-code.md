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
ms.openlocfilehash: 6072c71fa675bd203e94f3f42814a1183b12ae8e
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597972"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Creare la definizione della firma di accesso condiviso e recuperare i token della firma di accesso condiviso nel codice

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

Per indicazioni sull'uso del token di firma di accesso condiviso recuperato da Key Vault per accedere ai servizi di Archiviazione di Azure, vedere [Usare una firma di accesso condiviso dell'account per accedere al servizio BLOB](https://docs.microsoft.com/azure/storage/common/storage-account-sas-create-dotnet#use-an-account-sas-from-a-client)

> [!NOTE]
> L'app deve essere preparata per l'aggiornamento della firma di accesso condiviso se ottiene un errore 403 dalla risorsa di archiviazione, in modo da poter gestire il caso in cui una chiave è stata compromessa ed è necessario ruotarla più velocemente rispetto al periodo di rotazione normale. 

## <a name="next-steps"></a>Passaggi successivi
- Vedere altre informazioni su come [Concedere l'accesso limitato alle risorse di Archiviazione di Azure tramite la firma di accesso condiviso](../../storage/common/storage-sas-overview.md).
- Vedere altre informazioni su come [Gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure](overview-storage-keys.md) o [Azure PowerShell](overview-storage-keys-powershell.md).
- Vedere [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
