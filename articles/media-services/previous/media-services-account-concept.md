---
title: Gestire gli account di servizi multimediali di Azure v2 | Microsoft Docs
description: Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Questo articolo illustra come gestire gli account di servizi multimediali di Azure v2.
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
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622043"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Gestire gli account di servizi multimediali di Azure v2

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Lo spostamento di un account servizi multimediali tra sottoscrizioni 

Se è necessario spostare un account servizi multimediali in una nuova sottoscrizione, è necessario innanzitutto spostare l'intero gruppo di risorse che contiene l'account di servizi multimediali per la nuova sottoscrizione. È necessario spostare tutte le risorse collegate: Account di archiviazione di Azure, i profili rete CDN di Azure e così via. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/resource-group-move-resources.md). Come tutte le risorse in Azure, si sposta gruppo di risorse può richiedere parecchio tempo.

Servizi multimediali v2 supporta il modello multi-tenancy. Se è necessario spostare un account servizi multimediali in una sottoscrizione in un nuovo tenant, creare una nuova applicazione Azure Active Directory (Azure AD) nel nuovo tenant. Quindi spostare l'account della sottoscrizione nel nuovo tenant. Dopo aver completato lo spostamento di tenant, è possibile iniziare a usare un'applicazione di Azure AD dai nuovi tenant per accedere all'account di servizi multimediali usando le API v2. 

> [!IMPORTANT]
> È necessario reimpostare i [autenticazione di Azure AD](media-services-portal-get-started-with-aad.md) info per accedere all'API di servizi multimediali v2.  
### <a name="considerations"></a>Considerazioni

* Creare i backup di tutti i dati nell'account prima di eseguire la migrazione a una sottoscrizione diversa.
* È necessario arrestare tutti gli endpoint di Streaming e live streaming delle risorse. Gli utenti non sarà in grado di accedere ai contenuti per la durata dello spostamento di gruppo di risorse. 

> [!IMPORTANT]
> Non avviare l'Endpoint di Streaming fino a quando non lo spostamento viene completata correttamente.

### <a name="troubleshoot"></a>Risolvere problemi 

Se un account servizi multimediali o un account di archiviazione di Azure associato diventa "disconnesso" dopo lo spostamento di gruppo di risorse, provare a ruotare le chiavi dell'Account di archiviazione. Se ruotare le chiavi dell'Account di archiviazione, lo stato "disconnected" dell'account di servizi multimediali non viene risolto, di una nuova richiesta di supporto dal file la "Supporto e risoluzione dei problemi" menu nell'account di servizi multimediali.  
 
## <a name="next-steps"></a>Passaggi successivi

[Creare un account](media-services-portal-create-account.md)
