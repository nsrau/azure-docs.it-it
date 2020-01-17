---
title: Registrare le informazioni di autenticazione per reimpostare la password Azure AD
description: Registrare le informazioni sul metodo di verifica per Azure AD la reimpostazione della password self-service, in modo che sia possibile reimpostare la password senza la guida dell'amministratore.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: ada3e9ae115f1e9cec03cd940b6bfdbe32897122
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156302"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrare le informazioni sul metodo di verifica per reimpostare la password

Se si dimentica la password per un account aziendale o dell'istituto di istruzione, la password non è mai stata inviata dall'organizzazione oppure l'account è stato bloccato, è possibile usare le informazioni di sicurezza e il dispositivo mobile per reimpostare la password per un account aziendale o dell'istituto di istruzione.

L'amministratore deve attivare questa funzionalità per poter registrare le informazioni e reimpostare la propria password. Se non viene visualizzata l'opzione **password dimenticata** , significa che l'amministratore non ha attivato la funzionalità per l'organizzazione. Se si ritiene che si tratti di un errore, contattare l'help desk per assistenza.

>[!Important]
>Questo articolo è destinato agli utenti che tentano di usare l'iscrizione per la reimpostazione della password self-service. Ciò significa che sarà possibile reimpostare la password aziendale o dell'Istituto di istruzione, ad esempio alain@contoso.com, senza richiedere la guida dell'amministratore. Se si è un amministratore che cerca informazioni su come attivare la reimpostazione della password self-service per i dipendenti o altri utenti, vedere la pagina relativa alla [distribuzione Azure ad la reimpostazione della password self-service e altri articoli](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Configurare il metodo di verifica della reimpostazione della password

1. Aprire il Web browser nel dispositivo e passare alla pagina delle [informazioni di sicurezza](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. A seconda del modo in cui l'amministratore ha configurato l'organizzazione, saranno disponibili una o più delle opzioni seguenti per la configurazione come metodo di verifica della sicurezza. Se sono disponibili più opzioni, è consigliabile usare più di uno come metodo di verifica di sicurezza, nel caso in cui uno dei metodi diventi non disponibile.

    - **App di autenticazione.** Scegliere di usare l'app Microsoft Authenticator o un'altra app di autenticazione come metodo di verifica di sicurezza. Per altre informazioni sulla configurazione dell'app, vedere [configurare l'app Microsoft Authenticator come metodo di verifica](security-info-setup-auth-app.md).

    - **Messaggistica del testo.** Scegliere di inviare messaggi di testo al dispositivo mobile. Per altre informazioni sulla configurazione della messaggistica testuale, vedere [impostare la messaggistica di testo come metodo di verifica](security-info-setup-text-msg.md).

    - **Chiamate telefoniche.** Scegliere di ricevere una telefonata al numero di telefono registrato. Per ulteriori informazioni sulla configurazione delle chiamate telefoniche, vedere [configurare un numero di telefono come metodo di verifica](security-info-setup-phone-number.md).

    - **Chiave di sicurezza.** Scegliere di usare una chiave di sicurezza compatibile con Microsoft. Per altre informazioni, vedere [configurare una chiave di sicurezza come metodo di verifica](security-info-setup-security-key.md).

    - **Indirizzo di posta elettronica.** Scegliere di usare un indirizzo di posta elettronica alternativo che può essere usato senza richiedere la password dimenticata o mancante. Questa operazione funziona solo per la reimpostazione della password, non come metodo di verifica di sicurezza. Per altre informazioni sulla configurazione di un indirizzo di posta elettronica, vedere [configurare un indirizzo di posta elettronica come metodo di verifica](security-info-setup-email.md).

    - **Domande di sicurezza.** Scegliere di configurare e rispondere alle domande di sicurezza predefinite impostate dall'amministratore. Questa operazione funziona solo per la reimpostazione della password, non come metodo di verifica di sicurezza. Per ulteriori informazioni sulle domande di sicurezza, vedere la pagina relativa alla [configurazione di domande di sicurezza come metodo di verifica](security-info-setup-questions.md).

3. Dopo aver selezionato e configurato i metodi, scegliere **fine** per completare il processo.

    > [!Note]
    > Le informazioni aggiunte per il numero di telefono o l'indirizzo di posta elettronica non sono condivise con la directory globale dell'organizzazione. Gli unici utenti che possono visualizzare queste informazioni sono l'utente e l'amministratore. Le risposte alle domande di sicurezza possono essere visualizzate solo dall'utente.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

 Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

| Messaggio di errore |  Possibile soluzione |
| --- | --- | --- |
| Contattare l'amministratore.<br>È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente.<br>Contattare il personale IT per ricevere ulteriore assistenza.| Se si riceve questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione gestisce internamente la password e non vuole reimpostare la password dal collegamento **non è possibile accedere all'account** . Per reimpostare la password in questa situazione, è necessario contattare l'help desk o l'amministratore dell'organizzazione per assistenza. |
| Account non abilitato per la reimpostazione della password.<br>Il personale IT non ha configurato l'account per l'uso di questo servizio.<br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Se si riceve questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione non ha attivato la funzionalità di reimpostazione della password oppure non è possibile usarla. Per reimpostare la password in questa situazione, è necessario selezionare il collegamento **Contatta un amministratore** . Dopo aver fatto clic sul collegamento, viene inviato un messaggio di posta elettronica all'help desk o all'amministratore dell'organizzazione, per indicare che si vuole reimpostare la password. |
| Non è stato possibile verificare l'account.<br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Se si riceve questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione ha attivato la reimpostazione della password e che è possibile usarla, ma che non è stata registrata per il servizio. In tal caso, è necessario contattare l'help desk o l'amministratore dell'organizzazione per reimpostare la password. Per informazioni su come eseguire la registrazione per la reimpostazione della password dopo aver eseguito l'accesso al dispositivo, vedere il processo precedente in questo articolo. |

## <a name="next-steps"></a>Passaggi successivi

- [Modificare la password usando la reimpostazione della password self-service](active-directory-passwords-update-your-own-password.md)

- [Pagina delle informazioni di sicurezza](https://mysignins.microsoft.com/security-info)

- [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)

- [Se non si riesce ad accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)