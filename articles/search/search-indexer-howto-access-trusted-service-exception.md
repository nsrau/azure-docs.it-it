---
title: Accesso dell'indicizzatore ad archiviazione di Azure tramite un'eccezione del servizio attendibile
titleSuffix: Azure Cognitive Search
description: Abilitare l'accesso ai dati da un indicizzatore in Azure ricerca cognitiva ai dati archiviati in modo sicuro in archiviazione di Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101376"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Accesso dell'indicizzatore ad archiviazione di Azure tramite l'eccezione del servizio attendibile (Azure ricerca cognitiva)

Gli indicizzatori in un servizio ricerca cognitiva di Azure che accedono ai dati negli account di archiviazione di Azure possono usare la funzionalità di [eccezione del servizio attendibile](../storage/common/storage-network-security.md#exceptions) per accedere in modo sicuro ai dati. Questo meccanismo offre ai clienti che non sono in grado di concedere [l'accesso all'indicizzatore usando regole del firewall IP](search-indexer-howto-access-ip-restricted.md) un'alternativa semplice, sicura e gratuita per accedere ai dati negli account di archiviazione.

> [!NOTE]
> Il supporto per l'accesso ai dati negli account di archiviazione tramite un'eccezione del servizio attendibile è limitato all'archiviazione BLOB di Azure e Azure Data Lake archiviazione Gen2. L'archiviazione tabelle di Azure non è supportata.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Passaggio 1: configurare una connessione usando un'identità gestita

Seguire le istruzioni in [configurare una connessione a un account di archiviazione di Azure usando un'identità gestita](search-howto-managed-identities-storage.md). Al termine, il servizio di ricerca sarà stato registrato con Azure Active Directory come servizio attendibile e verranno concesse le autorizzazioni in archiviazione di Azure che forniscono i diritti specifici dell'identità di ricerca per accedere ai dati o alle informazioni.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Passaggio 2: consentire ai servizi Microsoft attendibili di accedere all'account di archiviazione

Nella portale di Azure passare alla scheda **firewall e reti virtuali** dell'account di archiviazione. Verificare che l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** sia selezionata. Questa opzione consente solo all'istanza del servizio di ricerca specifica con accesso basato sui ruoli appropriato per l'account di archiviazione (autenticazione avanzata) per accedere ai dati nell'account di archiviazione, anche se è protetto da regole del firewall IP.

![Eccezione servizio attendibile](media\search-indexer-howto-secure-access\exception.png "Eccezione servizio attendibile")

Gli indicizzatori saranno ora in grado di accedere ai dati nell'account di archiviazione, anche se l'account è protetto tramite regole del firewall IP.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli indicizzatori di Archiviazione di Azure:

- [Indicizzatore BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
- [Indicizzatore di Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indicizzatore di tabelle di Azure](search-howto-indexing-azure-tables.md)