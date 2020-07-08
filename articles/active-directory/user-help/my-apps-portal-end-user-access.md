---
title: Individuare e usare le app nel portale App personali - Azure AD
description: Informazioni su come trovare il portale App personali e su come accedere alle app basate sul cloud dell'organizzazione.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: aa9813480425d179bdd11bac6f6f944f9f65e3ab
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741959"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>Eseguire l'accesso e avviare le app dal portale App personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per:

- Visualizzare e avviare molte delle app basate sul cloud dell'organizzazione
- Aggiornare le informazioni su profilo e account
- Visualizzare le informazioni sui **Gruppi**
- Eseguire **verifiche di accesso** per app e gruppi

Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> Questo contenuto è destinato agli utenti di App personali. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Browser supportati

È possibile aprire il portale **App personali** dai Web browser seguenti:

- Google Chrome

- Mozilla Firefox, versione 26.0 o successiva

- Microsoft Edge

- Internet Explorer, versione 11 (supporto limitato)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Scaricare e installare l'estensione My Apps Secure Sign-in

Scaricare e installare l'estensione My Apps Secure Sign-in, se richiesto. Questa estensione consente di avviare tutte le app cloud dell'organizzazione che richiedono l'uso di un processo di accesso Single Sign-On. Se l'organizzazione ha già configurato l'accesso Single Sign-On per l'utente, l'estensione viene installata automaticamente ed è possibile ignorare questa sezione.

Questa estensione consente di:

- Accedere direttamente alle app dalla pagina di accesso.

- Avviare tutte le app tramite la funzionalità **Ricerca rapida**.

- Visualizzare le ultime app usate nella sezione **Usate di recente**.

- Usare URL aziendali interne in modalità remota tramite [Proxy di applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Per scaricare e installare l'estensione

Scaricare e installare l'estensione in base al browser in uso:

- **Google Chrome.** Da Chrome Web Store passare alla funzionalità [My Apps Secure Sign-in Extension](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) e quindi selezionare **Aggiungi a Chrome**.

- **Mozilla Firefox**. Nella pagina **Componenti aggiuntivi** in Firefox passare alla funzionalità [My Apps Secure Sign-in Extension](https://addons.mozilla.org/firefox/addon/access-panel-extension/) e quindi selezionare **Aggiungi a Firefox**.

- **Microsoft Edge**. In Microsoft Store passare alla funzionalità [My Apps Secure Sign-in Extension](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e quindi selezionare **Ottieni** per scaricare l'estensione per il browser legacy Microsoft Edge.  
Se si usa il nuovo browser Microsoft Edge (Chromium), è possibile ottenere l'estensione dallo [Store Estensioni per Microsoft Edge](https://microsoftedge.microsoft.com/addons/category/EdgeExtensionsEditorsPick).

Verrà aggiunta un'icona a destra della barra degli **indirizzi** che consentirà di accedere e personalizzare l'estensione.

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>Per modificare il portale App personali usando l'estensione

È possibile scegliere il numero di app da visualizzare nella sezione **Usate di recente** e decidere se consentire il reindirizzamento degli URL interni dell'organizzazione.

1. Selezionare la nuova icona di **My Apps Secure Sign-in Extension** ![icona dell'estensione](media/my-apps-portal/my-apps-portal-extension-icon.png) a destra della barra degli **indirizzi** e quindi selezionare **Accedi** per iniziare.

2. Fare clic con il pulsante destro del mouse sull'icona **Impostazioni** ![icona Impostazioni](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), quindi scegliere **Impostazioni**.

3. Nella casella **Impostazioni** selezionare il numero di app recenti da visualizzare nel portale e se consentire il reindirizzamento degli URL interni dell'organizzazione per poterli usare in modalità remota.

    ![Pagina Impostazioni dell'estensione che mostra le personalizzazioni disponibili](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Accedere e usare il portale App personali da un dispositivo

È possibile accedere e usare il portale App personali nel computer, da un browser gestito da Intune o da un dispositivo mobile iOS o Android.

![Pagina App nel portale App personali](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Accedere e usare il portale App personali dal computer

Se si è autorizzati ad accedere e a usare le app basate sul cloud dell'organizzazione, queste saranno disponibili tramite il portale **App personali**.

1. Accedere all'account aziendale o dell'istituto di istruzione nel computer.

2. In un Web browser supportato aprire e passare a https://myapps.microsoft.com oppure usare il collegamento fornito dall'organizzazione, se è previsto un indirizzamento dell'utente a una pagina personalizzata, ad esempio `https://myapps.microsoft.com/contoso.com`.

    Verrà visualizzata la pagina **App**, che mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **App** selezionare l'app che si vuole iniziare a usare.

    Verrà aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Accedere e usare il portale App personali in un browser gestito da Intune

È possibile visualizzare e usare le app dell'organizzazione da un'istanza di Intune Managed Browser nei dispositivi iOS e Android.

1. Nel dispositivo mobile scaricare e installare l'app Intune Managed Browser dall'App Store di Apple o da Google Play Store.

2. Aprire l'app Intune Managed Browser, passare a https://myapps.microsoft.com oppure usare il collegamento fornito dall'organizzazione, se è previsto un indirizzamento dell'utente a una pagina personalizzata, ad esempio https://myapps.microsoft.com/contoso.com.

    Verrà visualizzata la pagina **App**, che mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **App** selezionare l'app che si vuole iniziare a usare.

    Verrà aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Accedere e usare il portale App personali da un dispositivo iOS

È possibile visualizzare e usare il portale **App personali** da un dispositivo iPhone o iPad, che esegue iOS 7 o versione successiva. È anche possibile installare l'[app per dispositivi mobili My Apps](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) per accedere alle app dell'organizzazione dai dispositivi iOS.

1. Nel dispositivo mobile avviare un'app Web browser, ad esempio Safari.

2. Passare a https://myapps.microsoft.com oppure usare il collegamento fornito dall'organizzazione, se è previsto un indirizzamento dell'utente a una pagina personalizzata, ad esempio https://myapps.microsoft.com/contoso.com.

    Verrà visualizzata la pagina **App**, che mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **App** selezionare l'app che si vuole iniziare a usare.

    Verrà aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Accedere e usare il portale App personali da un dispositivo Android

È possibile visualizzare e usare il portale **App personali** da un dispositivo Android.

1. Nel dispositivo mobile avviare un'app Web browser, ad esempio Google Chrome.

2. Passare a https://myapps.microsoft.com oppure usare il collegamento fornito dall'organizzazione, se è previsto un indirizzamento dell'utente a una pagina personalizzata, ad esempio https://myapps.microsoft.com/contoso.com.

    Verrà visualizzata la pagina **App**, che mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **App** selezionare l'app che si vuole iniziare a usare.

    Verrà aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Aggiungere una nuova app al portale App personali

Se l'amministratore ha concesso l'autorizzazione, è possibile aggiungere una nuova app alla pagina **App**.

1. Nella pagina **App** eseguire una delle operazioni seguenti:
    - Se si usa l'esperienza App personali originale, selezionare **Aggiungi app** come illustrato.

      ![Pagina Aggiungi app nel portale App personali](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - Se si usa l'esperienza App personali aggiornata, selezionare **Aggiungi app self-service**.

       ![Pagina Aggiungi app nel portale App personali all'indirizzo myapplications.microsoft.com](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. Selezionare l'app che si vuole aggiungere dall'elenco fornito, quindi selezionare **Aggiungi**.

3. L'app viene aggiunta all'elenco nella pagina **App**.

    Prima di aggiungere alcune app potrebbe essere necessaria l'approvazione dell'amministratore. In tal caso, l'app non viene aggiunta alla pagina **App** fino a quando non viene approvata dall'amministratore.

## <a name="start-a-cloud-based-app"></a>Avviare un'app basata sul cloud

È possibile avviare tutte le app basate sul cloud disponibili dal portale **App personali**. Verranno visualizzate solo le app che si è autorizzati a usare.

- Dalla pagina **App** selezionare l'app che si vuole iniziare a usare.

    Verrà aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

## <a name="activities-in-the-my-apps-portal"></a>Attività nel portale App personali

Dopo l'accesso al portale **App personali**, è possibile:

- Organizzare le app nelle diverse categorie create e fornite dall'organizzazione. Per altre informazioni, vedere [Accedere e usare le raccolte nel portale App personali](my-applications-portal-workspaces.md).

- Esaminare, aggiornare o revocare le autorizzazioni concesse alle applicazioni. Per altre informazioni, vedere [Modificare o revocare le autorizzazione per le applicazioni nel portale App personali](my-applications-portal-permissions-saved-accounts.md).

>[!Note]
>Se non sono disponibili raccolte o categorie, significa che l'amministratore non le ha configurate né condivise. Rivolgersi all'help desk dell'organizzazione per ottenere ulteriore assistenza o le autorizzazioni per visualizzare le raccolte condivise.

## <a name="next-steps"></a>Passaggi successivi

Dopo l'accesso alla pagina **App**, è possibile:

- [Cambiare le informazioni del profilo](my-apps-portal-end-user-update-profile.md)

- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md)

- [Eseguire verifiche di accesso personali](my-apps-portal-end-user-access-reviews.md)
