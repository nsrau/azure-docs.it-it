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
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738609"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemi comuni con la verifica a due fattori e l'account aziendale o dell'istituto di istruzione

Quando in un'organizzazione Azure Active Directory (Azure AD) viene abilitata la verifica a due fattori, l'accesso all'account aziendale o dell'istituto di istruzione richiede una combinazione di nome utente, password e dispositivo mobile o telefono cellulare. È un metodo più sicuro rispetto alla sola password poiché si basa su due forme di autenticazione: un elemento noto e un elemento che l'utente ha con sé. Con la verifica a due fattori si impedisce a utenti malintenzionati di fingere di essere l'utente autorizzato, perché anche se possiedono la password dell'utente, è improbabile che abbiano anche il dispositivo.

<center>

![Immagine concettuale dei metodi di autenticazione](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Alcuni problemi comuni di verifica a due fattori sembrano presentarsi con maggiore frequenza rispetto al previsto. In questo articolo sono stati raccolti i problemi più comuni e alcune correzioni possibili.

>[!Important]
>Gli amministratori possono trovare altre informazioni su come configurare e gestire l'ambiente Azure AD nella [documentazione di Azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Il contenuto di questo articolo fornisce informazioni solo per l'uso dell'account aziendale o dell'istituto di istruzione, ovvero l'account fornito dalla propria organizzazione, ad esempio alain@contoso.com. In caso di problemi con la verifica a due fattori e l'account Microsoft personale, ovvero l'account configurato per se stessi, ad esempio danielle@outlook.com, vedere [Abilitazione o disabilitazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Non si ha il dispositivo mobile con sé

Può accadere. Il dispositivo mobile è stato lasciato a casa e ora non è possibile usare il telefono per la verifica. Se in precedenza è stato aggiunto un altro metodo per accedere al proprio account, ad esempio il telefono dell'ufficio, dovrebbe essere possibile usarlo in questo caso. Se non è mai stato aggiunto un ulteriore metodo di verifica, sarà necessario contattare l'help desk dell'organizzazione per poter accedere al proprio account.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Per accedere all'account aziendale o dell'istituto di istruzione con un altro metodo di verifica

1. Accedere normalmente all'account e selezionare il collegamento **Accedi in modo diverso** nella pagina **Two-factor verification** (Verifica a due fattori).

    ![Modificare il metodo di verifica dell'accesso](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se il collegamento **Sign in another way** (Accedi in altro modo) non viene visualizzato, non è stato impostato alcun altro metodo di verifica. È necessario contattare l'amministratore per ottenere assistenza sull'accesso all'account.

2. Scegliere il metodo di verifica alternativo e continuare il processo di verifica a due fattori.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Il dispositivo mobile è stato smarrito o rubato

In caso di smarrimento o di furto del dispositivo mobile, è possibile accedere con un altro metodo oppure chiedere all'help desk dell'organizzazione di cancellare le impostazioni. È consigliabile comunicare all'help desk dell'organizzazione se il telefono è stato smarrito o rubato, in modo che all'account possano essere apportati gli aggiornamenti appropriati. Dopo che le impostazioni sono state cancellate, all'accesso successivo viene chiesto di [eseguire la registrazione per la verifica a due fattori](multi-factor-authentication-end-user-first-time.md).

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Il codice di verifica inviato al dispositivo mobile non è stato recapitato

Il mancato recupero del codice di verifica è un problema comune ed è in genere correlato al dispositivo mobile e alle relative impostazioni. Ecco alcune possibili soluzioni:

Soluzione | Informazioni aggiuntive
--------- | ------------
Riavviare il dispositivo mobile | A volte il dispositivo necessita solo di un aggiornamento. Il riavvio del dispositivo termina eventuali processi o servizi in background attualmente in esecuzione e che potrebbero causare problemi. Oltre all'aggiornamento dei componenti principali del dispositivo, ne viene eseguito il riavvio in caso di arresto anomalo in un determinato momento.
Verificare la correttezza delle informazioni di sicurezza | Assicurarsi che le informazioni per il metodo di verifica di sicurezza siano accurate, in particolare i numeri di telefono. Se si specifica un numero di telefono errato, tutti gli avvisi verranno inviati a tale numero. Fortunatamente, l'utente di tale numero non sarà in grado di eseguire alcuna operazione con gli avvisi, ma si continuerà a non poter accedere al proprio account. Per assicurarsi che le informazioni siano corrette, vedere le istruzioni riportate nell'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md).
Verificare che le notifiche siano abilitate | Assicurarsi che nel dispositivo mobile siano abilitate le notifiche e che sia stato selezionato un metodo di notifica che consenta alle telefonate, all'app di autenticazione e all'app di messaggistica (per gli SMS) di inviare al dispositivo mobile notifiche di avviso visibili.
Assicurarsi che il dispositivo riceva il segnale e di disporre di una connessione Internet | Assicurarsi che il dispositivo mobile riceva le telefonate e gli SMS. Chiedere a un amico di telefonare e inviare un SMS per assicurarsi di ricevere entrambi. In caso di problemi, controllare prima di tutto che il dispositivo mobile sia acceso. Se è acceso, ma non si riceve comunque la telefonata o l'SMS, probabilmente si tratta di un problema con la rete e sarà necessario contattare il provider. Se si verificano spesso problemi di segnale, è consigliabile usare l'[app Microsoft Authenticator](user-help-auth-app-download-install.md) sul dispositivo mobile. Questa app può generare codici di sicurezza casuali per l'accesso, senza richiedere alcun segnale di rete né alcuna connessione Internet.
Disabilitare Non disturbare | Assicurarsi di non aver abilitato la funzionalità **Non disturbare** per il dispositivo mobile. Quando questa funzionalità è abilitata, non è consentito l'invio di notifiche al dispositivo mobile. Per istruzioni su come disabilitare questa funzionalità, vedere il manuale del dispositivo mobile.
Sbloccare i numeri di telefono | Nel Stati Uniti le chiamate vocali da Microsoft provengono dai numeri seguenti: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Controllare le impostazioni relative alla batteria | Questo aspetto è apparentemente strano, ma se è stata configurata l'ottimizzazione della batteria con l'arresto delle app meno usate per evitare che restino attive in background, è probabile che questo abbia effetto sul sistema di notifica. Per tentare di risolvere il problema, disabilitare l'ottimizzazione della batteria per l'app di autenticazione e l'app di messaggistica, quindi provare di nuovo ad accedere all'account.
Disabilitare le app di sicurezza di terze parti | Se si dispone di un'app che protegge gli SMS o le telefonate per ridurre al minimo chiamate da sconosciuti, è possibile che tale app impedisca la ricezione del codice di verifica. Provare a disabilitare eventuali app di sicurezza di terze parti sul telefono, quindi richiedere l'invio di un altro codice di verifica.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Non vengono richieste le informazioni della seconda verifica

Se è stato effettuato l'accesso all'account aziendale o dell'istituto di istruzione con il nome utente e la password, ma non sono state richieste informazioni per la verifica di sicurezza aggiuntiva, è probabile che il dispositivo non sia stato ancora configurato. Il dispositivo mobile deve essere configurato per l'uso del metodo specifico di verifica di sicurezza aggiuntiva. Per assicurarsi di aver abilitato il dispositivo mobile e che sia disponibile per l'uso con il metodo di verifica, vedere l'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md). Se si è certi di non aver configurato il dispositivo o l'account, è possibile eseguire la configurazione seguendo la procedura illustrata nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Si dispone di un nuovo numero di telefono e si desidera aggiungerlo

Se si dispone di un nuovo numero di telefono, è necessario aggiornare i dettagli del metodo di verifica di sicurezza in modo che le richieste di verifica vengano recapitate correttamente. Per aggiornare il metodo di verifica, seguire la procedura descritta nella sezione **Aggiungere o modificare il numero di telefono** nell'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number).

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Si dispone di nuovo dispositivo mobile e si desidera aggiungerlo

Se si dispone di un nuovo dispositivo mobile, è necessario configurarlo per l'uso della verifica a due fattori. La soluzione si articola in più passaggi:

1. Configurare il dispositivo per l'uso dell'account aziendale o dell'istituto di istruzione seguendo la procedura illustrata nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

1. Aggiornare le informazioni relative all'account e al dispositivo nella pagina **Verifica aggiuntiva di sicurezza**, eliminando il dispositivo precedente e aggiungendone uno nuovo. Per altre informazioni, vedere l'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md).

Passaggi facoltativi:

- Scaricare, installare e configurare l'app Microsoft Authenticator sul dispositivo mobile seguendo la procedura descritta nell'articolo [Scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md).

- Abilitare la verifica a due fattori per i dispositivi attendibili seguendo la procedura illustrata nella sezione **Abilitare richieste di verifica a due fattori su un dispositivo attendibile** nell'articolo [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Si verificano problemi durante l'accesso al dispositivo mobile mentre si è in viaggio

Mentre ci si trova è in una località internazionale, potrebbe risultare più difficile usare un metodo di verifica correlato ai dispositivi mobili, ad esempio l'invio di SMS. È anche possibile che il dispositivo mobile possa comportare addebiti per il roaming. Per questa situazione, è consigliabile usare l'app Microsoft Authenticator, con l'opzione per connettersi a un hotspot Wi-Fi. Per altre informazioni su come scaricare, installare e configurare l'app Microsoft Authenticator sul dispositivo mobile , vedere l'articolo [Scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="i-cant-get-my-app-passwords-to-work"></a>Le password per le app non funzionano

Le password per le app sostituiscono la normale password per le applicazioni desktop precedenti che non supportano la verifica a due fattori. Verificare prima che la password sia stata digitata correttamente. Se il problema persiste, provare a creare una nuova password per l'app seguendo la procedura illustrata nella sezione **Creare ed eliminare le password per le app usando il portale App personali** nell'articolo [Gestire le password per le app per la verifica in due passaggi](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

## <a name="i-cant-turn-two-factor-verification-off"></a>Non è possibile disabilitare la verifica a due fattori

Se si usa la verifica a due fattori con l'account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, è molto probabile che l'organizzazione abbia deciso di usare questa funzionalità di sicurezza aggiuntiva. Poiché l'organizzazione ha deciso che è necessario usare questa funzionalità, non è possibile disattivarla individualmente. Se tuttavia si usa la verifica a due fattori con un account personale, ad esempio alain@outlook.com, è possibile abilitare e disabilitare la funzionalità. Per istruzioni su come controllare la verifica a due fattori per gli account personali, vedere [Abilitazione o disabilitazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se non è possibile disabilitare la verifica a due fattori, il problema potrebbe dipendere anche dalle impostazioni predefinite di sicurezza applicate a livello di organizzazione. Per altre informazioni sulle impostazioni predefinite di sicurezza, vedere [Che cosa sono le impostazioni predefinite della sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato

Se i problemi persistono anche dopo l'esecuzione delle operazioni descritte, contattare l'help desk dell'organizzazione per ottenere assistenza.

## <a name="related-articles"></a>Articoli correlati

- [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md)

- [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)
