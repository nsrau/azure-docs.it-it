---
title: 'Implementazione: Reimpostazione password self-service di Azure AD | Microsoft Docs'
description: Consigli per l'implementazione della reimpostazione password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Come implementare la reimpostazione della password self-service

La maggior parte dei clienti segue questa procedura per garantire un'implementazione semplice della reimpostazione della password self-service.

1. [Abilitare la reimpostazione della password nella directory](active-directory-passwords-getting-started.md).
2. [Configurare le autorizzazioni AD locali per il writeback delle password](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configurare il writeback delle password](active-directory-passwords-writeback.md#configuring-password-writeback) per riscrivere le password da Azure AD alla directory locale.
4. [Assegnare e verificare le licenze necessarie](active-directory-passwords-licensing.md).
5. Per implementare gradualmente la reimpostazione della password nella directory, è possibile limitare l'accesso a un gruppo di utenti per poter eseguire progetti pilota con un gruppo specifico. A tale scopo impostare l'opzione **Reimpostazione password self-service abilitata** su **Selezionato** e selezionare il gruppo di sicurezza da abilitare per la reimpostazione della password. 
6. Immettere i [dati di autenticazione](active-directory-passwords-data.md) degli utenti, ad esempio il telefono dell'ufficio, il telefono cellulare e l'indirizzo di posta elettronica alternativo.
7. [Personalizzare l'esperienza di accesso di Azure AD per includere le informazioni personalizzate distintive dell'azienda](active-directory-passwords-customize.md).
8. Illustrare agli utenti l'uso della reimpostazione password self-service inviando istruzioni per la registrazione e la reimpostazione.
9. È possibile scegliere di imporre la registrazione in qualsiasi momento e richiedere agli utenti di riconfermare le informazioni di autenticazione dopo un certo periodo di tempo.
10. Nel tempo, esaminare gli utenti che eseguono la registrazione e usano la funzionalità tramite i [report messi a disposizione da Azure AD](active-directory-passwords-reporting.md).
11. Quando si è pronti, abilitare la reimpostazione della password per tutti gli utenti e impostare l'opzione **Reimpostazione delle password self-service abilitata** su **Tutti**. 

   > [!IMPORTANT]
   > Testare la reimpostazione password self-service con un utente e non con un amministratore, perché Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Implementazione basata sulla posta elettronica

Molti clienti ritengono che una campagna di posta elettronica con istruzioni semplici sia il modo più facile per invitare gli utenti a usare la reimpostazione password self-service. [Sono stati creati tre semplici messaggi di posta elettronica che possono essere usati come modelli per agevolare l'implementazione.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* Modello di messaggio **Presto disponibile** da usare nelle settimane o nei giorni precedenti l'implementazione per informare gli utenti che sarà necessario eseguire alcune operazioni.
* Modello di messaggio **Ora disponibile** da usare il giorno del lancio per invitare gli utenti a eseguire la registrazione e verificare i dati di autenticazione per usare la reimpostazione password self-service quando necessario.
* Modello di messaggio **Promemoria di registrazione** da usare per alcuni giorni o settimane dopo l'implementazione per ricordare agli utenti di eseguire la registrazione e verificare i dati di autenticazione.

![Indirizzo di posta elettronica][Email]

## <a name="creating-your-own-password-portal"></a>Creazione del portale delle password

Molti dei nostri clienti più grandi scelgono di ospitare una pagina Web e creare una voce DNS radice, ad esempio https://passwords.contoso.com. Popolano questa pagina con collegamenti per la reimpostazione della password di Azure AD, la registrazione per la reimpostazione della password e i portali di modifica della password, oltre ad altre informazioni specifiche dell'organizzazione. In qualsiasi comunicazione cartacea o di posta elettronica inviata è possibile includere un URL personalizzato facile da ricordare al quale gli utenti possono accedere quando devono usare i servizi.

* Portale di reimpostazione della password: https://aka.ms/sspr
* Portale di registrazione per la reimpostazione della password: http://aka.ms/ssprsetup
* Portale di modifica della password: https://account.activedirectory.windowsazure.com/ChangePassword.aspx

È stata creata una pagina di esempio, che è possibile usare e personalizzare in base alle esigenze dell'organizzazione, scaricabile da [GitHub](https://github.com/ajamess/password-reset-page).

## <a name="using-enforced-registration"></a>Uso della registrazione applicata

Se si vuole che gli utenti eseguano la registrazione per la reimpostazione della password, è possibile imporre la registrazione quando accedono usando Azure AD. È possibile abilitare questa opzione dal pannello **Reimpostazione password** della directory, selezionando l'opzione **Richiedere agli utenti di registrarsi all'accesso?** nella scheda **Registrazione**.

Gli amministratori possono richiedere agli utenti di eseguire nuovamente la registrazione dopo un periodo di tempo impostando **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su un valore compreso tra 0 e 730 giorni.

Dopo aver abilitato questa opzione, quando gli utenti accedono visualizzeranno un messaggio che li informa che l'amministratore ha richiesto loro di verificare le informazioni di autenticazione.

## <a name="populate-authentication-data"></a>Popolare i dati di autenticazione

Se si [popolano i dati di autenticazione per conto degli utenti](active-directory-passwords-data.md), questi non dovranno eseguire la registrazione per usare la reimpostazione password self-service. Se hanno dati di autenticazione che soddisfano i criteri di reimpostazione della password definiti, gli utenti potranno reimpostare le proprie password.

## <a name="disabling-self-service-password-reset"></a>Disabilitazione della reimpostazione self-service della password

Per disabilitare la reimpostazione self-service della password è sufficiente aprire il tenant di Azure AD e passare a **Reimpostazione password**, **Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**

## <a name="next-steps"></a>Passaggi successivi

* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalizzare questi modelli di messaggi di posta elettronica in base ai requisiti aziendali"