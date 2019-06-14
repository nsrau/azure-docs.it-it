---
title: Reimpostare le password usando le informazioni di sicurezza - Azure Active Directory | Microsoft Docs
description: Come reimpostare la password se la si dimentica, usando la verifica in due passaggi e le informazioni di sicurezza.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0d0fdf4e7df0401692eb026d55fe1a7c9322f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473318"
---
# <a name="reset-your-work-or-school-password"></a>Reimpostare la password aziendale o dell’istituto di istruzione

Se si dimentica la password, la password non è mai stata inviata dal supporto tecnico dell'azienda oppure l'account è stato bloccato, è possibile usare le informazioni di sicurezza e il dispositivo mobile per reimpostare la password.

>[!NOTE]
>Se si conosce la password e si vuole solo modificarla, è possibile passare alla procedura [Modificare la password](#how-to-change-your-password) in questo articolo.<br><br>
>Se si sta provando ad accedere nuovamente a un account personale come ad esempio Xbox, hotmail.com o outlook.com, provare i suggerimenti nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Come reimpostare o sbloccare la password per un account aziendale o dell'istituto di istruzione

Se risulta impossibile accedere all'account Azure Active Directory (Azure AD), la causa potrebbe essere una delle seguenti:

- La password non funziona e si vuole reimpostarla; oppure

- Si conosce la password, ma l'account è bloccato ed è necessario sbloccarlo.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Per reimpostare la password e accedere nuovamente all'account

1. Nella schermata **Immettere la password** selezionare **Password dimenticata**.

2. Nella schermata **Tornare all'account** digitare l'**ID utente** dell'account aziendale o dell'istituto di istruzione (ad esempio, l'indirizzo di posta elettronica), dimostrare di non essere un robot immettendo i caratteri visualizzati e quindi selezionare  **Avanti**.

   ![Schermata Tornare all'account](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Se l'amministratore non ha abilitato la possibilità di reimpostare la password, verrà visualizzato un collegamento **Contattare l'amministratore** anziché la schermata **Tornare all'account**. Questo collegamento consente di contattare l'amministratore per reimpostare la password, tramite posta elettronica o mediante un portale Web.

3. Scegliere uno dei metodi seguenti per verificare la propria identità e modificare la password. A seconda di come l'amministratore ha configurato l'organizzazione, potrebbe essere necessario eseguire il processo una seconda volta, aggiungendo le informazioni per un secondo passaggio di verifica.

    ![Tornare all'account, passaggio di verifica 1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >A seconda di come l'amministratore ha configurato l'organizzazione, alcune di queste opzioni di verifica potrebbero non essere disponibili. È necessario avere precedentemente impostato il dispositivo mobile per la verifica usando almeno uno di questi metodi.<br><br>Inoltre, la nuova password potrebbe dover soddisfare determinati requisiti di complessità. Le password complesse in genere contengono da 8 a 16 caratteri, inclusi caratteri maiuscoli e minuscoli, almeno un numero e almeno un carattere speciale.

- **Reimpostare la password usando l'indirizzo e-mail.** Viene inviato un messaggio di posta elettronica all'indirizzo di posta elettronica configurato in precedenza nella verifica in due passaggi o nelle informazioni di sicurezza. Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un indirizzo di posta elettronica nell'articolo [Configurare le informazioni di sicurezza per l'uso della posta elettronica (anteprima)](security-info-setup-email.md). Se non si usano ancora le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un indirizzo di posta elettronica nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md). 

    1. Selezionare **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**, quindi selezionare **Indirizzo di posta elettronica**.

    2. Digitare nella casella il codice di verifica contenuto nel messaggio di posta elettronica e quindi selezionare **Avanti**.
    
    3. Digitare e confermare la nuova password e quindi selezionare **Fine**.

- **Reimpostare la password usando un SMS.** Viene inviato un SMS al numero di telefono configurato in precedenza nelle informazioni di sicurezza. Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione degli SMS nell'articolo [Configurare le informazioni di sicurezza per l'uso di SMS (anteprima)](security-info-setup-text-msg.md). Se non si usano ancora le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione degli SMS nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

    1. Selezionare **Invia SMS sul telefono cellulare**, digitare il numero di telefono e quindi selezionare **SMS**.

    2. Digitare nella casella il codice di verifica contenuto nel messaggio SMS e quindi selezionare **Avanti**.

    3. Digitare e confermare la nuova password e quindi selezionare **Fine**.

- **Reimpostare la password usando un numero di telefono.** Viene inviato un SMS al numero di telefono configurato in precedenza nelle informazioni di sicurezza. Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un numero di telefono nell'articolo [Configurare le informazioni di sicurezza per l'uso di una chiamata telefonica (anteprima)](security-info-setup-phone-number.md). Se non si usano ancora le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un numero di telefono nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

    1. Selezionare **Chiama telefono cellulare**, digitare il numero di telefono e quindi selezionare **Chiama**.

    2. Rispondere alla chiamata, seguire le istruzioni per verificare la propria identità e quindi selezionare **Avanti**.

    3. Digitare e confermare la nuova password e quindi selezionare **Fine**.

- **Reimpostare la password usando domande di sicurezza.** Viene visualizzato l'elenco delle domande di sicurezza impostate nelle informazioni di sicurezza. Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione delle domande di sicurezza nell'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza predefinite (anteprima)](security-info-setup-questions.md). Se non si usano ancora le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione delle domande di sicurezza nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

    1. Selezionare **Rispondi alle domande di sicurezza**, rispondere alle domande e quindi selezionare **Avanti**.

    2. Digitare e confermare la nuova password e quindi selezionare **Fine**.

- **Reimpostare la password usando una notifica dall'app di autenticazione.** Viene inviata una notifica di approvazione all'app di autenticazione. Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un'app di autenticazione per l'invio di una notifica nell'articolo [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione (anteprima)](security-info-setup-auth-app.md). Se non si usano ancora le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un'app di autenticazione per l'invio di una notifica nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

    1. Selezionare **Approva una notifica nell'app di autenticazione**, quindi selezionare **Invia notifica**.

    2. Approvare l'accesso dall'app di autenticazione.

    3. Digitare e confermare la nuova password e quindi selezionare **Fine**.

- **Reimpostare la password usando un codice dall'app di autenticazione.** Accetta un codice casuale fornito dall'app di autenticazione. Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un'app di autenticazione per l'invio di un codice nell'articolo [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione (anteprima)](security-info-setup-auth-app.md). Se non si usano ancora le informazioni di sicurezza, è possibile trovare altre informazioni sulla configurazione di un'app di autenticazione per l'invio di un codice nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

  1. Selezionare **Immetti un codice dall'app di autenticazione**, quindi selezionare **Invia notifica**.

  2. Aprire l'app di autenticazione, digitare nella casella il codice di verifica per l'account e quindi selezionare **Avanti**.

  3. Digitare e confermare la nuova password e quindi selezionare **Fine**.

  4. Una volta visualizzato il messaggio che indica che la password è stata reimpostata, è possibile accedere all'account con la nuova password.
        
     Se non è comunque possibile accedere all'account, contattare l'amministratore dell'organizzazione per ricevere assistenza.

Dopo aver reimpostato la password, è possibile che si riceva un messaggio di posta elettronica di conferma proveniente da un account come "Microsoft per conto di \<*organizzazione*>." Se si riceve un messaggio di questo tipo ma non è stata reimpostata la password di recente, è necessario contattare immediatamente l'amministratore dell'organizzazione.

## <a name="how-to-change-your-password"></a>Come cambiare la password

Se si vuole semplicemente modificare la password, è possibile eseguire questa operazione tramite il portale di Office 365, il pannello di accesso di Azure o la pagina di accesso di Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Per cambiare la password usando il portale di Microsoft Office 365

Usare questo metodo se in genere si accede alle app tramite il portale di Office:

1. Accedere al proprio [account Office 365](https://portal.office.com) con la password esistente.

2. Selezionare il profilo in alto a destra e quindi selezionare **Visualizza account**.

3. Selezionare **Sicurezza e privacy** > **Password**.

4. Digitare la password precedente, creare e confermare la nuova password, quindi selezionare **Invia**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Per cambiare la password dal pannello di accesso di Azure

Usare questo metodo se in genere si accede alle app dal pannello di accesso di Azure (MyApps):

1. Accedere al [pannello di accesso di Azure](https://myapps.microsoft.com/) usando la password esistente.

2. Selezionare il profilo in alto a destra e quindi selezionare **Profilo**.

3. Selezionare **Modifica password**.

4. Digitare la password precedente, creare e confermare la nuova password, quindi selezionare **Invia**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Per cambiare la password all'accesso a Windows

Se l'amministratore ha attivato la funzionalità, è possibile visualizzare un collegamento **Reimposta password** nella schermata di accesso di Windows 7, Windows 8, Windows 8.1 o Windows 10.

1. Selezionare il collegamento **Reimposta password** per avviare il processo di reimpostazione della password senza dover usare la normale esperienza basata sul Web.

2. Confermare l'ID utente e selezionare **Avanti**.

3. Selezionare e confermare un metodo di contatto per la verifica. Se necessario, scegliere una seconda opzione di verifica diversa da quella precedente, compilando le informazioni necessarie.

4. Nella pagina **Crea una nuova password** digitare e confermare la nuova password, quindi selezionare **Avanti**.

    Le password complesse in genere contengono da 8 a 16 caratteri, inclusi caratteri maiuscoli e minuscoli, almeno un numero e almeno un carattere speciale.

5. Una volta visualizzato il messaggio che indica che la password è stata reimpostata, selezionare **Fine**.

    Se non è comunque possibile accedere all'account, contattare l'amministratore dell'organizzazione per ricevere assistenza.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

|Problema|Descrizione|Soluzione|
| --- | --- | --- |
|Quando si tenta di modificare la password, viene visualizzato un errore. |La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare.| Riprovare con una password più complessa.|
|Dopo avere immesso l'ID utente, viene visualizzata una pagina in cui è indicato "Contattare l'amministratore".|Microsoft ha stabilito che la password dell'account utente è gestita dall'amministratore in un ambiente locale. Di conseguenza, non è possibile reimpostare la password dal collegamento "Problemi di accesso all'account?". |Contattare l'amministratore per ulteriore assistenza.|
|Dopo aver immesso l'ID utente, viene visualizzato il messaggio di errore "Account non abilitato per la reimpostazione della password".|L'amministratore non ha configurato l'account in modo da consentire la reimpostazione della password.|L'amministratore non ha attivato la reimpostazione della password per l'organizzazione dal collegamento "Problemi di accesso all'account?" o non consente all'utente di usare la funzionalità.<br><br> Per reimpostare la password, è necessario selezionare il collegamento "Contattare l'amministratore" e inviare un messaggio di posta elettronica all'amministratore dell'azienda per richiedere la reimpostazione della password.|
|Dopo aver immesso l'ID utente, viene visualizzato l'errore "Impossibile verificare l'account".|Durante il processo di accesso non è stato possibile verificare le informazioni dell'account.|Questo messaggio potrebbe essere visualizzato per due motivi.<br><br>1. L'amministratore ha attivato la reimpostazione della password per l'organizzazione, ma l'utente non ha ancora eseguito la registrazione per l'uso del servizio. Per registrare la reimpostazione della password, vedere uno degli articoli seguenti, basati sul proprio metodo di verifica: [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione (anteprima)](security-info-setup-auth-app.md), [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche (anteprima)](security-info-setup-phone-number.md), [Configurare le informazioni di sicurezza per l'uso di SMS (anteprima)](security-info-setup-text-msg.md), [Configurare le informazioni di sicurezza per l'uso della posta elettronica (anteprima)](security-info-setup-email.md) o [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza (anteprima)](security-info-setup-questions.md).<br><br>2. L'amministratore non ha ancora attivato la reimpostazione della password per l'organizzazione. In questa situazione, è necessario selezionare il collegamento "Contattare l'amministratore" per inviare un messaggio di posta elettronica all'amministratore e richiedere la reimpostazione della password.|

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle informazioni di sicurezza sono disponibili nell'articolo [Panoramica delle informazioni di sicurezza (anteprima)](user-help-security-info-overview.md).

- Informazioni sulla verifica in due passaggi sono disponibili nell'articolo [Panoramica della verifica in due passaggi](user-help-two-step-verification-overview.md). 

- Se la password è stata persa o dimenticata, è possibile reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/)

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
