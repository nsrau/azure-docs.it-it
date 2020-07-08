---
title: Registrare le informazioni di autenticazione per reimpostare la password - Azure AD
description: Registrare le informazioni sul metodo di verifica per la reimpostazione della password self-service, in modo che sia possibile reimpostare la password senza il supporto dell'amministratore.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: 999b458985d8ab486e8dbd6bcaa48ddd9e02fc28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266101"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrare le informazioni sul metodo di verifica per reimpostare la password

Se si dimentica la password per un account aziendale o dell'istituto di istruzione, la password non è mai stata inviata dall'organizzazione oppure l'account è stato bloccato, è possibile usare le informazioni di sicurezza e il dispositivo mobile per reimpostare la password per un account aziendale o dell'istituto di istruzione.

Per poter registrare le informazioni e reimpostare la password, è necessario che l'amministratore attivi questa funzionalità. Se l'opzione **Password dimenticata** non è visualizzata, significa che l'amministratore non ha attivato la funzionalità per l'organizzazione. Se si ritiene che si tratti di un errore, contattare l'help desk per assistenza.

>[!Important]
>Questo articolo è destinato agli utenti che desiderano registrarsi per la reimpostazione della password self-service, Ciò significa che sarà possibile reimpostare la password aziendale o dell'Istituto di istruzione, ad esempio alain@contoso.com , senza richiedere la guida dell'amministratore. Se si è un amministratore che sta cercando altre informazioni su come testare la reimpostazione della password self-service per i dipendenti o altri utenti, vedere [Implementare la reimpostazione della password self-service in Azure AD e altri articoli](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Configurare il metodo di verifica della reimpostazione della password

1. Aprire il browser Web nel dispositivo e passare alla [pagina delle informazioni di sicurezza](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. A seconda del modo in cui l'amministratore ha configurato l'organizzazione, saranno disponibili una o più delle opzioni seguenti per la configurazione come metodo di verifica di sicurezza. Se sono disponibili più opzioni, è altamente consigliabile usarne più di uno come metodo di verifica di sicurezza, nel caso in cui uno dei metodi non sia disponibile.

    - **App per l'autenticazione.** Permette di scegliere di usare l'app Microsoft Authenticator o un'altra app di autenticazione come metodo di verifica di sicurezza. Per altre informazioni sulla configurazione dell'app, vedere [Configurare l'app Microsoft Authenticator come metodo di verifica](security-info-setup-auth-app.md).

    - **Messaggistica di testo.** Permette di scegliere di inviare messaggi di testo al dispositivo mobile. Per altre informazioni sulla configurazione della messaggistica di testo, vedere [Configurare la messaggistica di testo come metodo di verifica](security-info-setup-text-msg.md).

    - **Telefono.** Permette di scegliere di ricevere una telefonata al numero di telefono registrato. Per altre informazioni sulla configurazione delle telefonate, vedere [Configurare un numero di telefono come metodo di verifica](security-info-setup-phone-number.md).

    - **Chiave di sicurezza.** Permette di scegliere di usare una chiave di sicurezza compatibile con Microsoft. Per altre informazioni, vedere [Configurare una chiave di sicurezza come metodo di verifica](security-info-setup-security-key.md).

    - **Indirizzo di posta elettronica.** Scegliere di usare un indirizzo di posta elettronica alternativo che possa essere usato senza richiedere la password dimenticata o mancante. Questa operazione funziona solo per la reimpostazione della password, non come metodo di verifica di sicurezza. Per altre informazioni sulla configurazione di un indirizzo di posta elettronica, vedere [Configurare un indirizzo di posta elettronica come metodo di verifica](security-info-setup-email.md).

    - **Domande di sicurezza.** Permette di scegliere di configurare e rispondere a domande di sicurezza predefinite impostate dall'amministratore. Questa operazione funziona solo per la reimpostazione della password, non come metodo di verifica di sicurezza. Per altre informazioni sulle domande di sicurezza, vedere [Configurare domande di sicurezza come metodo di verifica](security-info-setup-questions.md).

3. Dopo aver selezionato e configurato i metodi, scegliere **Fine** per completare la procedura.

    > [!Note]
    > Le informazioni aggiunte per il numero di telefono o l'indirizzo di posta elettronica non verranno condivise con la directory globale dell'organizzazione, ma potranno essere visualizzate soltanto dall'utente stesso e dagli amministratori. Le risposte alle domande di sicurezza possono essere visualizzate solo dall'utente.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

 Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

| Messaggio di errore |  Possibile soluzione |
| --- | --- | --- |
| Contattare l'amministratore.<br>È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente.<br>Contattare il personale IT per ricevere ulteriore assistenza.| Se si riceve questo messaggio di errore dopo aver digitato l'ID utente, significa che la password è gestita internamente dall'organizzazione e non è quindi possibile reimpostarla dal collegamento **Can't access your account** (Impossibile accedere all'account). Per reimpostare la password in questi casi, è necessario contattare l'help desk dell'organizzazione o il proprio amministratore per richiedere assistenza. |
| Account non abilitato per la reimpostazione della password.<br>Il personale IT non ha configurato l'account per l'uso di questo servizio.<br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Se si riceve questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione non ha attivato la funzionalità di reimpostazione della password oppure non è possibile usarla. Per reimpostare la password in questi casi, selezionare il collegamento **Contattare un amministratore**. Dopo aver fatto clic sul collegamento, verrà inviato un messaggio di posta elettronica all'help desk dell'organizzazione o all'amministratore, per comunicare la necessità di reimpostare la password. |
| Non è stato possibile verificare l'account.<br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Se si riceve questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione ha attivato la reimpostazione della password e che è possibile usarla, ma che non si è registrati al servizio. In questi casi, per reimpostare la password è necessario contattare l'help desk dell'organizzazione o il proprio amministratore. Per informazioni su come eseguire la registrazione per la reimpostazione della password dopo aver eseguito l'accesso al dispositivo, vedere la procedura descritta in precedenza in questo articolo. |

## <a name="next-steps"></a>Passaggi successivi

- [Modificare la password usando la reimpostazione della password self-service](active-directory-passwords-update-your-own-password.md)

- [Pagina relativa alle informazioni di sicurezza](https://mysignins.microsoft.com/security-info)

- [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)

- [Se non si riesce ad accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
