---
title: Distribuire un servizio di sincronizzazione archiviazione
description: Distribuzione della risorsa Sincronizzazione file di Azure cloud. Un servizio di sincronizzazione archiviazione. Un blocco di testo comune, condiviso tra i documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209454"
---
In questo passaggio sono necessarie le credenziali della sottoscrizione di Azure. La sottoscrizione di Azure usata può essere diversa da quella usata per StorSimple.

La risorsa principale per configurare Sincronizzazione file di Azure viene chiamata "servizio di sincronizzazione archiviazione".
È consigliabile distribuirne solo uno per tutti i server della società che sincronizzano lo stesso set di file ora o in futuro. Se sono presenti più appliance StorSimple, è possibile creare una risorsa del servizio di sincronizzazione archiviazione per ognuno di essi. Tuttavia, è consigliabile creare più servizi di sincronizzazione archiviazione se sono presenti Set distinti di server che non devono mai scambiare dati. In caso contrario, è consigliabile un solo servizio di sincronizzazione archiviazione.

Scegliere un'area di Azure per il servizio di sincronizzazione archiviazione vicino al percorso dell'ufficio. Tutte le altre risorse cloud devono essere distribuite nella stessa area.
La procedura consigliata consiste nel creare un nuovo gruppo di risorse nella sottoscrizione per ospitare le risorse di sincronizzazione e archiviazione per una gestione più semplice.

Nell'articolo seguente viene descritto come distribuire un servizio di sincronizzazione archiviazione. Seguire questa parte del documento. Nei passaggi successivi saranno disponibili collegamenti ad altre sottosezioni di questo documento.

[Informazioni su come distribuire un servizio di sincronizzazione archiviazione.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
