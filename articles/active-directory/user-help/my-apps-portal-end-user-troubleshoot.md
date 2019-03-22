---
title: Ottieni assistenza per il portale App personali - Azure Active Directory | Microsoft Docs
description: Ottieni assistenza per accesso a e l'esecuzione di attività comuni nel portale App personali.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340110"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Risolvere i problemi con il portale App personali
Se vengono riscontrati problemi con l'accesso a o usando il **My Apps** portale, provare questi suggerimenti di risoluzione dei problemi prima di contattare il supporto tecnico o l'amministratore per assistenza.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Si verifica un problema di installazione dell'estensione accesso sicuro alle App personali
Se si verificano problemi di installazione dell'estensione accesso sicuro alle App personali:

- Assicurarsi che si sta usando un browser supportati, tra cui:

    - **Microsoft Edge.** In esecuzione in Windows 10 Anniversary Edition o versioni successive.
    - **Google Chrome.** In esecuzione in Windows 7 o versioni successive e in Mac OS X o versione successiva.
    - **Mozilla Firefox 26.0 o versione successiva.** In esecuzione in Windows XP SP2 o versioni successive e in Mac OS X 10.6 o versione successiva.
    - **Internet Explorer 11.** In Windows 7 o versioni successive (supporto limitato) è in esecuzione.

- Assicurarsi che le impostazioni di estensione del browser siano accesi.

- Provare a riavviare il browser e accedere la **My Apps** nuovo portale.

- Provare a cancellare i cookie del browser, quindi riavviare e accedere al **My Apps** nuovo portale.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Non è possibile accedere per il **My Apps** portale
Se si riscontrano problemi di accesso di **My Apps** portale, è possibile provare quanto segue:

- Assicurarsi che si usa l'URL corretto. Deve essere https://myapps.microsoft.com o una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

- Assicurarsi che la password sia corretta e non sia scaduto. Per altre informazioni, vedi [reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Assicurarsi che le tue info di verifica non aggiornate e accurate. Per altre informazioni, vedere [cosa Azure multi-Factor Authentication per me?](multi-factor-authentication-end-user.md) oppure [modifica le informazioni e metodi di info di sicurezza](security-info-add-update-methods-overview.md).

- Aggiungere il **App personali** portale URL per il **proprietà Internet > sicurezza > siti attendibili** impostazione.

- Cancellare la cache del browser e provare a eseguire nuovamente l'accesso.

## <a name="my-password-isnt-working"></a>La password non funziona
Se si dimentica la password, mai ricevuto uno della propria organizzazione, sono bloccati senza accesso all'account o per modificare la password, vedere [dimenticata la password di Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Desidera essere in grado di reimpostare la propria password
Per essere in grado di reimpostare la password, l'amministratore deve prima di tutto attivare la funzionalità per l'organizzazione e quindi è necessario aggiornare e verificare i metodi di verifica richiesto. Per altre informazioni su come aggiornare i metodi di verifica, vedere [registrazione per reimpostazione password self-service](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Viene visualizzato un messaggio accesso negato quando si avvia un'app
Se si riceve un **accesso negato** dei messaggi dopo l'avvio di un'app dalle **My App** portale, è possibile provare quanto segue:

- Assicurarsi di aver installato il [sicuro alle App personali estensione di accesso](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) e che sta usando una [browser supportato](my-apps-portal-end-user-access.md#supported-browsers).

- Assicurarsi che si usa l'URL corretto per l'app e che l'URL è nelle **proprietà Internet > sicurezza > siti attendibili** elenco.

- Assicurarsi che la password sia corretta e non sia scaduto. Per altre informazioni, vedi [reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Assicurarsi che le tue info di verifica non aggiornate e accurate. Per altre informazioni, vedere [cosa Azure multi-Factor Authentication per me?](multi-factor-authentication-end-user.md) oppure [modifica le informazioni e metodi di info di sicurezza](security-info-add-update-methods-overview.md).

- Cancellare la cache del browser e provare a eseguire nuovamente l'accesso.

Se dopo aver provato queste operazioni è ancora possibile accedere all'app, è necessario contattare l'Help desk dell'organizzazione per assistenza.

## <a name="next-steps"></a>Passaggi successivi
Dopo l'accesso per il **My Apps** portale, è anche possibile aggiornare il profilo e le informazioni sull'account, le informazioni sul gruppo e l'accesso esaminare informazioni (se si dispone dell'autorizzazione).

- [Accedere e usare le app nel portale App personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni sul profilo](my-apps-portal-end-user-update-profile.md).

- [Visualizzare e aggiornare le informazioni correlate a gruppi](my-apps-portal-end-user-groups.md).

- [Eseguire le verifiche di accesso personalizzati](my-apps-portal-end-user-access-reviews.md).