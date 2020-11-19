---
title: Problemi comuni relativi all'autenticazione a due fattori dell'account - Azure AD
description: Soluzioni per alcuni dei problemi più comuni di verifica a due fattori e l'account aziendale o dell'istituto di istruzione.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: 095ea62ee992fa12fc80f715f9e63725e9930595
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886210"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemi comuni con la verifica a due fattori e l'account aziendale o dell'istituto di istruzione

Alcuni problemi comuni di verifica a due fattori sembrano presentarsi con maggiore frequenza rispetto al previsto. Questo articolo è stato raccolto per descrivere le correzioni per i problemi più comuni.

L'organizzazione Azure Active Directory (Azure AD) può attivare la verifica a due fattori per l'account. Quando la verifica a due fattori è attiva, l'accesso dell'account richiede una combinazione dei dati seguenti:

- Il proprio nome utente.
- Password
- Un dispositivo mobile o un telefono

La verifica a due fattori è più sicura rispetto alla sola password, perché la verifica a due fattori richiede un _elemento che si_ _conosce_ più. Nessun pirata informatico ha il telefono fisico.

>[!Important]
>Gli amministratori possono trovare altre informazioni su come configurare e gestire l'ambiente Azure AD nella [documentazione di Azure AD](../index.yml).

Questo contenuto può essere utile per l'account aziendale o dell'Istituto di istruzione, che corrisponde all'account fornito dall'organizzazione, ad esempio dritan@contoso.com . Se si verificano problemi con la verifica a due fattori su un account Microsoft personale, ovvero un account configurato autonomamente (ad esempio, danielle@outlook.com ), vedere [attivazione o disattivazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Non si ha il dispositivo mobile con sé

Può accadere. Il dispositivo mobile è stato lasciato a casa e ora non è possibile usare il telefono per verificare chi è. Forse in precedenza è stato aggiunto un metodo alternativo per accedere al proprio account, ad esempio tramite il telefono dell'ufficio. In tal caso, è possibile usare questo metodo alternativo. Se non è mai stato aggiunto un metodo di verifica alternativo, è possibile contattare il supporto tecnico dell'organizzazione per assistenza.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Per accedere all'account aziendale o dell'istituto di istruzione con un altro metodo di verifica

1. Accedere normalmente all'account e selezionare il collegamento **Accedi in modo diverso** nella pagina **Two-factor verification** (Verifica a due fattori).

    ![Modificare il metodo di verifica dell'accesso](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se il collegamento **Sign in another way** (Accedi in altro modo) non viene visualizzato, non è stato impostato alcun altro metodo di verifica. È necessario contattare l'amministratore per ottenere assistenza sull'accesso all'account.

2. Scegliere il metodo di verifica alternativo e continuare il processo di verifica a due fattori.

## <a name="i-cant-turn-two-factor-verification-off"></a>Non è possibile disabilitare la verifica a due fattori

- Se si usa la verifica a due fattori con un account personale per un servizio Microsoft, ad esempio alain@outlook.com , è possibile [attivare e disattivare la funzionalità](https://account.live.com/proofs/Manage).

- Se si usa la verifica a due fattori con l'account aziendale o dell'Istituto di istruzione, molto probabilmente significa che l'organizzazione ha deciso di usare questa funzionalità di sicurezza aggiuntiva. Non è possibile disattivarla singolarmente.

Se non è possibile disabilitare la verifica a due fattori, il problema potrebbe dipendere anche dalle impostazioni predefinite di sicurezza applicate a livello di organizzazione. Per altre informazioni sulle impostazioni predefinite di sicurezza, vedere [Che cosa sono le impostazioni predefinite della sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>Il dispositivo è stato smarrito o rubato

In caso di smarrimento o di furto del dispositivo mobile, è possibile eseguire una delle operazioni seguenti:

- Accedere con un metodo diverso.
- Chiedere al supporto tecnico dell'organizzazione di cancellare le impostazioni.

È consigliabile consentire al supporto tecnico dell'organizzazione di verificare se il telefono è stato smarrito o rubato. Il supporto tecnico può effettuare gli aggiornamenti appropriati per l'account. Dopo che le impostazioni sono state cancellate, all'accesso successivo viene chiesto di [eseguire la registrazione per la verifica a due fattori](multi-factor-authentication-end-user-first-time.md).

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Non ricevo il codice di verifica inviato al mio dispositivo mobile

Non ricevere il codice di verifica è un problema comune. Il problema è in genere correlato al dispositivo mobile e alle relative impostazioni. Di seguito sono riportate alcune azioni che è possibile provare.

Soluzione | Informazioni aggiuntive
--------- | ------------
Usare l'app Microsoft Authenticator o i codici di verifica | Si sta ricevendo il messaggio di errore "è stato raggiunto il limite per le chiamate di verifica" o "è stato raggiunto il limite sui codici di verifica del testo" durante l'accesso. <br/><br/>Microsoft può limitare i tentativi di autenticazione ripetuti eseguiti dallo stesso utente in un breve periodo di tempo. Questa limitazione non si applica all'Microsoft Authenticator o al codice di verifica. Se sono stati raggiunti questi limiti, è possibile usare l'app Authenticator, il codice di verifica oppure provare ad accedere di nuovo in pochi minuti.
Riavviare il dispositivo mobile | A volte il dispositivo necessita solo di un aggiornamento. Quando si riavvia il dispositivo, tutti i processi e i servizi in background vengono terminati. Il riavvio arresta anche i componenti principali del dispositivo. Ogni servizio o componente viene aggiornato al riavvio del dispositivo.
Verificare la correttezza delle informazioni di sicurezza | Assicurarsi che le informazioni per il metodo di verifica di sicurezza siano accurate, in particolare i numeri di telefono. Se si specifica un numero di telefono errato, tutti gli avvisi verranno inviati a tale numero. Fortunatamente, l'utente di tale numero non sarà in grado di eseguire alcuna operazione con gli avvisi, ma si continuerà a non poter accedere al proprio account. Per assicurarsi che le informazioni siano corrette, vedere le istruzioni riportate nell'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md).
Verificare che le notifiche siano abilitate | Verificare che nel dispositivo mobile siano attivate le notifiche. Assicurarsi che siano consentite le modalità di notifica seguenti: <br/><br/> &bull; Chiamate telefoniche <br/> &bull; App di autenticazione <br/> &bull; App per la messaggistica testuale <br/><br/> Assicurarsi che queste modalità creino un avviso _visibile_ sul dispositivo.
Assicurarsi che il dispositivo riceva il segnale e di disporre di una connessione Internet | Assicurarsi che il dispositivo mobile riceva le telefonate e gli SMS. Chiedere a un amico di telefonare e inviare un SMS per assicurarsi di ricevere entrambi. Se non si riceve la chiamata o il testo, verificare prima di tutto che il dispositivo mobile sia attivato. Se il dispositivo è acceso, ma non viene ancora ricevuta la chiamata o il testo, probabilmente si è verificato un problema con la rete. È necessario comunicare con il provider. Se si verificano spesso problemi di segnale, è consigliabile usare l'[app Microsoft Authenticator](user-help-auth-app-download-install.md) sul dispositivo mobile. Questa app può generare codici di sicurezza casuali per l'accesso, senza richiedere alcun segnale di rete né alcuna connessione Internet.
Disabilitare Non disturbare | Assicurarsi di non aver abilitato la funzionalità **Non disturbare** per il dispositivo mobile. Quando questa funzionalità è abilitata, non è consentito l'invio di notifiche al dispositivo mobile. Per istruzioni su come disabilitare questa funzionalità, vedere il manuale del dispositivo mobile.
Sbloccare i numeri di telefono | Nel Stati Uniti le chiamate vocali da Microsoft provengono dai numeri seguenti: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Controllare le impostazioni relative alla batteria | Se si imposta l'ottimizzazione della batteria per arrestare le app usate meno di frequente rimanenti attive in background, è probabile che il sistema di notifica sia interessato. Provare a disattivare l'ottimizzazione della batteria per l'app di autenticazione e per l'app di messaggistica. Provare quindi ad accedere di nuovo all'account.
Disabilitare le app di sicurezza di terze parti | Alcune app per la sicurezza del telefono bloccano i messaggi di testo e le telefonate da chiamanti sconosciuti. Un'app di sicurezza potrebbe impedire al telefono di ricevere il codice di verifica. Provare a disabilitare eventuali app per la sicurezza di terze parti sul telefono, quindi richiedere l'invio di un altro codice di verifica.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Non vengono richieste le seconde informazioni di verifica

Per accedere all'account aziendale o dell'Istituto di istruzione, usare il nome utente e la password. Verranno quindi richieste le informazioni di verifica aggiuntive sulla sicurezza. Se non viene richiesto, forse non è stato ancora configurato il dispositivo. Il dispositivo mobile deve essere configurato per l'uso del metodo specifico di verifica di sicurezza aggiuntiva.

Forse non è stato ancora configurato il dispositivo. Il dispositivo mobile deve essere configurato per funzionare con un metodo di verifica di sicurezza aggiuntivo specifico. Per i passaggi per rendere il dispositivo mobile disponibile per l'uso con il metodo di verifica, vedere [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md). Se si è certi che il dispositivo o l'account non è ancora stato configurato, è possibile seguire la procedura descritta nell'articolo [configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Ho un nuovo numero di telefono che desidero aggiungere

Se si dispone di un nuovo numero di telefono, è necessario aggiornare i dettagli del metodo di verifica della sicurezza. Ciò consente alle richieste di verifica di accedere alla posizione corretta. Per aggiornare il metodo di verifica, seguire la procedura descritta nella sezione **aggiungere o modificare il numero di telefono** dell'articolo [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Ho un nuovo dispositivo mobile che desidero aggiungere

Se si dispone di un nuovo dispositivo mobile, è necessario configurarlo in modo che funzioni con la verifica a due fattori. La soluzione si articola in più passaggi:

1. Configurare il dispositivo in modo che funzioni con l'account attenendosi alla procedura descritta nell'articolo [configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md) .

1. Aggiornare le informazioni relative all'account e al dispositivo nella pagina **verifica aggiuntiva di sicurezza** . Per eseguire l'aggiornamento, eliminare il dispositivo precedente e aggiungerne uno nuovo. Per altre informazioni, vedere l'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md).

Passaggi facoltativi:

- Installare l'app Microsoft Authenticator nel dispositivo mobile attenendosi alla procedura descritta nell'articolo [scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md) .

- Abilitare la verifica a due fattori per i dispositivi attendibili seguendo la procedura illustrata nella sezione **Abilitare richieste di verifica a due fattori su un dispositivo attendibile** nell'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Si verificano problemi durante l'accesso al dispositivo mobile mentre si è in viaggio

Mentre ci si trova è in una località internazionale, potrebbe risultare più difficile usare un metodo di verifica correlato ai dispositivi mobili, ad esempio l'invio di SMS. È anche possibile che il dispositivo mobile possa comportare addebiti per il roaming. Per questa situazione, è consigliabile usare l'app Microsoft Authenticator, con l'opzione per connettersi a una Wi-Fi area sensibile. Per altre informazioni su come configurare l'app Microsoft Authenticator sul dispositivo mobile, vedere l'articolo [scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Le password per le app non funzionano

Le password per le app sostituiscono la normale password per le applicazioni desktop precedenti che non supportano la verifica a due fattori. Verificare prima che la password sia stata digitata correttamente. Se il problema persiste, provare a creare una nuova password dell'app per l'app. Per eseguire questa operazione, seguire la procedura descritta nella sezione **creare ed eliminare password di app usando il portale app personali** dell'articolo [gestire le password delle app per la verifica in due passaggi](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-off-two-factor-verification"></a>Non è possibile disattivare la verifica a due fattori

Se si usa la verifica a due fattori con l'account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, è molto probabile che l'organizzazione abbia deciso di usare questa funzionalità di sicurezza aggiuntiva. Poiché l'organizzazione ha deciso che è necessario usare questa funzionalità, non è possibile disattivarla individualmente. Se tuttavia si usa la verifica a due fattori con un account personale, ad esempio alain@outlook.com, è possibile abilitare e disabilitare la funzionalità. Per istruzioni su come controllare la verifica a due fattori per gli account personali, vedere [Abilitazione o disabilitazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se non è possibile disabilitare la verifica a due fattori, il problema potrebbe dipendere anche dalle impostazioni predefinite di sicurezza applicate a livello di organizzazione. Per altre informazioni sulle impostazioni predefinite di sicurezza, vedere [Che cosa sono le impostazioni predefinite della sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato

Se i problemi persistono anche dopo l'esecuzione delle operazioni descritte, contattare l'help desk dell'organizzazione per ottenere assistenza.

## <a name="related-articles"></a>Articoli correlati

- [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md)

- [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)