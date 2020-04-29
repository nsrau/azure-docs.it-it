---
title: Argomenti di sistema in griglia di eventi di Azure
description: Descrive gli argomenti di sistema in griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393160"
---
# <a name="system-topics-in-azure-event-grid"></a>Argomenti di sistema in griglia di eventi di Azure
Il servizio griglia di eventi di Azure crea argomenti di sistema quando si crea una prima sottoscrizione di evento per un'origine evento di Azure. Attualmente, griglia di eventi non crea argomenti di sistema per le origini degli argomenti create prima del Mar, 15, 2020. Per tutte le origini degli argomenti create in data o dopo questa data, griglia di eventi crea automaticamente gli argomenti di sistema. Questo articolo descrive gli **argomenti di sistema** in griglia di eventi di Azure.

> [!NOTE]
> Questa funzionalità non è attualmente abilitata per il cloud di Azure per enti pubblici. 

## <a name="overview"></a>Panoramica
Quando si crea la prima sottoscrizione di eventi per un'origine evento di Azure, ad esempio un account di archiviazione di Azure, il processo di provisioning per la sottoscrizione crea una risorsa aggiuntiva di tipo **Microsoft. EventGrid/systemTopics**. Quando viene eliminata l'ultima sottoscrizione di eventi dell'origine evento di Azure, l'argomento di sistema viene eliminato automaticamente.

L'argomento di sistema non è applicabile agli scenari di argomenti personalizzati, ovvero argomenti di griglia di eventi e domini di griglia di eventi. 

## <a name="location"></a>Percorso
Per le origini eventi di Azure che si trovano in un'area o in una località specifica, l'argomento di sistema viene creato nella stessa posizione dell'origine eventi di Azure. Se, ad esempio, si crea una sottoscrizione di eventi per un archivio BLOB di Azure nell'area Stati Uniti orientali, l'argomento di sistema viene creato nell'area Stati Uniti orientali. Per le origini eventi globali di Azure, ad esempio le sottoscrizioni di Azure, i gruppi di risorse o le mappe di Azure, griglia di eventi crea l'argomento di sistema in posizione **globale** . 

## <a name="resource-group"></a>Resource group 
In generale, l'argomento di sistema viene creato nello stesso gruppo di risorse in cui si trova l'origine evento di Azure. Per le sottoscrizioni di eventi create nell'ambito della sottoscrizione di Azure, l'argomento di sistema viene creato nel gruppo **di risorse default-EventGrid**. Se il gruppo di risorse non esiste, griglia di eventi di Azure lo crea prima di creare l'argomento di sistema. 

Quando si tenta di eliminare il gruppo di risorse con l'account di archiviazione, l'argomento sistema verrà visualizzato nell'elenco delle risorse interessate.  

![Eliminare un gruppo di risorse](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Argomenti personalizzati](custom-topics.md)
- [Domini](event-domains.md)