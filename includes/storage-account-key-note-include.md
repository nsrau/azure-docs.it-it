---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027333"
---
## <a name="protect-your-access-keys"></a>Proteggere le chiavi di accesso

La chiave di accesso dell'account di archiviazione è simile a una password radice per l'account di archiviazione. Prestare sempre attenzione a proteggere le chiavi di accesso. Usare Azure Key Vault per gestire e ruotare le chiavi in modo sicuro. Evitare di distribuire chiavi di accesso ad altri utenti, codificarle a livello di codice o salvarle ovunque in testo normale accessibile ad altri utenti. Ruotare le chiavi se si ritiene che potrebbero essere state compromesse.

> [!NOTE]
> Microsoft consiglia di usare Azure Active Directory (Azure AD) per autorizzare le richieste sui dati di BLOB e di Accodamento, se possibile, invece della chiave condivisa. Azure AD offre sicurezza e facilità d'uso superiori rispetto alla chiave condivisa. Per altre informazioni su come autorizzare l'accesso ai dati con Azure AD, vedere [autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
