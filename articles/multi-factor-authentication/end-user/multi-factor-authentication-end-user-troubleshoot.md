---
title: Risoluzione dei problemi di verifica in due passaggi | Microsoft Docs
description: Questo documento fornisce informazioni sulle operazioni da eseguire se si verifica un problema con Azure multi-Factor Authentication.
services: multi-factor-authentication
keywords: client di multi-factor authentication, problema di autenticazione, ID di correlazione
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: barlan
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: 840f4f9e3616f5a60ebbbb0ad33cee9a67958989
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="get-help-with-two-step-verification"></a>Informazioni sulla verifica in due passaggi
Questo articolo contiene le risposte alle domande più comuni degli utenti sulla verifica in due passaggi.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Perché è necessario eseguire la verifica in due passaggi? È possibile disattivarla?

La verifica in due passaggi è una funzionalità di sicurezza che l'organizzazione ha scelto di usare per proteggere gli account. È un metodo più sicuro rispetto alla sola password perché si basa su due forme di autenticazione: un elemento noto e un elemento che l'utente ha con sé. L'elemento noto è la password. L'elemento che l'utente normalmente ha con sé è un telefono o un dispositivo. Quando l'account è protetto dalla verifica in due passaggi, un utente malintenzionato che fosse riuscito a ottenere la password dell'utente non potrà comunque accedere perché non avrà accesso anche al telefono.

Microsoft offre la funzionalità di verifica in due passaggi e l'organizzazione sceglie di usarla. L'utente non può rifiutare di usarla se il supporto tecnico aziendale la richiede, proprio come non è possibile rifiutare di usare una password per proteggere il proprio account.

Se la verifica in due passaggi è attivata per l'account Microsoft personale e si desidera modificare le impostazioni, vedere invece [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="i-dont-have-my-phone-with-me-today"></a>Telefono momentaneamente non disponibile

A volte si lascia il telefono a casa, ma si ha lo stesso la necessità di accedere alle risorse di lavoro. Il primo tentativo da effettuare è l'accesso con un altro metodo di verifica. Quando è stata eseguita la registrazione per la verifica in due passaggi, è stato impostato più di un numero di telefono? Per provare ad accedere con un metodo diverso, seguire questa procedura:

1. Accedere come si farebbe normalmente.
2. Quando si apre la pagina della verifica in due passaggi scegliere **Usa un'opzione di verifica diversa**.

   ![Verifica diversa](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Selezionare l'opzione di verifica che si intende usare.
4. Procedere con la verifica in due passaggi.

Se il collegamento **Usa un'opzione di verifica diversa** non viene visualizzato significa che al momento della registrazione per la verifica in due passaggi non sono stati impostati metodi alternativi. Contattare il supporto tecnico aziendale per ottenere assistenza per accedere all'account. Dopo l'accesso, assicurarsi di [gestire le impostazioni](multi-factor-authentication-end-user-manage-settings.md) per aggiungere metodi di verifica diversi per la prossima volta.

Se il collegamento **Usa un'opzione di verifica diversa** viene visualizzato, ma non si riesce comunque ad accedere ai metodi alternativi, contattare il supporto tecnico aziendale per ottenere assistenza per accedere all'account.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Telefono perso o nuovo numero
Esistono due modi per rientrare nel proprio account. Il primo consiste nell'accedere con il numero di telefono di autenticazione alternativo, se è stato configurato. Il secondo consiste nel chiedere al supporto tecnico aziendale di cancellare le impostazioni.

Se il telefono è stato perso o rubato, è anche consigliabile informare il supporto tecnico aziendale in modo che possa reimpostare le password dell'app e cancellare eventuali dispositivi memorizzati.

### <a name="use-an-alternate-phone-number"></a>Usare un numero di telefono alternativo
Se sono state impostate più opzioni di verifica, tra cui un numero di telefono secondario o un'app di autenticazione in un altro dispositivo, è possibile usarle per accedere.

Per accedere tramite il numero di telefono alternativo, seguire questa procedura:

1. Accedere come si farebbe normalmente.
2. Quando viene richiesto di verificare l'account, scegliere **Usa un'opzione di verifica diversa**.

   ![Verifica diversa](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Selezionare il numero di telefono o il dispositivo a cui si ha accesso.
4. Dopo essere rientrati nel proprio account, [gestire le impostazioni](multi-factor-authentication-end-user-manage-settings.md) per modificare il numero di telefono di autenticazione.

### <a name="clear-your-settings"></a>Cancellare le impostazioni
Se non è stato configurato un numero di telefono di autenticazione secondario, contattare il supporto tecnico aziendale per ottenere assistenza. Chiedere la cancellazione delle impostazioni in modo che al prossimo accesso verrà chiesto di eseguire di nuovo la [registrazione per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Non ricevo messaggi o chiamate sul telefono
Esistono diversi motivi per cui si tenta di effettuare l'accesso, ma non si ricevono messaggi o telefonate. Se nel passato messaggi e chiamate venivano ricevuti senza problemi, si tratta molto probabilmente di un problema del provider del servizio telefonico, non dell'account. Verificare di avere un buon segnale e, se si sta tentando di ricevere un SMS, controllare che questa funzionalità sia supportata. Chiedere a un amico di provare a chiamare o a inviare un SMS.

Se sono trascorsi diversi minuti e non sono arrivati né SMS né chiamate, il modo più rapido per accedere al proprio account è quello di tentare con un'opzione diversa.

1. Selezionare **Usa un'opzione di verifica diversa** nella pagina in cui si è in attesa della verifica.

    ![Verifica diversa](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Selezionare il metodo di recapito o il numero di telefono che si vuole usare.

    Se sono stati ricevuti più codici di verifica, usare il più recente.

Se non è stato configurato un altro metodo, contattare il supporto tecnico aziendale e chiedere di cancellare le impostazioni. Al tentativo di accesso successivo, verrà richiesto di [riconfigurare l'autenticazione a più fattori](multi-factor-authentication-end-user-first-time.md).

Se si hanno spesso ritardi a causa della scarsa qualità del segnale, è consigliabile usare l'[app Microsoft Authenticator](microsoft-authenticator-app-how-to.md) sullo smartphone. L'app può generare codici di sicurezza casuali che consentono di accedere e che non richiedono segnale telefonico o connessione Internet.

## <a name="app-passwords-are-not-working"></a>Le password dell'app non funzionano
Assicurarsi prima di tutto di avere immesso correttamente la password dell'app. La password dell'app generata sostituisce la normale password, ma solo per le applicazioni desktop precedenti che non supportano la verifica in due passaggi. Se ancora non funziona, provare ad accedere e [creare una nuova password dell'app](multi-factor-authentication-end-user-app-passwords.md).  Se il problema persiste, contattare il supporto tecnico aziendale e chiedere di [eliminare le password dell'app esistenti](../multi-factor-authentication-manage-users-and-devices.md) in modo da poter creare una nuova password.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato.
Se dopo aver provato queste procedure i problemi persistono, contattare il supporto tecnico aziendale per assistenza.

## <a name="related-topics"></a>Argomenti correlati
* [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)  
* [Domande frequenti sull'applicazione Microsoft Authenticator](microsoft-authenticator-app-faq.md)
