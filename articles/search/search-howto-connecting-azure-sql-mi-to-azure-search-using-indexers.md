---
title: Connessione dell'istanza gestita di Azure SQL per l'indicizzazione della ricercaAzure SQL Managed Instance connection for search indexing
titleSuffix: Azure Cognitive Search
description: Abilitare l'endpoint pubblico per consentire le connessioni alle istanze gestite SQL da un indicizzatore in Ricerca cognitiva di Azure.Enable public endpoint to allow connections to SQL Managed Instances from an indexer on Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964890"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configurare una connessione da un indicizzatore di Ricerca cognitiva di Azure all'istanza gestita SQLConfigure a connection from an Azure Cognitive Search indexer to SQL Managed Instance

Come indicato in Connessione del database SQL di Azure alla ricerca cognitiva di Azure usando [gli indicizzatori,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)la creazione di indicizzatori con **istanze gestite SQL** è supportata da Ricerca cognitiva di Azure tramite l'endpoint pubblico.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Creare un'istanza gestita SQL di Azure con un endpoint pubblicoCreate Azure SQL Managed Instance with public endpoint
Creare un'istanza gestita SQL con l'opzione **Abilita endpoint pubblico** selezionata.

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Abilita endpoint pubblico")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Abilitare l'endpoint pubblico dell'istanza gestita di Azure SQLEnable Azure SQL Managed Instance public endpoint
È inoltre possibile abilitare l'endpoint pubblico in un'istanza gestita SQL esistente in Abilita**endpoint** > **pubblico rete** > virtuale **di sicurezza** > **.**

   ![Abilita endpoint pubblico](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Abilita endpoint pubblico")

## <a name="verify-nsg-rules"></a>Verificare le regole del gruppo di sicurezza di baseVerify NSG rules
Controllare il gruppo di sicurezza di rete ha le regole di sicurezza in ingresso corrette che consentono le connessioni dai servizi di Azure.Check the Network Security Group has the correct **Inbound security rules** that allow connections from Azure services.

   ![Regola di sicurezza in ingresso del gruppo di sicurezza di base](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regola di sicurezza in ingresso del gruppo di sicurezza di base")

> [!NOTE]
> Gli indicizzatori richiedono comunque che l'istanza gestita SQL venga configurata con un endpoint pubblico per leggere i dati.
> Tuttavia, è possibile scegliere di limitare l'accesso in ingresso`public_endpoint_inbound`a tale endpoint pubblico sostituendo la regola corrente ( ) con le due regole seguenti:
>
> * `AzureCognitiveSearch` Consentire l'accesso in ingresso dal `AzureCognitiveSearch`tag del [servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("SOURCE" , "NAME" ) `cognitive_search_inbound`
>
> * Consentire l'accesso in ingresso dall'indirizzo IP del servizio di ricerca, che può essere `<your-search-service-name>.search.windows.net`ottenuto eseguendo il ping del relativo nome di dominio completo (ad esempio, ). ("ORIGINE" `IP address`- "NOME" `search_service_inbound`)
>
> Per ognuna di queste 2 regole, impostare "PORT" , `3342`, "PROTOCOL" , `TCP`"DESTINATION" , `Any`"ACTION"`Allow`

## <a name="get-public-endpoint-connection-string"></a>Ottenere la stringa di connessione dell'endpoint pubblicoGet public endpoint connection string
Assicurarsi di utilizzare la stringa di connessione per **l'endpoint pubblico** (porta 3342, non porta 1433).

   ![Stringa di connessione dell'endpoint pubblicoPublic endpoint connection string](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Stringa di connessione dell'endpoint pubblicoPublic endpoint connection string")

## <a name="next-steps"></a>Passaggi successivi
Con la configurazione in disintosso, è ora possibile specificare un'istanza gestita SQL come origine dati per un indicizzatore di Ricerca cognitiva di Azure usando il portale o l'API REST. Per altre informazioni, vedere Connessione del database SQL di Azure alla ricerca cognitiva di Azure usando [gli indicizzatori.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
