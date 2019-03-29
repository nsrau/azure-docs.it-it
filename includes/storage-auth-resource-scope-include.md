---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622638"
---
Prima di assegnare un ruolo RBAC a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate indicano che è sempre consigliabile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in corrispondenza del quale è possibile definire l'ambito di accesso alle risorse blob e di Accodamento di Azure, inizia con l'ambito più ristretto:

- **Un singolo contenitore.** In questo ambito, un'entità di sicurezza ha accesso a tutti i BLOB nel contenitore, nonché le proprietà del contenitore e i metadati.
- **Una singola coda.** In questo ambito, un'entità di sicurezza può accedere ai messaggi nella coda, nonché le proprietà della coda e i metadati.
- **L'account di archiviazione.** In questo ambito, un'entità di sicurezza ha accesso a tutti i contenitori e i BLOB o per tutte le code e i relativi messaggi.
- **Il gruppo di risorse.** In questo ambito, un'entità di sicurezza ha accesso a tutti i contenitori o le code in tutti gli account di archiviazione nel gruppo di risorse.
- **La sottoscrizione.** In questo ambito, un'entità di sicurezza ha accesso a tutti i contenitori o le code in tutti gli account di archiviazione in tutti i gruppi di risorse nella sottoscrizione.

> [!IMPORTANT]
> Se l'abbonamento include uno spazio dei nomi di Azure DataBricks, i ruoli assegnati nell'ambito della sottoscrizione non potranno concedere l'accesso a dati blob e coda.