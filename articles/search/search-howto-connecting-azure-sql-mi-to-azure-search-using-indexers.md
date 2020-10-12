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
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294255"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configurare una connessione da un indicizzatore di Azure ricerca cognitiva a SQL Istanza gestita

Come indicato nella pagina relativa [alla connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), la creazione di indicizzatori in **istanze gestite da SQL** è supportata da Azure ricerca cognitiva tramite l'endpoint pubblico.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Creare Istanza gestita SQL di Azure con l'endpoint pubblico
Creare un Istanza gestita SQL con l'opzione **Abilita endpoint pubblico** selezionato.

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Abilita endpoint pubblico")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Abilitare l'endpoint pubblico di Istanza gestita SQL di Azure
È anche possibile abilitare l'endpoint pubblico in un istanza gestita SQL esistente **Security**in  >  **rete virtuale**di sicurezza abilitare l'  >  **endpoint pubblico**  >  **Enable**.

   ![Abilitare l'endpoint pubblico usando l'istanza gestita VNET](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Abilita endpoint pubblico")

## <a name="verify-nsg-rules"></a>Verificare le regole di NSG
Verificare che nel gruppo di sicurezza di rete siano presenti le **regole di sicurezza in ingresso** corrette che consentono le connessioni dai servizi di Azure.

   ![Regola di sicurezza in ingresso NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regola di sicurezza in ingresso NSG")

> [!NOTE]
> Gli indicizzatori richiedono comunque che SQL Istanza gestita sia configurato con un endpoint pubblico per leggere i dati.
> Tuttavia, è possibile scegliere di limitare l'accesso in ingresso a tale endpoint pubblico sostituendo la regola corrente ( `public_endpoint_inbound` ) con le 2 regole seguenti:
>
> * Consentire l'accesso in ingresso dal `AzureCognitiveSearch` [tag del servizio](../virtual-network/service-tags-overview.md#available-service-tags) ("Source" = `AzureCognitiveSearch` , "Name" = `cognitive_search_inbound` )
>
> * Consentire l'accesso in ingresso dall'indirizzo IP del servizio di ricerca, che può essere ottenuto effettuando il ping del nome di dominio completo (ad esempio, `<your-search-service-name>.search.windows.net` ). ("SOURCE" = `IP address` , "Name" = `search_service_inbound` )
>
> Per ognuna di queste due regole, impostare "PORT" = `3342` , "Protocol" = `TCP` , "Destination" = `Any` , "Action" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Ottenere la stringa di connessione dell'endpoint pubblico
Assicurarsi di usare la stringa di connessione per l' **endpoint pubblico** (porta 3342, non porta 1433).

   ![Stringa di connessione dell'endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Stringa di connessione dell'endpoint pubblico")

## <a name="next-steps"></a>Passaggi successivi
Con la configurazione, è ora possibile specificare un Istanza gestita SQL come origine dati per un indicizzatore di Azure ricerca cognitiva usando il portale o l'API REST. Per altre informazioni, vedere [connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .