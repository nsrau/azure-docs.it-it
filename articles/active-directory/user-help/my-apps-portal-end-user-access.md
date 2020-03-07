---
title: Individuare & usare le app nel portale app personali-Azure AD
description: Informazioni su come trovare il portale app personali e quindi su come accedere alle app basate sul cloud dell'organizzazione.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: a5dae30c8b8f43602581b39005389211da83cb9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375883"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>Accedere e avviare app dal portale app personali

È possibile usare l'account aziendale o dell'Istituto di istruzione con il portale **app personali** basato sul Web per:

- Visualizza e avvia molte delle app basate sul cloud dell'organizzazione
- Aggiornare il profilo e le informazioni sull'account
- Vedere le informazioni sui **gruppi**
- Eseguire verifiche di **accesso** per le app e i gruppi

Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> Questo contenuto è destinato agli utenti di app personali. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Browser supportati

È possibile ottenere il portale **app personali** da uno dei seguenti Web browser:

- Google Chrome

- Mozilla Firefox, versione 26,0 o successiva

- Microsoft Edge

- Internet Explorer, versione 11 (supporto limitato)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Scaricare e installare l'estensione per l'accesso sicuro alle app personali

Scaricare e installare l'estensione per l'accesso sicuro alle app personali, se richiesto. Questa estensione consente di avviare tutte le app cloud dell'organizzazione che richiedono l'uso di un processo di Single Sign-On. Se l'organizzazione è già stata configurata per l'Single Sign-On, l'estensione viene installata automaticamente ed è possibile ignorare questa sezione.

Questa estensione consente di:

- Accedere direttamente alle app dalla pagina di accesso.

- Avviare le app usando la funzionalità **ricerca rapida** .

- Vedere le ultime app usate nella sezione **usato di recente** .

- Usare gli URL interni della società in remoto con il [proxy di applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Per scaricare e installare l'estensione

Scaricare e installare l'estensione in base al browser in uso:

- **Google Chrome.** Da Chrome Web Store passare alla funzionalità [My App Secure Sign-in Extension](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) e quindi selezionare **Aggiungi a Chrome**.

- **Mozilla Firefox** Dalla pagina dei componenti aggiuntivi di **Firefox** passare alla funzionalità di estensione per l' [accesso sicuro alle app personali](https://addons.mozilla.org/firefox/addon/access-panel-extension/) e quindi selezionare **Aggiungi a Firefox**.

- **Microsoft Edge** Dal Microsoft Store passare alla funzionalità di estensione per l' [accesso sicuro alle app personali](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e quindi selezionare **Ottieni**.

Viene aggiunta un'icona a destra della barra degli **indirizzi** , che consente di accedere e personalizzare l'estensione.

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>Per modificare il portale delle app personali usando l'estensione

È possibile scegliere il numero di app da visualizzare nella sezione **usato di recente** e decidere se consentire il reindirizzamento degli URL interni dell'organizzazione.

1. Selezionare l'icona dell'estensione per l' **accesso sicuro alle app personali** ![estensione](media/my-apps-portal/my-apps-portal-extension-icon.png) a destra della barra degli **indirizzi** e quindi selezionare **Accedi per**iniziare.

2. Fare clic con il pulsante destro del mouse sull'icona **delle impostazioni ![** icona Impostazioni](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), quindi selezionare **Impostazioni**.

3. Nella casella **Impostazioni** selezionare il numero di app recenti da visualizzare nel portale e se consentire agli URL interni dell'organizzazione di essere reindirizzati in modo da poterli usare in remoto.

    ![Pagina delle impostazioni dell'estensione che mostra le personalizzazioni disponibili](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Accedere e usare il portale app personali per dispositivo

È possibile accedere e usare il portale app personali nel computer, da un browser gestito da Intune o da un dispositivo mobile iOS o Android.

![Pagina app nel portale app personali](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Accedere e usare il portale app personali nel computer

Se si dispone dell'autorizzazione per accedere e usare le app basate sul cloud dell'organizzazione, è possibile accedervi tramite il portale **app personali** .

1. Accedere all'account aziendale o dell'Istituto di istruzione nel computer.

2. In un Web browser supportato aprire e passare a https://myapps.microsoft.comoppure usare il collegamento fornito dall'organizzazione se indirizzare l'utente a una pagina personalizzata, ad esempio `https://myapps.microsoft.com/contoso.com`.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione che sono disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Accedere e usare il portale app personali in un browser gestito da Intune

Consente di visualizzare e usare le app dell'organizzazione da una Intune Managed Browser nei dispositivi iOS e Android.

1. Nel dispositivo mobile scaricare e installare l'app Intune Managed Browser dall' [App Store di Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e la [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Aprire l'app Intune Managed Browser, passare a https://myapps.microsoft.como usare il collegamento fornito dall'organizzazione se indirizzare l'utente a una pagina personalizzata, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Accedere e usare il portale app personali in un dispositivo iOS

Visualizzare e usare il portale **app personali** da un dispositivo iPhone o iPad, che esegue iOS 7 o versione successiva. È anche possibile installare l' [app](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) per dispositivi mobili app personali per accedere alle app aziendali nei dispositivi iOS.

1. Sul dispositivo mobile, avviare un'app Web browser, ad esempio Safari.

2. Passare a https://myapps.microsoft.como usare il collegamento fornito dall'organizzazione se indirizzare l'utente a una pagina personalizzata, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione che sono disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Accedere e usare il portale app personali in un dispositivo Android

Visualizzare e usare il portale **app personali** in un dispositivo Android.

1. Sul dispositivo mobile, avviare un'app Web browser, ad esempio Google Chrome.

2. Passare a https://myapps.microsoft.como usare il collegamento fornito dall'organizzazione se indirizzare l'utente a una pagina personalizzata, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Aggiungere una nuova app al portale app personali

Se l'amministratore ha concesso l'autorizzazione, è possibile aggiungere una nuova app alla pagina **app** .

1. Dalla pagina **app** eseguire una delle operazioni seguenti:
    - Se si usa l'esperienza My Apps originale, selezionare **Aggiungi app** , come illustrato.

      ![Pagina Aggiungi app, nel portale app personali](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - Se ci si trova nell'esperienza app personali aggiornata, selezionare **Aggiungi app self-service**.

       ![Pagina Aggiungi app, nel portale app personali in myapplications.microsoft.com](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. Selezionare l'app che si vuole aggiungere dall'elenco fornito e quindi selezionare **Aggiungi**.

3. L'app viene aggiunta all'elenco nella pagina **app** .

    Prima di aggiungere alcune app potrebbe essere necessaria l'approvazione dell'amministratore. Quando si verifica questa situazione, l'app non viene aggiunta alla pagina **app** fino a quando non viene approvata dall'amministratore.

## <a name="start-a-cloud-based-app"></a>Avviare un'app basata sul cloud

È possibile avviare una qualsiasi delle app basate sul cloud disponibili dal portale **app personali** . Verranno visualizzate solo le app di cui si dispone delle autorizzazioni per l'utilizzo.

- Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

## <a name="activities-in-the-my-apps-portal"></a>Attività nel portale app personali

Dopo aver ottenuto il portale **app personali** , è possibile:

- Organizzare le app nelle diverse categorie create e fornite dall'organizzazione. Per altre informazioni, vedere [Accedere e usare le raccolte nel portale App personali](my-applications-portal-workspaces.md).

- Esaminare, aggiornare o revocare le autorizzazioni concesse alle applicazioni. Per altre informazioni, vedere [Modificare o revocare le autorizzazione per le applicazioni nel portale App personali](my-applications-portal-permissions-saved-accounts.md).

>[!Note]
>Se non sono disponibili raccolte o categorie, significa che l'amministratore non le ha configurate né condivise. Rivolgersi all'help desk dell'organizzazione per ottenere ulteriore assistenza o le autorizzazioni per visualizzare le raccolte condivise.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver ottenuto la pagina **app** , è possibile:

- [Modificare le informazioni sul profilo](my-apps-portal-end-user-update-profile.md)

- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md)

- [Eseguire verifiche di accesso personali](my-apps-portal-end-user-access-reviews.md)
