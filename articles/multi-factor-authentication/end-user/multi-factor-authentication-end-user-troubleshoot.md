---
title: Risoluzione dei problemi di verifica in due passaggi | Microsoft Docs
description: Questo documento fornisce informazioni sulle operazioni da eseguire se si verifica un problema con Azure multi-Factor Authentication.
services: multi-factor-authentication
keywords: client di multi-factor authentication, problema di autenticazione, ID di correlazione
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 10b63a859c67b965734e32ec979b26a1ea6cc516
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---
# <a name="having-trouble-with-two-step-verification"></a>Problemi con la verifica in due passaggi
In questo articolo vengono descritti alcuni problemi che possono verificarsi con la verifica in due passaggi. Se il problema che si verifica non è incluso in questo articolo, inserire un feedback dettagliato nella sezione dei commenti ai fini di un miglioramento.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Il telefono è stato perso o rubato
Esistono due modi per rientrare nel proprio account. Il primo consiste nell'accedere con il numero di telefono di autenticazione alternativo, se è stato configurato. Il secondo consiste nel chiedere all'amministratore di cancellare le impostazioni.

Se il telefono è stato perso o rubato, è consigliabile richiedere all'amministratore di reimpostare le password dell'app e cancellare eventuali dispositivi memorizzati. Se l'amministratore non sa come eseguire questa attività, può fare riferimento a questo articolo: [Manage users and devices](../multi-factor-authentication-manage-users-and-devices.md) (Gestire utenti e dispositivi).

### <a name="use-an-alternate-phone-number"></a>Usare un numero di telefono alternativo
Se sono state impostate più opzioni di verifica, tra cui un numero di telefono secondario o un'app di autenticazione in un altro dispositivo, è possibile usarle per accedere.

Per accedere tramite il numero di telefono alternativo, seguire questa procedura:

1. Accedere come si farebbe normalmente.
2. Quando viene richiesto di verificare l'account, scegliere **Usa un'opzione di verifica diversa**.
   
    ![Verifica diversa](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. Selezionare il numero di telefono cui è possibile accedere.
   
    ![Telefono alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. Dopo essere rientrati nel proprio account, [gestire le impostazioni](multi-factor-authentication-end-user-manage-settings.md) per modificare il numero di telefono di autenticazione.

> [!IMPORTANT]
> È importante configurare un numero di telefono di autenticazione secondario. Se il numero di telefono principale e l'app per dispositivi mobili si trovano nello stesso dispositivo, è necessaria una terza opzione in caso di furto o smarrimento.   

### <a name="clear-your-settings"></a>Cancellare le impostazioni
Se non è stato configurato un numero di telefono di autenticazione secondario, è necessario richiedere assistenza contattando l'amministratore. Chiedere all'amministratore di cancellare le impostazioni in modo che all'accesso successivo verrà richiesto di [reimpostare l'account](multi-factor-authentication-end-user-first-time.md).

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Non ricevo messaggi o chiamate sul telefono
Esistono diversi motivi per cui si tenta di effettuare l'accesso, ma non si ricevono messaggi o telefonate. Se nel passato messaggi e chiamate venivano ricevuti senza problemi, si tratta molto probabilmente di un problema del provider del servizio telefonico, non dell'account. Assicurarsi di avere un buon segnale e, se si sta tentando di ricevere un SMS, assicurarsi che il telefono e il piano di servizio supportino gli SMS.

Se sono trascorsi diversi minuti e non sono arrivati né SMS né chiamate, il modo più rapido per accedere al proprio account è quello di tentare con un'opzione diversa.

1. Selezionare **Usa un'opzione di verifica diversa** nella pagina in cui si è in attesa della verifica.
   
    ![Verifica diversa](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Selezionare il metodo di recapito o il numero di telefono che si vuole usare.
   
    Se sono stati ricevuti più codici di verifica, usare il più recente.

Se non è stato configurato un altro metodo, contattare l'amministratore e chiedere di cancellare le impostazioni. Al tentativo di accesso successivo, verrà richiesto di [riconfigurare l'autenticazione a più fattori](multi-factor-authentication-end-user-first-time.md).

Se si hanno spesso ritardi a causa della scarsa qualità del segnale, è consigliabile usare l'[app Microsoft Authenticator](microsoft-authenticator-app-how-to.md) sullo smartphone. L'app può generare codici di sicurezza casuali che consentono di accedere e che non richiedono segnale telefonico o connessione Internet.

## <a name="app-passwords-are-not-working"></a>Le password dell'app non funzionano
Assicurarsi prima di tutto di avere immesso correttamente la password dell'app. Se non funziona ancora, provare ad accedere e [creare una password per l'app](multi-factor-authentication-end-user-app-passwords.md).  Se il problema persiste, contattare l'amministratore, richiedere l'[eliminazione delle password esistenti per l'app](../multi-factor-authentication-manage-users-and-devices.md) e creare una nuova password.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato.
Se queste procedure di risoluzione dei problemi non sono state utili, contattare l'amministratore o la persona che ha configurato l'autenticazione a più fattori sul dispositivo per assistenza.

È anche possibile pubblicare una domanda nel [forum di Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o [contattare il supporto](https://support.microsoft.com/contactus) che risponderà non appena possibile.

Se si contatta il supporto, includere le informazioni seguenti:

* **ID utente**: qual è l'indirizzo di posta elettronica con cui si è tentato di accedere?
* **Descrizione generale dell'errore**: qual è il messaggio di errore esatto che viene visualizzato?  Se non è stato visualizzato alcun messaggio di errore, descrivere dettagliatamente il comportamento imprevisto riscontrato.
* **Pagina** : la pagina che si stava consultando quando è stato visualizzato l'errore (includere l'URL).
* **Codice di errore** : l'esatto codice di errore visualizzato.
* **ID sessione** : l'esatto ID di sessione visualizzato.
* **ID correlazione** : il codice ID di correlazione generato quando è stato visualizzato l'errore.
* **Data e ora** : la data e l'ora in cui è stato visualizzato l'errore (includere il fuso orario).

Molte di queste informazioni sono reperibili nella pagina di accesso. Quando non si verifica l'accesso in tempo, selezionare **Visualizza dettagli**.

![Dettagli errore di accesso](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Queste informazioni consentiranno di risolvere il problema nel più breve tempo possibile.

## <a name="related-topics"></a>Argomenti correlati
* [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)  
* [Domande frequenti sull'applicazione Microsoft Authenticator](microsoft-authenticator-app-faq.md)


