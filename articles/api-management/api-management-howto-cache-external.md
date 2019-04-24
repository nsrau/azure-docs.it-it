---
title: Usare una cache esterna in Gestione API di Azure | Microsoft Docs
description: Informazioni su come configurare e usare una cache esterna in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 8e3f34210627fbb455a1eca0b415cdd6de9b3681
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527834"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Usare una cache di Azure esterna per Redis in Gestione API di Azure

Oltre a utilizzare la cache predefinita, Gestione API di Azure consente di memorizzare le risposte in una cache di Azure esterna per Redis.

L'uso di una cache esterna consente di superare alcune limitazioni della cache predefinita. È particolarmente utile se si vuole:

* Evitare che la cache venga periodicamente cancellata durante gli aggiornamenti di Gestione API
* Avere maggiore controllo sulla configurazione della cache
* Memorizzare nella cache più dati di quelli consentiti dal livello Gestione API
* Usare la memorizzazione nella cache con il livello A consumo di Gestione API

Per informazioni più dettagliate sulla memorizzazione nella cache, vedere [Criteri di memorizzazione nella cache in Gestione API](api-management-caching-policies.md) e [Memorizzazione nella cache personalizzata in Gestione API di Azure](api-management-sample-cache-by-key.md).

![Funzionalità Bring Your Own Cache per Gestione API](media/api-management-howto-cache-external/overview.png)

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Aggiungere una cache esterna in Gestione API

## <a name="availability"></a>Disponibilità

> [!NOTE]
> Questa funzionalità è attualmente disponibile solo nel livello **A consumo** di Gestione API di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

+ [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
+ Conoscere la [memorizzazione nella cache di Gestione API di Azure](api-management-howto-cache.md)

## <a name="create-cache"> </a> Creare una cache di Azure per Redis

Questa sezione illustra come creare una cache per Redis in Azure. Se già si disponde di una cache di Azure per Redis, all'interno o all'esterno di Azure, è possibile <a href="#add-external-cache">passare</a> alla sezione successiva.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Aggiungere una cache esterna

Seguire i passaggi indicati di seguito per aggiungere una cache esterna per Redis in Gestione API di Azure.

![Funzionalità Bring Your Own Cache per Gestione API](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Il **usare da** impostazione consente di specificare che Gestione API di distribuzione a livello di area per le comunicazioni con la cache configurata in caso di una configurazione di più area di gestione API. Le cache specificate come **predefinite** verranno sostituite dalle cache con un valore a livello di area.
>
> Se ad esempio Gestione API è ospitato nelle aree Stati Uniti orientali, Asia sud-orientale ed Europa occidentale e sono presenti due cache configurate, una per **Predefinita** e una per **Asia sud-orientale**, Gestione API in **Asia sud-orientale** userà la propria cache, mentre le altre due aree useranno la voce della cache **Predefinita**.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Aggiungere una cache di Azure per Redis dalla stessa sottoscrizione

1. Passare all'istanza di Gestione API nel portale di Azure.
2. Selezionare la scheda **External cache** (Cache esterna) dal menu a sinistra.
3. Fare clic sul pulsante **+ Aggiungi**.
4. Selezionare la cache nel campo a discesa **Cache instance** (Istanza della cache).
5. Selezionare **predefinite** oppure specificare l'area desiderata nella **usare da** campo a discesa.
6. Fare clic su **Save**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Aggiungere una cache di Azure per Redis ospitata al di fuori della sottoscrizione di Azure corrente o di Azure in generale

1. Passare all'istanza di Gestione API nel portale di Azure.
2. Selezionare la scheda **External cache** (Cache esterna) dal menu a sinistra.
3. Fare clic sul pulsante **+ Aggiungi**.
4. Selezionare **Personalizzata** nel campo a discesa **Cache instance** (Istanza della cache).
5. Selezionare **predefinite** oppure specificare l'area desiderata nella **usare da** campo a discesa.
6. Fornire la stringa di connessione della cachedi Azure per Redis nel campo **Stringa di connessione**.
7. Fare clic su **Save**.

## <a name="use-the-external-cache"></a>Usare la cache esterna

Dopo essere stata configurata in Gestione API di Azure, la cache esterna può essere usata tramite i criteri di memorizzazione nella cache. Vedere [Aggiungere il caching per migliorare le prestazioni in Gestione API di Azure](api-management-howto-cache.md) per la procedura dettagliata.

## <a name="next-steps"> </a>Passaggi successivi
* Per altre informazioni sui criteri di caching, vedere [Caching policies][Caching policies] (Criteri di caching) nell'argomento [API Management policy reference][API Management policy reference] (Riferimento ai criteri di Gestione API).
* Per informazioni sul caching degli elementi in base alla chiave usando espressioni di criteri, vedere [Caching personalizzato in Gestione API di Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
