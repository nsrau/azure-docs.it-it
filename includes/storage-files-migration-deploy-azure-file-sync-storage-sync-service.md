---
title: Distribuire un servizio di sincronizzazione archiviazione
description: Distribuzione della risorsa Sincronizzazione file di Azure cloud. Un servizio di sincronizzazione archiviazione. Un blocco di testo comune, condiviso tra i documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124733"
---
In questo passaggio sono necessarie le credenziali della sottoscrizione di Azure.

La risorsa principale per configurare Sincronizzazione file di Azure viene chiamata "servizio di sincronizzazione archiviazione".
È consigliabile distribuirne solo uno per tutti i server della società che sincronizzano lo stesso set di file ora o in futuro. È possibile creare più servizi di sincronizzazione archiviazione solo se si dispone di Set distinti di server che non devono mai scambiare dati. (ad esempio, sincronizzare la stessa condivisione file di Azure). In caso contrario, è consigliabile un solo servizio di sincronizzazione archiviazione.

Scegliere un'area di Azure per il servizio di sincronizzazione archiviazione vicino al percorso dell'ufficio. Tutte le altre risorse cloud devono essere distribuite nella stessa area.
Per semplificare la gestione, creare un nuovo gruppo di risorse nella sottoscrizione che ospita le risorse di sincronizzazione e archiviazione.

Nell'articolo seguente viene descritto come distribuire un servizio di sincronizzazione archiviazione. Seguire questa parte del documento. Nei passaggi successivi saranno disponibili collegamenti ad altre sottosezioni di questo documento.

[Informazioni su come distribuire un servizio di sincronizzazione archiviazione.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
