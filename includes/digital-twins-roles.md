---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534110"
---
La tabella seguente descrive i ruoli disponibili in Gemelli digitali di Azure:

| **Ruolo** | **Descrizione** | **Identificatore** |
| --- | --- | --- |
| Amministratore dello spazio | Autorizzazioni di *CREAZIONE*, *LETTURA*, *AGGIORNAMENTO* ed *ELIMINAZIONE* per lo spazio specificato e tutti i nodi sottostanti. Autorizzazione globale. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Amministratore utenti| Autorizzazioni di *CREAZIONE*, *LETTURA*, *AGGIORNAMENTO* ed *ELIMINAZIONE* per gli utenti e gli oggetti correlati agli utenti. Autorizzazione di *LETTURA* per gli spazi. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Amministratore del dispositivo | Autorizzazioni di *CREAZIONE*, *LETTURA*, *AGGIORNAMENTO* ed *ELIMINAZIONE* per i dispositivi e gli oggetti correlati ai dispositivi. Autorizzazione di *LETTURA* per gli spazi. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Amministratore delle chiavi | Autorizzazioni di *CREAZIONE*, *LETTURA*, *AGGIORNAMENTO* ed *ELIMINAZIONE* per le chiavi di accesso. Autorizzazione di *LETTURA* per gli spazi. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Amministratore dei token |  Autorizzazioni di *LETTURA* e *AGGIORNAMENTO* per le chiavi di accesso. Autorizzazione di *LETTURA* per gli spazi. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Utente |  Autorizzazione di *LETTURA* per gli spazi, i sensori e gli utenti, inclusi gli oggetti correlati corrispondenti. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specialista del supporto tecnico |  Autorizzazione di *LETTURA* per tutti gli elementi ad eccezione delle chiavi di accesso. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Programma di installazione dei dispositivi | Autorizzazioni di *LETTURA* e *AGGIORNAMENTO* per i dispositivi e i sensori, inclusi gli oggetti correlati corrispondenti. Autorizzazione di *LETTURA* per gli spazi. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Dispositivo gateway | Autorizzazione di *CREAZIONE* per i sensori. Autorizzazione di *LETTURA* per i dispositivi e i sensori, inclusi gli oggetti correlati corrispondenti. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |