---
title: "Problemi comuni relativi all'autenticazione a due fattori dell'account: Azure ADCommon problems with account two factor authentication - Azure AD"
description: Soluzioni per alcuni dei più comuni problemi di verifica a due fattori e il tuo account aziendale o dell'istituto di istruzione.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 3df79fb1e103ddfaafeb348641e675cd250f5858
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271693"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemi comuni relativi alla verifica a due fattori e all'account aziendale o dell'istituto di istruzione

Quando l'organizzazione di Azure Active Directory (Azure AD) attiva la verifica a due fattori, l'accesso all'account aziendale o dell'istituto di istruzione richiede una combinazione del nome utente, della password e di un dispositivo mobile o di un telefono. È più sicuro di una semplice password, basandosi su due forme di autenticazione: qualcosa che conosci e qualcosa che hai con te. La verifica a due fattori può aiutare a impedire agli hacker malintenzionati di fingere di essere te, perché anche se hanno la tua password, le probabilità sono che non hanno anche il tuo dispositivo.

<center>

![Immagine concettuale relativa ai metodi di autenticazione](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Ci sono alcuni problemi comuni di verifica a due fattori che sembrano accadere più frequentemente di quanto chiunque di noi vorrebbe. Abbiamo messo insieme questo articolo per risolvere i problemi più comuni e alcune possibili correzioni.

>[!Important]
>Se si è un amministratore, è possibile trovare altre informazioni su come configurare e gestire l'ambiente Azure AD nella documentazione di [Azure AD.](https://docs.microsoft.com/azure/active-directory)
>
>Questo contenuto è destinato anche all'utilizzo solo con l'account aziendale o dell'istituto di istruzione, ovvero l'account fornito dall'organizzazione, ad esempio alain@contoso.com). Se si verificano problemi con la verifica a due fattori e l'account Microsoft personale, danielle@outlook.comovvero un account configurato personalmente, vedere Attivazione o disattivazione della verifica a due fattori [per l'account Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

## <a name="i-dont-have-my-mobile-device-with-me"></a>Non ho il mio dispositivo mobile con me

Succede. Hai lasciato il tuo dispositivo mobile a casa e ora non puoi usare il telefono per verificare chi sei. Se in precedenza è stato aggiunto un altro metodo per accedere all'account, ad esempio il telefono dell'ufficio, dovrebbe essere possibile utilizzare tale metodo ora. Se non hai mai aggiunto un metodo di verifica aggiuntivo, dovrai contattare l'help desk della tua organizzazione e invitarli a tornare al tuo account.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Per accedere all'account aziendale o dell'istituto di istruzione utilizzando un altro metodo di verifica

1. Accedi al tuo account ma seleziona il link **Accedi in un altro modo** nella pagina Verifica a due **fattori.**

    ![Modificare il metodo di verifica dell'accesso](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se il collegamento **Sign in another way** (Accedi in altro modo) non viene visualizzato, non è stato impostato alcun altro metodo di verifica. È necessario contattare l'amministratore per ottenere assistenza sull'accesso all'account.

2. Scegli il metodo di verifica alternativo e continua con il processo di verifica a due fattori.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Ho perso il mio dispositivo mobile o è stato rubato

Se il dispositivo mobile è stato smarrito o è stato rubato, è possibile accedere utilizzando un metodo diverso oppure chiedere all'help desk dell'organizzazione di cancellare le impostazioni. Ti consigliamo vivamente di informare l'Help desk della tua organizzazione se il telefono è stato smarrito o rubato, in modo da poter effettuare gli aggiornamenti appropriati al tuo account. Dopo aver cancellato le impostazioni, ti verrà richiesto di [registrarti per](multi-factor-authentication-end-user-first-time.md) la verifica a due fattori al successivo accesso.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Non ricevo il codice di verifica inviato al mio dispositivo mobile

Non ottenere il codice di verifica è un problema comune ed è in genere correlato al tuo dispositivo mobile e alle sue impostazioni. Alcune possibili cose da provare:

mancanti | Informazioni di orientamento
--------- | ------------
Riavviare il dispositivo mobile | A volte il dispositivo ha solo bisogno di un aggiornamento. Il riavvio del dispositivo termina tutti i processi o i servizi in background attualmente in esecuzione che potrebbero causare problemi, insieme all'aggiornamento dei componenti principali del dispositivo, riavviandoli nel caso in cui si arrestassero a un certo punto.
Verificare che le informazioni di sicurezza siano corrette | Assicurati che le informazioni sul metodo di verifica della sicurezza siano accurate, in particolare i numeri di telefono. Se si inserisce il numero di telefono errato, tutti gli avvisi andranno a quel numero errato. Fortunatamente, l'utente non sarà in grado di eseguire alcuna operazione con gli avvisi, ma non ti aiuterà ad accedere al tuo account. Per assicurarti che le tue informazioni siano corrette, consulta le istruzioni nell'articolo Gestire le impostazioni del metodo di verifica a [due fattori.](multi-factor-authentication-end-user-manage-settings.md)
Verificare che le notifiche siano attivate | Assicurati che sul dispositivo mobile siano attivate le notifiche e di aver selezionato un metodo di notifica che consente alle chiamate telefoniche, all'app di autenticazione e all'app di messaggistica (per i messaggi di testo) di inviare notifiche di avviso visibili al dispositivo mobile.
Assicurarsi di disporre di un segnale del dispositivo e di una connessione Internet | Assicurati che le telefonate e i messaggi di testo vengano inviati al tuo dispositivo mobile. Chiedi a un amico di chiamarti e di inviarti un messaggio di testo per assicurarti di ricevere entrambi. In caso contrario, verifica innanzitutto che il dispositivo mobile sia acceso. Se il dispositivo è acceso, ma non si riceve ancora la chiamata o il testo, è molto probabile che si tratti di un problema con la rete e sarà necessario parlare con il provider. In caso di problemi relativi al segnale, è consigliabile installare e usare [l'app Microsoft Authenticator](user-help-auth-app-download-install.md) sul dispositivo mobile. L'app di autenticazione può generare codici di sicurezza casuali per l'accesso, senza richiedere alcun segnale cellulare o connessione Internet.
Disattiva Non disturbare | Assicurati di non aver attivato la funzione **Non disturbare** per il tuo dispositivo mobile. Quando questa funzionalità è attivata, le notifiche non sono autorizzate a avvisarti sul tuo dispositivo mobile. Consultare il manuale del dispositivo mobile per istruzioni su come disattivare questa funzionalità.
Sbloccare i numeri di telefono | Negli Stati Uniti, le chiamate vocali da Microsoft provengono dai seguenti numeri: 1 (866) 539 4191, 1 (855) 330 8653 e 1 (877) 668 6536.
Controllare le impostazioni relative alla batteria | Questo sembra un po 'strano sulla superficie, ma se hai impostato l'ottimizzazione della batteria per fermare le applicazioni meno usate di rimanere attivi in background, il sistema di notifica è stato molto probabilmente influenzato. Per provare a risolvere il problema, disattiva l'ottimizzazione della batteria per l'app di autenticazione e l'app di messaggistica, quindi prova ad accedere di nuovo al tuo account.
Disabilitare le app di sicurezza di terze parti | Se hai un'app che protegge i messaggi di testo o le telefonate per ridurre al minimo i chiamanti sconosciuti, potrebbero impedire la ricezione del codice di verifica. Prova a disabilitare qualsiasi app di sicurezza di terze parti sul telefono, quindi richiedi l'invio di un altro codice di verifica.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Non ricevo la richiesta delle seconde informazioni di verifica

Se hai eseguito l'accesso al tuo account aziendale o dell'istituto di istruzione utilizzando il nome utente e la password, ma non ti hanno richiesto informazioni aggiuntive sulla verifica della sicurezza, forse non hai ancora configurato il dispositivo. Il dispositivo mobile deve essere configurato per funzionare con il metodo di verifica della sicurezza aggiuntivo specifico. Per assicurarti di aver attivato il dispositivo mobile e che sia disponibile per l'uso con il metodo di verifica, consulta l'articolo Gestire le impostazioni del metodo di verifica a [due fattori.](multi-factor-authentication-end-user-manage-settings.md) Se sai di non aver configurato il tuo dispositivo o il tuo account, puoi farlo ora seguendo la procedura descritta nell'articolo [Configurare il mio account per](multi-factor-authentication-end-user-first-time.md) la verifica in due passaggi.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Ho un nuovo numero di telefono e voglio aggiungerlo

Se hai ottenuto un nuovo numero di telefono, dovrai aggiornare i dettagli del metodo di verifica della sicurezza in modo che le richieste di verifica vadano nella posizione corretta. Per aggiornare il metodo di verifica, segui i passaggi descritti nella sezione **Aggiungere o modificare il numero** di telefono dell'articolo Gestire le impostazioni del metodo di verifica a due [fattori.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Ho un nuovo dispositivo mobile e voglio aggiungerlo

Se hai ottenuto un nuovo dispositivo mobile, dovrai configurarlo per funzionare con la verifica a due fattori. Questa è una soluzione multi-step:

1. Configura il dispositivo per l'utilizzo con il tuo account aziendale o dell'istituto di istruzione seguendo la procedura descritta nell'articolo [Configurare l'account personale per](multi-factor-authentication-end-user-first-time.md) la verifica in due passaggi.

1. Aggiorna le informazioni sul tuo account e sul dispositivo nella pagina **Verifica di sicurezza aggiuntiva,** eliminando il vecchio dispositivo e aggiungendo quello nuovo. Per altre informazioni, vedere l'articolo Gestire le impostazioni del metodo di verifica a [due fattori.](multi-factor-authentication-end-user-manage-settings.md)

Passaggi facoltativi:

- Scaricare, installare e configurare l'app Microsoft Authenticator nel dispositivo mobile seguendo la procedura descritta [nell'articolo Scaricare e installare l'app Microsoft Authenticator.](user-help-auth-app-download-install.md)

- Attiva la verifica a due fattori per i tuoi dispositivi attendibili seguendo la procedura descritta nella sezione Attivare la verifica a due fattori **su un dispositivo attendibile** nell'articolo Gestire le impostazioni del metodo di verifica a due [fattori.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Si riscontrano problemi di accesso sul dispositivo mobile durante il viaggio

Potrebbe essere più difficile utilizzare un metodo di verifica relativo ai dispositivi mobili, ad esempio un messaggio di testo, mentre ci si trova in una posizione internazionale. È anche possibile che il tuo dispositivo mobile possa farti incorrere in costi di roaming. Per questa situazione, ti consigliamo di usare l'app Microsoft Authenticator, con la possibilità di connetterti a un hotspot Wi-Fi. Per altre informazioni su come scaricare, installare e configurare l'app Microsoft Authenticator nel dispositivo mobile, vedere [l'articolo Scaricare e installare l'app Microsoft Authenticator.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Non riesco a far funzionare le password della mia app

Le password delle app sostituiscono la normale password per le applicazioni desktop meno recenti che non supportano la verifica a due fattori. Verificare prima che la password sia stata digitata correttamente. Se il problema persiste, prova a creare una nuova password per l'app seguendo i passaggi descritti nella sezione Creare **ed eliminare password dell'app usando la** sezione Portale app personali dell'articolo Gestire le password [dell'app per](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) la verifica in due passaggi.

## <a name="i-cant-turn-two-factor-verification-off"></a>Non riesco a disattivare la verifica a due fattori

Se si utilizza la verifica a due fattori con l'account aziendale o dell'istituto di istruzione (ad esempio, alain@contoso.com), molto probabilmente significa che l'organizzazione ha deciso che è necessario utilizzare questa funzionalità di sicurezza aggiuntiva. Poiché l'organizzazione ha deciso che è necessario usare questa funzionalità, non è possibile disattivarla individualmente. Se, tuttavia, utilizzi la verifica a due fattori alain@outlook.comcon un account personale, ad esempio , hai la possibilità di attivare e disattivare la funzione. Per istruzioni su come controllare la verifica a due fattori per gli account personali, vedere Attivazione o disattivazione della verifica a due fattori [per l'account Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

Se non è possibile disattivare la verifica a due fattori, potrebbe anche essere a causa delle impostazioni predefinite di sicurezza applicate a livello di organizzazione. Per ulteriori informazioni sulle impostazioni predefinite di sicurezza, vedere Che cosa sono i [defulta](../fundamentals/concept-fundamentals-security-defaults.md) di sicurezza?

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato

Se hai provato questi passaggi ma continui a riscontrare problemi, contatta l'help desk della tua organizzazione per assistenza.

## <a name="related-articles"></a>Articoli correlati

- [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md)

- [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)
