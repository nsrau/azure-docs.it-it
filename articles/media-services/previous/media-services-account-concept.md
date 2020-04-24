---
title: Gestire gli account di servizi multimediali di Azure V2 | Microsoft Docs
description: Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Questo articolo illustra come gestire gli account di servizi multimediali di Azure V2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981938"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Gestire gli account di servizi multimediali di Azure V2

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Trasferimento di un account di servizi multimediali tra sottoscrizioni 

Se è necessario spostare un account di servizi multimediali in una nuova sottoscrizione, è necessario innanzitutto spostare l'intero gruppo di risorse contenente l'account di servizi multimediali nella nuova sottoscrizione. È necessario spostare tutte le risorse collegate: account di archiviazione di Azure, profili della rete CDN di Azure e così via. Per altre informazioni, vedere [spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Come per tutte le risorse in Azure, il completamento dello spostamento del gruppo di risorse può richiedere del tempo.

Media Services V2 non supporta il modello di multi-tenant. Se è necessario spostare un account di servizi multimediali in una sottoscrizione di un nuovo tenant, creare una nuova applicazione Azure Active Directory (Azure AD) nel nuovo tenant. Quindi spostare l'account nella sottoscrizione nel nuovo tenant. Al termine dello spostamento del tenant, è possibile iniziare a usare un'applicazione Azure AD dal nuovo tenant per accedere all'account di servizi multimediali usando le API v2. 

> [!IMPORTANT]
> È necessario reimpostare le informazioni di [autenticazione Azure ad](media-services-portal-get-started-with-aad.md) per accedere all'API di servizi multimediali V2.  
### <a name="considerations"></a>Considerazioni

* Creare backup di tutti i dati nell'account prima di eseguire la migrazione a una sottoscrizione diversa.
* È necessario arrestare tutti gli endpoint di streaming e le risorse di streaming live. Gli utenti non saranno in grado di accedere al contenuto per la durata dello spostamento del gruppo di risorse. 

> [!IMPORTANT]
> Non avviare l'endpoint di streaming finché il passaggio non viene completato correttamente.

### <a name="troubleshoot"></a>Risoluzione dei problemi 

Se un account di servizi multimediali o un account di archiviazione di Azure associato diventa "disconnesso" dopo lo spostamento del gruppo di risorse, provare a ruotare le chiavi dell'account di archiviazione. Se la rotazione delle chiavi dell'account di archiviazione non risolve lo stato "disconnesso" dell'account di servizi multimediali, inserire una nuova richiesta di supporto dal menu "supporto e risoluzione dei problemi" nell'account di servizi multimediali.  
 
## <a name="next-steps"></a>Passaggi successivi

[Creare un account](media-services-portal-create-account.md)
