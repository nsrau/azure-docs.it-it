---
title: Gestire gli account di Servizi multimediali di Azure v3 Documenti Microsoft
description: Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Questo articolo illustra come gestire gli account di Servizi multimediali di Azure.This article explains how to manage Azure Media Services v3 accounts.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980372"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gestire gli account di Servizi multimediali di Azure 63Manage Azure Media Services v3 accounts

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. Per ulteriori informazioni, vedere [Account di archiviazione](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Spostamento di un account di Servizi multimediali tra sottoscrizioni 

Se è necessario spostare un account di Servizi multimediali in una nuova sottoscrizione, è innanzitutto necessario spostare l'intero gruppo di risorse che contiene l'account di Servizi multimediali nella nuova sottoscrizione. È necessario spostare tutte le risorse associate: account di archiviazione di Azure, profili DELLA rete CDN di Azure e così via. Per altre informazioni, vedere [Spostare le risorse](../../azure-resource-manager/management/move-resource-group-and-subscription.md)in un nuovo gruppo di risorse o sottoscrizione. Come con tutte le risorse in Azure, gli spostamenti dei gruppi di risorse possono richiedere del tempo per essere completati.

> [!NOTE]
> Media Services v3 supporta il modello multi-tenancy.

### <a name="considerations"></a>Considerazioni

* Creare copie di backup di tutti i dati nell'account prima di eseguire la migrazione a una sottoscrizione diversa.
* È necessario arrestare tutti gli endpoint di streaming e le risorse di streaming live. Gli utenti non saranno in grado di accedere al contenuto per la durata dello spostamento del gruppo di risorse. 

> [!IMPORTANT]
> Non avviare l'endpoint di streaming fino al completamento dello spostamento.

### <a name="troubleshoot"></a>Risolvere i problemi 

Se un account di Servizi multimediali o un account di Archiviazione di Azure associato viene "disconnesso" dopo lo spostamento del gruppo di risorse, provare a ruotare le chiavi dell'account di archiviazione. Se la rotazione delle chiavi dell'account di archiviazione non risolve lo stato "disconnesso" dell'account di Servizi multimediali, presentare una nuova richiesta di supporto dal menu "Supporto e risoluzione dei problemi" nell'account di Servizi multimediali.  

## <a name="next-steps"></a>Passaggi successivi

[Creare un account](create-account-cli-quickstart.md)
