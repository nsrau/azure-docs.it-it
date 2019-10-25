---
title: Connessione Istanza gestita SQL di Azure per l'indicizzazione di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare l'endpoint pubblico per consentire le connessioni alle istanze gestite di SQL da un indicizzatore in ricerca cognitiva di Azure.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 16daf4a79252134703715ccd88f0b10dda7f4fa6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792163"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configurare una connessione da un indicizzatore di Azure ricerca cognitiva a SQL Istanza gestita

Come indicato nella pagina relativa [alla connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), la creazione di indicizzatori in **istanze gestite da SQL** è supportata da Azure ricerca cognitiva tramite l'endpoint pubblico.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Creare Istanza gestita SQL di Azure con l'endpoint pubblico
Creare un Istanza gestita SQL con l'opzione **Abilita endpoint pubblico** selezionato.

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Abilita endpoint pubblico")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Abilitare l'endpoint pubblico di Istanza gestita SQL di Azure
È anche possibile abilitare l'endpoint pubblico in un Istanza gestita SQL esistente in **sicurezza** > **rete virtuale** > **endpoint pubblico** > **abilitare**.

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Abilita endpoint pubblico")

## <a name="verify-nsg-rules"></a>Verificare le regole di NSG
Verificare che nel gruppo di sicurezza di rete siano presenti le **regole di sicurezza in ingresso** corrette che consentono le connessioni dai servizi di Azure.

   ![Regola di sicurezza in ingresso NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regola di sicurezza in ingresso NSG")

## <a name="get-public-endpoint-connection-string"></a>Ottenere la stringa di connessione dell'endpoint pubblico
Assicurarsi di usare la stringa di connessione per l' **endpoint pubblico** (porta 3342, non porta 1433).

   ![Stringa di connessione dell'endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Stringa di connessione dell'endpoint pubblico")

## <a name="next-steps"></a>Passaggi successivi
Con la configurazione, è ora possibile specificare un Istanza gestita SQL come origine dati per un indicizzatore di Azure ricerca cognitiva usando il portale o l'API REST. Per altre informazioni, vedere [connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
