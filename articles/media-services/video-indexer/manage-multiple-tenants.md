---
title: Gestire più tenant con Video Indexer - Azure
description: Questo articolo suggerisce le diverse opzioni di integrazione per la gestione di più tenant con Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/10/2019
ms.author: ikbarmen
ms.openlocfilehash: 3b6daa60e8c8945d49170a00da5bd3ffb57b48ac
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000115"
---
# <a name="manage-multiple-tenants"></a>Gestire più tenant

Questo articolo illustra le diverse opzioni per la gestione di più tenant con Video Indexer. Scegliere il metodo più adatto per lo scenario specifico:

* Account di Video Indexer per tenant
* Account singolo di Video Indexer per tutti i tenant
* Sottoscrizione di Azure per tenant

## <a name="video-indexer-account-per-tenant"></a>Account di Video Indexer per tenant

Quando si usa questa architettura, per ciascun tenant viene creato un account Video Indexer. I tenant hanno l'isolamento completo nel livello permanente e dell'ambiente di calcolo.  

![Account di Video Indexer per tenant](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Considerazioni

* I clienti non condividono gli account di archiviazione (a meno che non vangano configurati manualmente dal cliente).
* I clienti non condividono l'ambiente di calcolo (unità riservate) e non si compromettono a vicenda i tempi di elaborazione dei processi.
* È possibile rimuovere facilmente un tenant dal sistema eliminando l'account Video Indexer.
* Non è possibile condividere modelli personalizzati tra i tenant.

    Assicurarsi che non vi sia alcun requisito aziendale per la condivisione di modelli personalizzati.
* Più difficile da gestire a causa degli account multipli per ogni tenant (e i servizi multimediali associati) di Video Indexer.

> [!TIP]
> Creare un utente amministratore per il sistema nel [Portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/) e usare l'API di autorizzazione per fornire ai tenant il relativo [token di accesso dell'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Account singolo di Video Indexer per tutti gli utenti

Quando si usa questa architettura, il cliente è responsabile per l'isolamento dei tenant. Tutti i tenant devono usare un singolo account Video Indexer con un solo account Azure Media Service. Durante il caricamento, la ricerca o l'eliminazione di contenuti, il cliente dovrà filtrare i risultati appropriati per tale tenant.

![Account singolo di Video Indexer per tutti gli utenti](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Con questa opzione, i modelli di personalizzazione (utente, lingua e i marchi) possono essere condivisi o isolati tra tenant filtrando i modelli per ciascun tenant.

Durante il [caricamento dei video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) è possibile specificare un attributo di partizione diverso per ciascun tenant. Ciò consente l'isolamento nella [API di ricerca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Specificando l'attributo di partizione nella API di ricerca si otterranno solo i risultati della partizione specificata. 

 ### <a name="considerations"></a>Considerazioni

* Possibilità di condividere i contenuti e i modelli di personalizzazione tra i tenant.
* Un tenant influisce sulle prestazioni di altri tenant.
* Il cliente deve creare un livello di gestione complesso al di sopra di Video Indexer.

> [!TIP]
> È possibile usare l'attributo [priorità](upload-index-videos.md) per definire le priorità dei processi dei tenant.

## <a name="azure-subscription-per-tenant"></a>Sottoscrizione di Azure per tenant 

Quando si usa questa architettura, ciascun tenant avrà la propria sottoscrizione di Azure. Per ciascun utente, si creerà un nuovo account Video Indexer nella sottoscrizione del tenant.

![Sottoscrizione di Azure per tenant](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Considerazioni

* Si tratta dell'unica opzione che consente la separazione di fatturazione.
* Questa integrazione ha più sovraccarichi di gestione di Video Indexer per ciascun tenant. Se la fatturazione non è un requisito, è consigliabile usare una delle altre opzioni descritte in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

[Overview](video-indexer-overview.md)
