---
title: Registrare le informazioni di autenticazione per reimpostare la propria password - Azure ADRegister authentication info to reset your own password - Azure AD
description: Registrare le informazioni sul metodo di verifica per la reimpostazione della password self-service di Azure AD, in modo da poter reimpostare la password senza l'aiuto dell'amministratore.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062643"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrare le informazioni del metodo di verifica per reimpostare la password

Se si dimentica la password per un account aziendale o dell'istituto di istruzione, la password non è mai stata inviata dall'organizzazione oppure l'account è stato bloccato, è possibile usare le informazioni di sicurezza e il dispositivo mobile per reimpostare la password per un account aziendale o dell'istituto di istruzione.

L'amministratore deve attivare questa funzione per poter registrare le informazioni e reimpostare la propria password. Se l'opzione **Password dimenticata** non è visualizzata, significa che l'amministratore non ha attivato la funzionalità per l'organizzazione. Se si ritiene che si tratti di un errore, contattare l'help desk per assistenza.

>[!Important]
>Questo articolo è destinato agli utenti che tentano di utilizzare l'iscrizione per la reimpostazione della password self-service. Ciò significa che sarà possibile reimpostare la propria password alain@contoso.comaziendale o dell'istituto di istruzione (ad esempio, ), senza richiedere l'aiuto dell'amministratore. Per gli amministratori che cercano informazioni su come attivare la reimpostazione della password self-service per i dipendenti o altri utenti, vedere Distribuzione della [password self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)di Azure AD e altri articoli .

## <a name="set-up-your-password-reset-verification-method"></a>Impostare il metodo di verifica della reimpostazione della password

1. Aprire il browser Web sul dispositivo e passare alla pagina delle informazioni di [sicurezza.](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)

2. A seconda di come l'amministratore ha configurato l'organizzazione, una o più delle opzioni seguenti saranno disponibili per l'impostazione come metodo di verifica della sicurezza. Se sono disponibili più opzioni, ti consigliamo vivamente di utilizzarne più di una come metodo di verifica della sicurezza, nel caso in cui uno dei tuoi metodi non sia più disponibile.

    - **App di autenticazione.** Scegli di usare l'app Microsoft Authenticator o un'altra app di autenticazione come metodo di verifica della sicurezza. Per ulteriori informazioni sulla configurazione dell'app, vedere [Configurare l'app Microsoft Authenticator come metodo](security-info-setup-auth-app.md)di verifica.

    - **Messaggi di testo.** Scegli di inviarti messaggi di testo al tuo dispositivo mobile. Per ulteriori informazioni sull'impostazione della messaggistica di testo, vedere Configurare la [messaggistica](security-info-setup-text-msg.md)di testo come metodo di verifica .

    - **Telefonate.** Scegliere di ricevere una chiamata al numero di telefono registrato. Per ulteriori informazioni sull'impostazione delle chiamate telefoniche, vedere [Impostare un numero](security-info-setup-phone-number.md)di telefono come metodo di verifica.

    - **Chiave di sicurezza.** Scegliere di utilizzare una chiave di sicurezza compatibile con Microsoft.Choose to use a Microsoft-compatible security key. Per ulteriori informazioni, consultate [Impostare una chiave](security-info-setup-security-key.md)di sicurezza come metodo di verifica.

    - **indirizzo email.** Scegli di utilizzare un indirizzo email alternativo che può essere utilizzato senza richiedere la password dimenticata o mancante. Questo funziona solo per la reimpostazione della password, non come metodo di verifica della sicurezza. Per ulteriori informazioni sull'impostazione di un indirizzo di posta elettronica, vedere [Configurare un indirizzo di posta elettronica come metodo](security-info-setup-email.md)di verifica.

    - **Domande di sicurezza.** Scegliere di impostare e rispondere alle domande di sicurezza predefinite configurate dall'amministratore. Questo funziona solo per la reimpostazione della password, non come metodo di verifica della sicurezza. Per ulteriori informazioni sulle domande di sicurezza, consultate Impostare le domande di sicurezza come metodo di [verifica.](security-info-setup-questions.md)

3. Dopo aver selezionato e impostato i metodi, scegliere **Fine** per completare il processo.

    > [!Note]
    > Le informazioni aggiunte per il numero di telefono o l'indirizzo di posta elettronica non vengono condivise con la directory globale dell'organizzazione. Le uniche persone che possono vedere queste informazioni sono lei e l'amministratore. Le risposte alle domande di sicurezza possono essere visualizzate solo dall'utente.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

 Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

| Messaggio di errore |  Possibile soluzione |
| --- | --- | --- |
| Contattare l'amministratore.<br>È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente.<br>Contattare il personale IT per ricevere ulteriore assistenza.| Se viene visualizzato questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione gestisce internamente la password e non desidera reimpostare la password dal collegamento **Impossibile accedere all'account.** Per reimpostare la password in questa situazione, è necessario contattare l'help desk dell'organizzazione o l'amministratore per assistenza. |
| Account non abilitato per la reimpostazione della password.<br>Il personale IT non ha configurato l'account per l'uso di questo servizio.<br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Se viene visualizzato questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione non ha attivato la funzionalità di reimpostazione della password o che non è consentito utilizzarlo. Per reimpostare la password in questa situazione, è necessario selezionare il collegamento **Contatta un amministratore.** Dopo aver fatto clic sul collegamento, viene inviato un messaggio di posta elettronica all'help desk o all'amministratore dell'organizzazione, per informarlo che si desidera reimpostare la password. |
| Non è stato possibile verificare l'account.<br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Se viene visualizzato questo messaggio di errore dopo aver digitato l'ID utente, significa che l'organizzazione ha attivato la reimpostazione della password e che è possibile utilizzarlo, ma che non è stata registrata per il servizio. In questo caso, è necessario contattare l'help desk o l'amministratore dell'organizzazione per reimpostare la password. Per informazioni sulla registrazione per la reimpostazione della password dopo essere tornati sul dispositivo, vedere il processo precedente in questo articolo. |

## <a name="next-steps"></a>Passaggi successivi

- [Modificare la password usando la reimpostazione della password self-service](active-directory-passwords-update-your-own-password.md)

- [Pagina Informazioni di sicurezza](https://mysignins.microsoft.com/security-info)

- [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)

- [Se non si riesce ad accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
