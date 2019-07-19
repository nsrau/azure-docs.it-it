---
title: Connessione Istanza gestita SQL di Azure per l'indicizzazione di ricerca-ricerca di Azure
description: Abilitare l'endpoint pubblico per consentire le connessioni alle istanze gestite di SQL da un indicizzatore in ricerca di Azure.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229128"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Configurare una connessione da un indicizzatore di ricerca di Azure a SQL Istanza gestita
Come indicato nella pagina relativa [alla connessione del database SQL di Azure a ricerca di Azure tramite](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)gli indicizzatori, la creazione di indicizzatori in **istanze gestite da SQL** è supportata da ricerca di Azure tramite l'endpoint pubblico.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Creare Istanza gestita SQL di Azure con l'endpoint pubblico
Creare un Istanza gestita SQL con l'opzione **Abilita endpoint pubblico** selezionato.

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Abilita endpoint pubblico")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Abilitare l'endpoint pubblico di Istanza gestita SQL di Azure
È anche possibile abilitare l'endpoint pubblico in un istanza gestita SQL esistente in**rete** > virtuale di **sicurezza** > **abilitare**l'**endpoint** > pubblico.

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Abilita endpoint pubblico")

## <a name="verify-nsg-rules"></a>Verificare le regole di NSG
Verificare che nel gruppo di sicurezza di rete siano presenti le **regole di sicurezza in ingresso** corrette che consentono le connessioni dai servizi di Azure.

   ![Regola di sicurezza in ingresso NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regola di sicurezza in ingresso NSG")

## <a name="get-public-endpoint-connection-string"></a>Ottenere la stringa di connessione dell'endpoint pubblico
Assicurarsi di usare la stringa di connessione per l' **endpoint pubblico** (porta 3342, non porta 1433).

   ![Stringa di connessione dell'endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Stringa di connessione dell'endpoint pubblico")

## <a name="next-steps"></a>Passaggi successivi
Con la configurazione, è ora possibile specificare un Istanza gestita SQL come origine dati per un indicizzatore di ricerca di Azure usando il portale o l'API REST. Per altre informazioni, vedere [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
