---
title: Guida alla distribuzione della reimpostazione della password self-service - Azure Active Directory
description: Consigli per l'implementazione della reimpostazione password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 0ab45043e2f75ff07a2a1a48e69507be185b56ab
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Come implementare la reimpostazione della password self-service

Per garantire un'implementazione semplice della funzionalità di reimpostazione della password self-service di Azure Active Directory (Azure AD), la maggior parte dei clienti completa la procedura seguente:

1. [Abilitare la reimpostazione della password nella directory](active-directory-passwords-getting-started.md).
2. [Configurare le autorizzazioni di Active Directory locali per il writeback delle password](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configurare il writeback delle password](active-directory-passwords-writeback.md#configure-password-writeback) per riscrivere le password da Azure AD alla directory locale.
4. [Assegnare e verificare le licenze necessarie](active-directory-passwords-licensing.md).
5. Stabilire se si vuole effettuare un'implementazione graduale. Per implementare gradualmente la reimpostazione della password nella directory, è possibile limitare l'accesso a un gruppo di utenti per poter eseguire il programma pilota con un gruppo specifico. Per implementare un gruppo specifico, impostare l'opzione **Reimpostazione delle password self-service abilitata** su **Selezionata** e selezionare il gruppo di sicurezza a cui si vuole consentire la reimpostazione della password. 
6. Immettere i [dati di autenticazione](active-directory-passwords-data.md) necessari agli utenti per la registrazione, ad esempio il telefono dell'ufficio, il telefono cellulare e l'indirizzo di posta elettronica alternativo.
7. [Personalizzare l'esperienza di accesso di Azure AD per includere le informazioni personalizzate distintive dell'azienda](active-directory-passwords-customize.md).
8. Illustrare agli utenti l'uso della reimpostazione password self-service. Inviare istruzioni per la registrazione e la reimpostazione delle password.
9. Stabilire quando si vuole applicare la registrazione. È possibile scegliere di applicare la registrazione in qualsiasi momento. È anche possibile richiedere agli utenti di riconfermare le informazioni di autenticazione dopo un certo periodo di tempo.
10. Usare la funzionalità di creazione report. Nel corso del tempo, è possibile riesaminare la registrazione degli utenti e l'utilizzo con la [funzionalità di creazione di report di Azure AD](active-directory-passwords-reporting.md).
11. Abilitare la reimpostazione della password. Quando si è pronti, abilitare la reimpostazione della password per tutti gli utenti impostando l'opzione **Reimpostazione delle password self-service abilitata** su **Tutti**. 

   > [!NOTE]
   > La modifica di questa opzione da un gruppo selezionato a tutti gli utenti non invalida i dati di autenticazione esistenti registrati da un utente come parte di un gruppo di test. Gli utenti configurati e aventi dati di autenticazione validi registrati continuano a funzionare.

12. [Consentire agli utenti di Windows 10 di reimpostare la password nella schermata di accesso](active-directory-passwords-login.md).

   > [!IMPORTANT]
   > Testare la reimpostazione password self-service con un utente e non con un amministratore, perché Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Implementazione basata sulla posta elettronica

Molti clienti ritengono che il modo più facile per invitare gli utenti a usare la reimpostazione password self-service sia una campagna di posta elettronica che includa istruzioni semplici. [Sono stati creati tre semplici messaggi di posta elettronica che possono essere usati come modelli per agevolare l'implementazione](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16):

* **Presto disponibile**: modello di messaggio usato nelle settimane o nei giorni precedenti l'implementazione per informare gli utenti che sarà necessario eseguire alcune operazioni.
* **Ora disponibile**: modello di messaggio usato il giorno del lancio del programma per invitare gli utenti a eseguire la registrazione e verificare i dati di autenticazione. Se gli utenti si registrano subito, la reimpostazione password self-service sarà disponibile al momento necessario.
* **Promemoria di registrazione**: modello di messaggio usato per alcuni giorni o settimane dopo la distribuzione per ricordare agli utenti di eseguire la registrazione e verificare i dati di autenticazione.

![Indirizzo di posta elettronica][Email]

## <a name="create-your-own-password-portal"></a>Creare il portale delle password

Molti clienti scelgono di ospitare una pagina Web e di creare una voce DNS radice, ad esempio https://passwords.contoso.com. Popolano questa pagina con collegamenti alle informazioni seguenti:

* [Portale di reimpostazione della password di Azure AD: https://aka.ms/sspr](https://aka.ms/sspr)
* [Portale di registrazione per la reimpostazione della password di Azure AD: http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* [Portale di modifica della password di Azure AD: https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Altre informazioni specifiche dell'organizzazione

In qualsiasi comunicazione cartacea o di posta elettronica inviata è possibile includere un URL personalizzato facile da ricordare al quale gli utenti possono accedere quando devono usare i servizi. È stata creata una [pagina di reimpostazione della password di esempio](https://github.com/ajamess/password-reset-page), che è possibile usare e personalizzare in base alle esigenze dell'organizzazione.

## <a name="use-enforced-registration"></a>Usare la registrazione applicata

Se si vuole che gli utenti eseguano la registrazione per la reimpostazione della password, è possibile rendere obbligatoria la registrazione quando accedono tramite Azure AD. È possibile abilitare questa opzione dal riquadro **Reimpostazione password** della directory, selezionando l'opzione **Richiedere agli utenti di registrarsi all'accesso?** nella scheda **Registrazione**.

Gli amministratori possono richiedere agli utenti di registrarsi nuovamente dopo un periodo di tempo specifico. Possono impostare l'opzione **Number of days before users are asked to reconfirm their authentication information** (Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione) su un valore compreso tra 0 e 730 giorni.

Dopo aver abilitato questa opzione, quando gli utenti accedono, visualizzano un messaggio che informa che l'amministratore ha richiesto loro di verificare le informazioni di autenticazione.

## <a name="populate-authentication-data"></a>Popolare i dati di autenticazione

È consigliabile [popolare i dati di autenticazione per gli utenti](active-directory-passwords-data.md). In questo modo non è necessario che gli utenti eseguano registrazione per la reimpostazione della password prima di poter usare la reimpostazione password self-service. Se hanno fornito dati di autenticazione che soddisfano i criteri di reimpostazione della password, potranno reimpostare le proprie password.

## <a name="disable-self-service-password-reset"></a>Disabilitare la reimpostazione password self-service

La disabilitazione della reimpostazione password self-service è un'operazione semplice. Aprire il tenant di Azure AD e passare a **Reimpostazione password** > **Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**.

## <a name="next-steps"></a>Passaggi successivi

* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md)
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalizzare questi modelli di messaggi di posta elettronica in base ai requisiti aziendali"
