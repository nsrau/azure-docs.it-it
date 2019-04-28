---
title: Domande frequenti - Content Moderator
titlesuffix: Azure Cognitive Services
description: Risposte alle domande frequenti su Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268918"
---
# <a name="frequently-asked-questions-faq"></a>Domande frequenti

### <a name="what-does-my-content-moderator-subscription-include"></a>Cosa include la sottoscrizione di Content Moderator?

Sottoscrizione di Content Moderator include l'accesso per lo strumento di revisione e le API. È possibile decidere se si vuole usare uno o le altre o entrambi, a seconda delle esigenze aziendali.

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Quali sono i limiti e le restrizioni per i contenuti che è possibile moderare con l'API?

Quando si usa l'API, le immagini devono avere una risoluzione minima di 128 pixel e dimensioni massime del file di 4 MB. La lunghezza del testo può essere al massimo di 1024 caratteri. Non è previsto alcun limite per la durata dei video.

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Cosa succede se il contenuto passato all'API per il testo o all'API per le immagini supera i limiti di dimensioni consentiti?

L'API per il testo restituisce un codice di errore che segnala che il testo è più lungo di quanto consentito. Anche l'API per le immagini restituisce un codice di errore che segnala che l'immagine non soddisfa i requisiti di dimensioni.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Le immagini, il testo o i video inviati per la moderazione vengono conservati?

Il contenuto è di proprietà dell'utente e non può essere conservato da Microsoft senza il consenso dell'utente. Microsoft adotta misure di sicurezza leader del settore per proteggere il contenuto.

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>È possibile usare Content Moderator per filtrare le immagini per lo sfruttamento sessuale minorile illegale?

 No. Tuttavia, organizzazioni qualificate possono usare il [servizio cloud PhotoDNA](https://www.microsoft.com/photodna "Servizio cloud Microsoft PhotoDNA") per filtrare questo tipo di contenuto.

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Quanti esaminare i team possono un join di utente? L'utente può cambiare team?

Un utente può solo aggiungere un team alla volta.

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Che tipo di membro del team ruoli siano supportati da strumento di revisione? ma presentano alcune differenze.

Gli strumenti di revisione consente attualmente di assegnazione dei ruoli di amministratore e revisore. Gli amministratori possono invitare altri utenti e possono accedere alle impostazioni di configurazione, mentre i revisori possono solo controllare i risultati della moderazione e aggiungere o rimuovere tag per il contenuto.

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Che cos'è un tag? Lo strumento di revisione supporta tag personalizzati?

Un tag è un codice di lettera di uno o due brevi che denota un flag di moderazione, ad esempio "a" per adulti, 'r' per pornografici e così via. Gli amministratori possono definire nuovi tag per la propria azienda in base alle esigenze.

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Quanti membri può avere un team di revisione?

È possibile avere un massimo di cinque membri del team, tra cui l'amministratore, in un team.
