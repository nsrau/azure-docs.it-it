---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460568"
---
## <a name="protect-your-access-keys"></a>Proteggere le chiavi di accesso

La chiave di accesso dell'account di archiviazione è simile a una password radice per l'account di archiviazione. Prestare sempre attenzione a proteggere le chiavi di accesso. Usare Azure Key Vault per gestire e ruotare le chiavi in modo sicuro. Evitare di distribuire chiavi di accesso ad altri utenti, codificarle a livello di codice o salvarle ovunque in testo normale accessibile ad altri utenti. Ruotare le chiavi se si ritiene che potrebbero essere state compromesse.

Se possibile, usare Azure Active Directory (Azure AD) per autorizzare le richieste all'archiviazione BLOB e di Accodamento invece che alla chiave condivisa. Azure AD offre sicurezza e facilità d'uso superiori rispetto alla chiave condivisa. Per altre informazioni su come autorizzare l'accesso ai dati con Azure AD, vedere [autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
