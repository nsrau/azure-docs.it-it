---
title: Identità gestite e archiviazione attendibile con servizi multimediali
description: Servizi multimediali può essere usato con le identità gestite per abilitare l'archiviazione attendibile.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: d0811e8f9183ee334d413bcad69f2c7b32023be3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499357"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Identità gestite e archiviazione attendibile con servizi multimediali

Servizi multimediali può essere usato con le [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md) per abilitare l'archiviazione attendibile. Quando si crea un account di servizi multimediali, è necessario associarlo a un account di archiviazione. Servizi multimediali può accedere a tale account di archiviazione usando l'autenticazione di sistema. Servizi multimediali verifica che l'account di servizi multimediali e l'account di archiviazione si trovino nella stessa sottoscrizione e che l'utente che aggiunge l'associazione abbia accesso all'account di archiviazione con Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Archiviazione attendibile

Tuttavia, se si vuole usare un firewall per proteggere l'account di archiviazione, è necessario usare l'autenticazione dell'identità gestita. Consente ai servizi multimediali di accedere all'account di archiviazione configurato con un firewall o una restrizione VNet tramite l'accesso di archiviazione attendibile.  Per altre informazioni sui servizi Microsoft attendibili, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="media-services-managed-identity-scenarios"></a>Scenari di identità gestite di servizi multimediali

Esistono attualmente due scenari in cui l'identità gestita può essere usata con servizi multimediali:

- Usare l'identità gestita dell'account di servizi multimediali per accedere agli account di archiviazione.

- Usare l'identità gestita dell'account di servizi multimediali per accedere a Key Vault per accedere alle chiavi del cliente.

Le due sezioni successive descrivono le differenze nei due scenari.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Usare l'identità gestita dell'account di servizi multimediali per accedere agli account di archiviazione

1. Creare un account di servizi multimediali con un'identità gestita.
1. Concedere all'entità di identità gestita l'accesso a un account di archiviazione di cui si è proprietari.
1. Servizi multimediali può quindi accedere all'account di archiviazione per conto dell'utente usando l'identità gestita.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Usare l'identità gestita dell'account di servizi multimediali per accedere a Key Vault per accedere alle chiavi del cliente

1. Creare un account di servizi multimediali con un'identità gestita.
1. Concedere all'entità di identità gestita l'accesso a un Key Vault di cui si è proprietari.
1. Configurare l'account di servizi multimediali per l'uso della crittografia dell'account basata sulla chiave del cliente.
1. Servizi multimediali accede Key Vault per conto dell'utente usando l'identità gestita.

Per altre informazioni sulle chiavi gestite dal cliente e Key Vault, vedere [Bring your own key (chiavi gestite dal cliente) con servizi multimediali](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Esercitazioni

In queste esercitazioni sono inclusi entrambi gli scenari descritti in precedenza.

- [Usare la portale di Azure per usare chiavi gestite dal cliente o BYOK con servizi multimediali](tutorial-byok-portal.md)
- [Usare chiavi gestite dal cliente o BYOK con l'API REST di servizi multimediali](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle identità gestite che è possibile eseguire per l'utente e le applicazioni Azure, vedere [Azure ad identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).