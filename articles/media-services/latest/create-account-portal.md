---
title: Creare un account Servizi multimediali di Azure con il portale di Azure
description: Questa esercitazione illustra la procedura di creazione di un account Servizi multimediali di Azure con il portale di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5b05cd31a1747da0170556003e7a8534752e2fde
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137195"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Usare il portale di Azure per creare un account di servizi multimediali

Il portale di Azure fornisce un modo per creare rapidamente un account di servizi multimediali di Azure. È possibile utilizzare l'account per accedere ai servizi multimediali che consentono di archiviare, crittografare, codificare, gestire e distribuire in streaming contenuti multimediali in Azure.

Attualmente, è possibile utilizzare il [portale di Azure](https://portal.azure.com/) per:

* gestione [degli eventi live](live-events-outputs-concept.md)di servizi multimediali V3 
* visualizzare (non gestire) gli [Asset](assets-concept.md)V3, 
* [ottenere informazioni sull'accesso alle API](access-api-portal.md). 

Per tutte le altre attività di gestione (ad esempio, [trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto](content-protection-overview.md)), usare l' [API REST](https://aka.ms/ams-v3-rest-ref), l' [interfaccia](https://aka.ms/ams-v3-cli-ref)della riga di comando o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

Questo articolo illustra come creare un account di Servizi multimediali usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisites

Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Fare clic su **+ Crea una risorsa** > **media** > **servizi multimediali**.
1. Nella sezione **creare un account di servizi multimediali** immettere i valori necessari.
    
    | Nome | Descrizione |
    | ---|---|
    |**Account Name** (Nome account)|Immettere il nome del nuovo account di servizi multimediali. Un nome di account di Servizi multimediali deve essere composto solo da lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.|
    |**Sottoscrizione**|Se si hanno più sottoscrizioni, selezionarne una dall'elenco delle sottoscrizioni di Azure a cui si ha accesso.|
    |**Gruppo di risorse**|Selezionare la risorsa nuova o esistente. Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../../azure-resource-manager/management/overview.md#resource-groups) per altre informazioni.|
    |**Posizione**|Selezionare l'area geografica che verrà usata per archiviare i record di supporti e metadati per l'account di servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. |
    |**Storage Account**|Selezionare un account di archiviazione per fornire l'archiviazione BLOB del contenuto multimediale dell'account di servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account di Servizi multimediali oppure è possibile crearne uno nuovo. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.<br/><br/>È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. È possibile usare la portale di Azure per aggiungere account di archiviazione secondari. Per altre informazioni, vedere [account di archiviazione di Azure con account di servizi multimediali di Azure](storage-account-concept.md).<br/><br/>L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.|
    
1. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
1. Fare clic su **Crea** nella parte inferiore del form.

    Quando l'account di Servizi multimediali viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md) e della [crittografia dinamica](content-protection-overview.md), l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato in **esecuzione** . 

## <a name="next-steps"></a>Passaggi successivi

Se si prevede di accedere all'API di servizi multimediali a livello di codice, vedere [accedere all'API di servizi multimediali di Azure con l'autenticazione di Azure ad](access-api-portal.md).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

