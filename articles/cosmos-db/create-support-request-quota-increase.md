---
title: Come richiedere un aumento della quota per le risorse di Azure Cosmos DB
description: Informazioni su come richiedere un aumento della quota per Azure Cosmos DB risorse. Si apprenderà anche come abilitare una sottoscrizione per l'accesso a un'area.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: cd2bc3ec7e70049ee7e2c700731515a272e541dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097987"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Come richiedere un aumento della quota per le risorse di Azure Cosmos DB

Le risorse in Azure Cosmos DB hanno [quote/limiti predefiniti](concepts-limits.md). Tuttavia, può verificarsi un caso in cui il carico di lavoro richiede una quota maggiore rispetto al valore predefinito. In tal caso, è necessario contattare il team di Azure Cosmos DB per richiedere un aumento della quota. Questo articolo illustra come richiedere un aumento della quota per Azure Cosmos DB risorse. Si apprenderà anche come abilitare una sottoscrizione per l'accesso a un'area.

## <a name="create-a-new-support-request"></a>Creare una nuova richiesta di supporto

Per richiedere un aumento della quota, è necessario creare una nuova richiesta di supporto con i dettagli del carico di lavoro. Il team di Azure Cosmos DB valuterà quindi la richiesta e la approverà. Per creare una nuova richiesta di supporto dal portale di Azure, attenersi alla procedura seguente:

1. Accedere al portale di Azure.

1. Dal menu a sinistra selezionare **Guida e supporto** e quindi selezionare **nuova richiesta di supporto**.

1. Nella scheda informazioni di **base** immettere i dettagli seguenti:

   * Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**
   * Per **Subscription (sottoscrizione**) selezionare la sottoscrizione per cui si vuole aumentare la quota.
   * Per **tipo di quota**selezionare **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Crea una nuova richiesta di supporto Cosmos DB per l'aumento della quota":::

1. Nella scheda **Dettagli** immettere i dettagli corrispondenti alla richiesta di quota. Le informazioni fornite in questa scheda verranno utilizzate per valutare ulteriormente il problema e consentire al tecnico del supporto di risolvere il problema.

1. Inserire i dettagli seguenti nel seguente formato:

   * **Descrizione**: fornire una breve descrizione della richiesta, ad esempio il carico di lavoro, perché i valori predefiniti non sono sufficienti. In base al tipo di risorsa per cui si vuole aumentare la quota, è obbligatorio specificare i dettagli seguenti nel campo **Descrizione** :

     **Richieste aree** Se la richiesta corrisponde all'aggiunta di un'area all'elenco dei valori consentiti, assicurarsi di specificare i valori seguenti:

        * Nome area
        * ID sottoscrizione

     **Richieste limite velocità effettiva** Se la richiesta corrisponde all'aumento della quota per la velocità effettiva, assicurarsi di specificare i valori seguenti:

        * Nome database
        * ID sottoscrizione
        * Limite velocità effettiva nuovo

     **Richieste limite account database** Se la richiesta corrisponde all'aumento della quota per il numero di account di database in una sottoscrizione, assicurarsi di specificare i valori seguenti:

       * ID sottoscrizione
       * Limite nuovo account database

   * **Caricamento file**: caricare i file di diagnostica o qualsiasi altro file ritenuto pertinente per la richiesta di supporto. Per altre informazioni sulle linee guida per il caricamento di file, vedere l'articolo del [supporto tecnico di Azure]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) .

   * **Gravità**: scegliere uno dei livelli di gravità disponibili in base all'effetto aziendale.

   * **Metodo di contatto preferito**: è possibile scegliere di essere contattati tramite **posta elettronica** o **telefonicamente**.

1. Immettere i dettagli rimanenti, ad esempio la disponibilità, la lingua di supporto, le informazioni di contatto, la posta elettronica e il numero di telefono nel modulo.

1. Selezionare **Avanti: verifica + crea**. Convalidare le informazioni fornite e selezionare **Crea** per creare una richiesta di supporto.

Entro 24 ore, il team di supporto di Azure Cosmos DB valuterà la richiesta e tornerà a te.

## <a name="next-steps"></a>Passaggi successivi

* Vedere le [quote del servizio Azure Cosmos DB predefinite](concepts-limits.md)
