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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460568"
---
## <a name="protect-your-access-keys"></a>Proteggere le chiavi di accesso

Le chiavi di accesso dell'account di archiviazione sono simili a quelle di una password radice per l'account di archiviazione. Prestare sempre attenzione a proteggere le chiavi di accesso. Usare l'insieme di credenziali delle chiavi di Azure per gestire e ruotare le chiavi in modo sicuro. Evitare di distribuire le chiavi di accesso ad altri utenti, codificarle a livello di codice o salvarle in qualsiasi punto in testo normale accessibile ad altri utenti. Ruotare le chiavi se si ritiene che possano essere state compromesse.

Se possibile, usare Azure Active Directory (Azure AD) per autorizzare le richieste all'archiviazione BLOB e coda anziché a Shared Key.If possible, use Azure Active Directory (Azure AD) to authorize requests to Blob and Queue storage instead of Shared Key. Azure AD offre sicurezza e facilità d'uso superiori rispetto alla chiave condivisa. Per altre informazioni sull'autorizzazione dell'accesso ai dati con Azure AD, vedere [Autorizzare l'accesso a BLOB e code](../articles/storage/common/storage-auth-aad.md)di Azure con Azure Active Directory.For more information about authorizing access to data with Azure AD, see Authorize access to Azure blobs and queues using Azure Active Directory.
