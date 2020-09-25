---
title: Consenti l'accesso all'archiviazione tramite un'eccezione del servizio attendibile
titleSuffix: Azure Cognitive Search
description: Guida che descrive come impostare un'eccezione del servizio attendibile per accedere ai dati dagli account di archiviazione in modo sicuro.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320473"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Accesso sicuro ai dati negli account di archiviazione tramite un'eccezione del servizio attendibile

Gli indicizzatori che accedono ai dati negli account di archiviazione possono usare la funzionalità di [eccezione del servizio attendibile](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) per accedere in modo sicuro ai dati. Questo meccanismo offre ai clienti che non sono in grado di concedere [l'accesso all'indicizzatore tramite regole del firewall IP](search-indexer-howto-access-ip-restricted.md) un'alternativa semplice, sicura e gratuita per accedere ai dati negli account di archiviazione.

> [!NOTE]
> Il supporto per l'accesso ai dati negli account di archiviazione tramite un'eccezione del servizio attendibile è limitato all'archiviazione BLOB di Azure e Azure Data Lake archiviazione Gen2. L'archiviazione tabelle di Azure non è supportata.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Passaggio 1: configurare la connessione all'account di archiviazione tramite Identity

Seguire i dettagli descritti nella Guida all' [accesso alle identità gestite](search-howto-managed-identities-storage.md) per configurare gli indicizzatori per accedere agli account di archiviazione tramite l'identità gestita del servizio di ricerca.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Passaggio 2: consentire ai servizi Microsoft attendibili di accedere all'account di archiviazione

Nella portale di Azure passare alla scheda "firewall e reti virtuali" dell'account di archiviazione. Verificare che sia selezionata l'opzione "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione". Questa opzione consente solo all'istanza del servizio di ricerca specifica con accesso basato sui ruoli appropriato per l'account di archiviazione (autenticazione avanzata) per accedere ai dati nell'account di archiviazione, anche se è protetto da regole del firewall IP.

![Eccezione servizio attendibile](media\search-indexer-howto-secure-access\exception.png "Eccezione servizio attendibile")

Gli indicizzatori saranno ora in grado di accedere ai dati nell'account di archiviazione, anche se l'account è protetto tramite regole del firewall IP.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli indicizzatori di Archiviazione di Azure:

- [Indicizzatore BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
- [Indicizzatore di Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indicizzatore di tabelle di Azure](search-howto-indexing-azure-tables.md)
