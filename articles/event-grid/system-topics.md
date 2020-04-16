---
title: Argomenti di sistema in Griglia di eventi di AzureSystem topics in Azure Event Grid
description: Descrive gli argomenti di sistema in Griglia di eventi di Azure.Describes system topics in Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393160"
---
# <a name="system-topics-in-azure-event-grid"></a>Argomenti di sistema in Griglia di eventi di AzureSystem topics in Azure Event Grid
Il servizio Griglia di eventi di Azure crea argomenti di sistema quando si crea una prima sottoscrizione di eventi per un'origine eventi di Azure.The Azure Event Grid service creates system topics when you create a first event subscription for an Azure event source. Attualmente, Griglia di eventi non crea argomenti di sistema per le origini degli argomenti create prima del 15 marzo 2020. Per tutte le origini degli argomenti create in data o dopo tale data, Griglia di eventi crea automaticamente gli argomenti di sistema. Questo articolo descrive gli argomenti di sistema in Griglia di eventi di Azure.This article describes **system topics** in Azure Event Grid.

> [!NOTE]
> Questa funzionalità non è attualmente abilitata per il cloud di Azure per enti pubblici. 

## <a name="overview"></a>Panoramica
Quando si crea la prima sottoscrizione di eventi per un'origine eventi di Azure, ad esempio l'account di archiviazione di Azure, il processo di provisioning per la sottoscrizione crea una risorsa aggiuntiva di tipo **Microsoft.EventGrid/systemTopics**. Quando viene eliminata l'ultima sottoscrizione di eventi all'origine eventi di Azure, l'argomento di sistema viene eliminato automaticamente.

Argomento di sistema non è applicabile agli scenari di argomenti personalizzati, ovvero agli argomenti della griglia di eventi e ai domini griglia di eventi. 

## <a name="location"></a>Location
Per le origini eventi di Azure che si trovano in un'area/posizione specifica, l'argomento di sistema viene creato nello stesso percorso dell'origine eventi di Azure.For Azure event sources that are in a specific region/location, system topic is created in the same location as the Azure event source. Ad esempio, se si crea una sottoscrizione di eventi per un'archiviazione BLOB di Azure negli Stati Uniti orientali, l'argomento di sistema viene creato negli Stati Uniti orientali. Per le origini eventi globali di Azure, ad esempio sottoscrizioni di Azure, gruppi di risorse o Mappe di Azure, Griglia di eventi crea l'argomento di sistema in una posizione **globale.** 

## <a name="resource-group"></a>Resource group 
In generale, l'argomento di sistema viene creato nello stesso gruppo di risorse in cui si trova l'origine eventi di Azure.In general, system topic is created in the same resource group that the Azure event source is in. Per le sottoscrizioni di eventi create nell'ambito della sottoscrizione di Azure, l'argomento di sistema viene creato nel gruppo di risorse **Default-EventGrid**. Se il gruppo di risorse non esiste, Azure Event Grid lo crea prima di creare l'argomento di sistema. 

Quando si tenta di eliminare il gruppo di risorse con l'account di archiviazione, l'argomento di sistema verrà visualizzato nell'elenco delle risorse interessate.  

![Eliminare un gruppo di risorse](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Argomenti personalizzati](custom-topics.md)
- [Domini](event-domains.md)