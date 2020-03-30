---
title: Come ripristinare un account di archiviazione eliminato
description: Informazioni su come ripristinare un account di archiviazione eliminatoLearn how to recover a deleted storage account
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252625"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Come ripristinare un account di archiviazione eliminato

Archiviazione di Azure offre resilienza dei dati tramite repliche automatizzate, ma non impedisce agli utenti o al codice dell'applicazione di danneggiare i dati, in modo accidentale o dannoso. Mantenere la fedeltà dei dati durante le istanze di errore dell'applicazione o dell'utente richiede tecniche più avanzate, ad esempio la copia dei dati in un percorso di archiviazione secondario con un log di controllo.

Nella tabella seguente viene fornita una panoramica dell'ambito del ripristino dell'account di archiviazione a seconda della strategia di replica.

| |Archiviazione con ridondanza locale|ZRS|Archiviazione con ridondanza geografica|RA - GRS|
|---|---|---|---|---|
|Storage Account Azure Resource Manager|Sì|Sì|Sì|Sì|
|Account di archiviazione classico|Sì|Sì|Sì|Sì|

Raccogliere le seguenti informazioni e presentare una richiesta di supporto con il supporto tecnico Microsoft:

* Nome dell'account di archiviazione
* Data di cancellazione
* Area dell'account di archiviazione
* Come è stato eliminato l'account di archiviazione?
* Quale metodo ha eliminato l'account di archiviazione? (Portale, PowerShell, ecc.)

Punti importanti

* In genere può richiedere fino a 15 giorni dal momento dell'eliminazione per il servizio di archiviazione per eseguire la procedura di Garbage Collection, pertanto il ripristino degli account di archiviazione potrebbe non essere ripristinato con un contratto di servizio.
* Il supporto tecnico Microsoft tenterà di recuperare il contenitore/account con il massimo sforzo e non sarà in grado di garantire il ripristino.

> [!NOTE]
> Il ripristino potrebbe non riuscire se l'account è stato ricreato. Se l'account è già stato ricreato, è necessario eliminarlo prima di poter eseguire il ripristino.
