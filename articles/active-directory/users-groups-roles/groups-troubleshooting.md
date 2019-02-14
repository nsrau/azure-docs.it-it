---
title: Correzione dei problemi di appartenenza dinamica per i gruppi - Azure Active Directory | Microsoft Docs
description: Suggerimenti per la risoluzione dei problemi di appartenenza dinamica per i gruppi di Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1fef19c555b9d2e52d4734a8f7bc5e39183e684
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169310"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Risoluzione dei problemi di appartenenza dinamica per i gruppi

**È stata configurata una regola su un gruppo, ma nessuna appartenenza viene aggiornata nel gruppo**<br/>Verificare i valori degli attributi utente della regola: sono presenti utenti che soddisfano la regola? Se non vengono rilevati problemi, attendere il popolamento del gruppo. A seconda delle dimensioni del tenant, potrebbero essere necessarie fino a 24 ore per eseguire il popolamento per la prima volta o dopo una modifica di una regola.

**È stata configurata una regola, ma i membri esistenti della regola sono stati rimossi**<br/>Si tratta di un comportamento previsto. I membri esistenti del gruppo vengono rimosse quando una regola viene abilitata o modificata. Gli utenti restituiti dalla valutazione della regola vengono aggiunti come membri al gruppo.

**Quando si aggiunge o modifica una regola non vengono visualizzate immediatamente le modifiche a livello di appartenenza. Perché?**<br/>La valutazione dell'appartenenza dedicata viene eseguita periodicamente in un processo asincrono in background. La durata del processo è determinata dal numero di utenti nella directory e dalle dimensioni del gruppo creato in base alla regola. In genere, per le directory con un numero limitato di utenti le modifiche a livello di appartenenza al gruppo verranno visualizzate nell'arco di pochi minuti. L'inserimento dei dati per le directory con un numero elevato di utenti può richiedere intervalli maggiori o uguali a 30 minuti.

**Si è verificato un errore di elaborazione delle regole**<br/>La tabella seguente elenca gli errori comuni della regola di appartenenza dinamica con la relativa risoluzione.

| Errore del parser della regola | Uso errato | Uso corretto |
| --- | --- | --- |
| Errore: l'attributo non è supportato. |(user.invalidProperty -eq "Valore") |(user.department -eq "valore")<br/><br/>Verificare che l'attributo sia incluso nell'[elenco di proprietà supportate](groups-dynamic-membership.md#supported-properties). |
| Errore: l'operatore non è supportato sull'attributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>L'operatore usato non è supportato per il tipo di proprietà, in questo esempio contains non può essere usato nel tipo booleano. Usare gli operatori corretti per il tipo di proprietà. |
| Errore: si è verificato un errore di compilazione della query. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. Operatore mancante. Usare i due predicati di join -and oppure -or<br>(user.department -eq "Vendite") -or (user.department -eq "Marketing")<br>2. Errore nell'espressione regolare usata con -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>in alternativa: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Informazioni su Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
