---
title: Distribuire un servizio di sincronizzazione archiviazioneDeploy a Storage Sync Service
description: Distribuzione della risorsa cloud di Sincronizzazione file di Azure.Deploying the Azure File Sync cloud resource. Un servizio di sincronizzazione archiviazione. Blocco di testo comune, condiviso tra documenti di migrazione.
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
In questo passaggio sono necessarie le credenziali della sottoscrizione di Azure.In this step, you need your Azure subscription credentials.

La risorsa principale per configurare Sincronizzazione file di Azure viene definita "Servizio di sincronizzazione archiviazione".
È consigliabile distribuire solo uno per tutti i server della società che sincronizzano lo stesso set di file ora o in futuro. Creare più servizi di sincronizzazione archiviazione solo se si dispone di set distinti di server che non devono mai scambiare dati. (ad esempio: sincronizzare la stessa condivisione file di Azure). In caso contrario, è consigliabile utilizzare un singolo servizio di sincronizzazione archiviazione.

Scegliere un'area di Azure per il servizio di sincronizzazione archiviazione vicina alla sede dell'ufficio. Tutte le altre risorse cloud devono essere distribuite nella stessa area.
Per semplificare la gestione, creare un nuovo gruppo di risorse nella sottoscrizione che ospita le risorse di sincronizzazione e archiviazione.

L'articolo seguente descrive come distribuire un servizio di sincronizzazione archiviazione. Segui solo questa parte del documento. Ci saranno collegamenti ad altre sottosezioni di questo documento nei passaggi successivi.

[Informazioni su come distribuire un servizio di sincronizzazione archiviazione.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
