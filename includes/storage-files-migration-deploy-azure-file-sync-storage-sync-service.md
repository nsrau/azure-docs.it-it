---
title: Distribuire un servizio di sincronizzazione archiviazione
description: Distribuire la risorsa Sincronizzazione file di Azure cloud, un servizio di sincronizzazione archiviazione. Un blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043124"
---
In questo passaggio sono necessarie le credenziali della sottoscrizione di Azure.

La risorsa principale da configurare per Sincronizzazione file di Azure è chiamata *servizio di sincronizzazione archiviazione* . È consigliabile distribuirne solo uno per tutti i server che sincronizzano lo stesso set di file in un secondo momento o in futuro. Creare più servizi di sincronizzazione archiviazione solo se si dispone di Set distinti di server che non devono mai scambiare dati. Ad esempio, potrebbero essere presenti server che non devono mai sincronizzare la stessa condivisione file di Azure. In caso contrario, è consigliabile un solo servizio di sincronizzazione archiviazione.

Scegliere un'area di Azure per il servizio di sincronizzazione archiviazione vicino alla propria posizione. Tutte le altre risorse cloud devono essere distribuite nella stessa area. Per semplificare la gestione, creare un nuovo gruppo di risorse nella sottoscrizione che ospita le risorse di sincronizzazione e archiviazione.

Per ulteriori informazioni, vedere la [sezione relativa alla distribuzione del servizio di sincronizzazione archiviazione](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) nell'articolo sulla distribuzione di sincronizzazione file di Azure. Seguire solo questa parte dell'articolo. Nei passaggi successivi saranno disponibili collegamenti ad altre sezioni dell'articolo.