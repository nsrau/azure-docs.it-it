---
title: Domande frequenti e problemi noti con Identity Protection (procedura aggiornata) in Azure Active Directory | Microsoft Docs
description: Domande frequenti e problemi noti con Identity Protection (procedura aggiornata) in Azure Active Directory.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 392b7a08d9422658c5620f60e9c1caca074bc85e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452681"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Domande frequenti e problemi noti con Identity Protection (procedura aggiornata) in Azure Active Directory


## <a name="dismiss-user-risk-known-issues"></a>Ignorare il rischio utente problemi noti

**Ignora rischio utente** nella modalità classica di Identity Protection imposta **Azure AD** come attore nella cronologia del rischio dell'utente di Identity Protection (procedura aggiornata).


**Ignora rischio utente** in Identity Protection (procedura aggiornata) imposta il **\<nome dell'amministratore con un collegamento ipertestuale che punta al pannello dell'utente\>** come attore nella cronologia del rischio dell'utente di Identity Protection (procedura aggiornata).

Un problema noto corrente può causare latenze nel flusso di rimozione dei rischi utente. Se si hanno "criteri di rischio utente", questi criteri smetteranno di venire applicati agli utenti rimossi entro alcuni minuti da quando si fa clic su "Ignora rischio utente". Tuttavia, ci sono ritardi noti nell'esperienza utente di aggiornamento dello "stato del rischio" degli utenti rimossi. Come soluzione alternativa, aggiornare la pagina a livello di browser per visualizzare lo "stato del rischio" più recente per l'utente.


## <a name="risky-users-report-known-issues"></a>Gli utenti a rischio segnalare i problemi noti

Le query sul campo del **nome utente** fanno distinzione tra maiuscole e minuscole, mentre le query sul campo del **Nome** non fanno distinzione tra maiuscole e minuscole.

Attivando **Visualizza date come** viene nascosta la colonna **ULTIMO AGGIORNAMENTO RISCHIO**. Per aggiungere nuovamente la colonna fare clic su **Colonne** nella parte superiore del pannello Utenti a rischio.

**Elimina tutti gli eventi** nella modalità classica di Identity Protection imposta lo stato degli eventi di rischio come **Chiusi (risolti)**.


## <a name="risky-sign-ins-report-known-issues"></a>Report accessi a rischio problemi noti

**Risolvi** su un evento di rischio imposta lo stato su **Utenti che hanno superato la MFA secondo i criteri basati sul rischio**.


## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Cause che impediscono di impostare i livelli di rischio per ogni evento di rischio.

I livelli di rischio in Identity Protection si basano sulla precisione del rilevamento e si avvalgono della tecnologia di Machine Learning. Per personalizzare ciò che viene presentato agli utenti, l'amministratore può includere o escludere determinati utenti o gruppi dai criteri di rischio utente e di rischio di accesso.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Cause per cui la posizione di accesso non corrisponde a quella in cui l'utente ha effettivamente eseguito l'accesso.

Il mapping di georilevazione IP costituisce una sfida a livello di settore. Se si ritiene che la posizione elencata nel report degli accessi non corrisponda alla posizione effettiva, rivolgersi all'assistenza. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Come funzionano i meccanismi dei feedback in Identity Protection?

**Conferma compromesso** (in fase di accesso) - indica ad Azure Active Directory Identity Protection che l'accesso non è stato eseguito dal proprietario dell'identità e indica un compromesso.

- Dopo aver ricevuto il feedback, lo stato di rischio di accesso e di rischio utente verrà modificato in **Confermato compromesso** e il livello di rischio in **Elevato**.

- Inoltre, offriamo le informazioni ai sistemi di Machine Learning per futuri miglioramenti nella valutazione dei rischi.

    > [!NOTE]
    > Se l'utente è già stato salvaguardato, non fare clic su **Conferma compromesso** poiché modificherà lo stato di rischio di accesso e di rischio utente in **Confermato compromesso**, e il livello di rischio in **Elevato**.

**Conferma sicuro** (in fase di accesso) - indica ad Azure Active Directory Identity Protection che l'accesso è stato eseguito dal proprietario dell'identità e non indica un compromesso.

- Dopo aver ricevuto il feedback, lo stato di rischio di accesso (non di rischio utente) verrà modificato in **Confermato sicuro** e il livello di rischio in **-**.

- Inoltre, offriamo le informazioni ai sistemi di Machine Learning per futuri miglioramenti nella valutazione dei rischi.

    > [!NOTE]
    > Se si ritiene che l'utente non sia compromesso, usare **Ignora rischio utente** sul livello utente anziché **Confermato sicuro** a livello di accesso. **Ignora rischio utente** sul livello dell'utente chiude il rischio utente e tutti gli accessi a rischio e gli eventi di rischio precedenti.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Cause per cui viene visualizzato un punteggio di rischio utente con un valore basso (o superiore), anche se non vengono visualizzati gli accessi rischiosi o gli eventi di rischio in Identity Protection.

Considerato il fatto che il rischio utente è di natura cumulativo e non ha scadenza, un utente potrebbe avere un rischio utente basso o superiore anche se non vi sono recenti accessi rischiosi o eventi di rischio visualizzati in Identity Protection. Questo problema può verificarsi se l'unica attività dannosa di un utente ha avuto luogo al di fuori dell'intervallo di tempo per cui si archiviano i dettagli degli accessi rischiosi e degli eventi di rischio. Il rischio utente non ha scadenza poiché gli attori dannosi sono noti rimanere nell'ambiente dei clienti per oltre 140 giorni con un'identità compromessa prima di lanciare l'attacco. I clienti possono esaminare la sequenza temporale del rischio utente per comprendere il motivo per cui un utente è a rischio, recandosi a: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Cause per cui un accesso presenta un punteggio Elevato di "rischio di accesso (aggregato)" quando l'attività di rilevamento associata presenta un rischio basso o medio.

Il punteggio elevato di rischio aggregato potrebbe essere basato su altre funzionalità di accesso o sul fatto che sono stati generati più rilevamenti per tale accesso. E viceversa, un accesso potrebbe avere un rischio di accesso (aggregato) di livello Medio anche se i rilevamenti associati a tale accesso sono di rischio Elevato. 
