---
title: Come ripristinare un account di archiviazione eliminato
description: Informazioni su come ripristinare un account di archiviazione eliminato
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252625"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Come ripristinare un account di archiviazione eliminato

Archiviazione di Azure garantisce la resilienza dei dati tramite le repliche automatiche, ma non impedisce a utenti o codice dell'applicazione di danneggiare i dati, sia accidentalmente che intenzionalmente. La gestione della fedeltà dei dati durante le istanze dell'errore dell'applicazione o dell'utente richiede tecniche più avanzate, ad esempio la copia dei dati in un percorso di archiviazione secondario con un log di controllo.

La tabella seguente fornisce una panoramica dell'ambito del ripristino dell'account di archiviazione, a seconda della strategia di replica.

| |Archiviazione con ridondanza locale|ZRS|Archiviazione con ridondanza geografica|RA-GRS|
|---|---|---|---|---|
|Azure Resource Manager dell'account di archiviazione|Sì|Sì|Sì|Sì|
|Account di archiviazione classico|Sì|Sì|Sì|Sì|

Raccogliere le informazioni seguenti e archiviare una richiesta di supporto con supporto tecnico Microsoft:

* Nome dell'account di archiviazione
* Data di eliminazione
* Area dell'account di archiviazione
* Come è stato eliminato l'account di archiviazione?
* Quale metodo ha eliminato l'account di archiviazione? (Portale, PowerShell e così via)

Punti importanti

* In genere può essere necessario attendere fino a 15 giorni dal momento dell'eliminazione affinché il servizio di archiviazione esegua Garbage Collection, quindi il ripristino degli account di archiviazione non può essere recuperato con un contratto di servizio.
* Supporto tecnico Microsoft tenterà di ripristinare il contenitore o l'account in base al massimo sforzo e non potrà garantire il ripristino.

> [!NOTE]
> Il ripristino potrebbe non riuscire se l'account è stato ricreato. Se è già stato ricreato l'account, è necessario eliminarlo prima di poter tentare il ripristino.
