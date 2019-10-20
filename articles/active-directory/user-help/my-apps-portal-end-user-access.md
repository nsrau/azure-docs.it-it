---
title: Accedere e usare le app nel portale app personali-Azure Active Directory | Microsoft Docs
description: Informazioni su come accedere al portale delle app personali.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 901067e198616dba390e0e1162ec26a0b11768f1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "68383131"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>Accedere e usare le app nel portale app personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

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

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Per modificare il portale delle app personali usando l'estensione
È possibile scegliere il numero di app da visualizzare nella sezione **usato di recente** e decidere se consentire il reindirizzamento degli URL interni dell'organizzazione.

1. Selezionare l'icona dell'estensione per l' **accesso sicuro alle app personali** ![Extension icona ](media/my-apps-portal/my-apps-portal-extension-icon.png) a destra della barra degli **indirizzi** e quindi selezionare **Accedi per**iniziare.

2. Fare clic con il pulsante destro del mouse sull'icona **delle impostazioni** ![Settings icona ](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), quindi scegliere **Impostazioni**.

3. Nella casella **Impostazioni** selezionare il numero di app recenti da visualizzare nel portale e se consentire agli URL interni dell'organizzazione di essere reindirizzati in modo da poterli usare in remoto.

    ![Pagina delle impostazioni dell'estensione che mostra le personalizzazioni disponibili](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Accedere e usare il portale app personali per dispositivo
È possibile accedere e usare il portale app personali nel computer, da un browser gestito da Intune o da un dispositivo mobile iOS o Android.

![Pagina app nel portale app personali](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Accedere e usare il portale app personali nel computer
Se si dispone dell'autorizzazione per accedere e usare le app basate sul cloud dell'organizzazione, è possibile accedervi tramite il portale **app personali** .

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser e passare a https://myapps.microsoft.com o usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Accedere e usare il portale app personali in un browser gestito da Intune

Consente di visualizzare e usare le app dell'organizzazione da una Intune Managed Browser nei dispositivi iOS e Android.

1. Nel dispositivo mobile scaricare e installare l'app Intune Managed Browser dall' [App Store di Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e la [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Aprire l'app Intune Managed Browser, passare a https://myapps.microsoft.com o usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Accedere e usare il portale app personali in un dispositivo iOS

Visualizzare e usare il portale **app personali** da un dispositivo iPhone o iPad, che esegue iOS 7 o versione successiva. È anche possibile installare l' [app](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) per dispositivi mobili app personali per accedere alle app aziendali nei dispositivi iOS. 

1. Sul dispositivo mobile, avviare un'app Web browser, ad esempio Safari.

2. Passare a https://myapps.microsoft.com o usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Accedere e usare il portale app personali in un dispositivo Android

Visualizzare e usare il portale **app personali** in un dispositivo Android.

1. Sul dispositivo mobile, avviare un'app Web browser, ad esempio Google Chrome.

2. Passare a https://myapps.microsoft.com o usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

3. Dalla pagina **app** selezionare l'app che si vuole iniziare a usare.

    Viene aperta una nuova pagina per l'app, in cui è possibile accedere (se necessario) o iniziare a usare l'app.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Aggiungere una nuova app al portale app personali

Se l'amministratore ha concesso l'autorizzazione, è possibile aggiungere una nuova app alla pagina **app** .

1. Dalla pagina **app** selezionare **Aggiungi app**.

    Verrà visualizzata la pagina **Aggiungi app** .

    ![Pagina Aggiungi app, nel portale app personali](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Selezionare l'app che si vuole aggiungere dall'elenco fornito e quindi selezionare **Aggiungi**.

3. L'app viene aggiunta all'elenco nella pagina **app** .

    Prima di aggiungere alcune app potrebbe essere necessaria l'approvazione dell'amministratore. In questa situazione, l'app non viene aggiunta alla pagina **app** fino a quando non viene approvata dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver ottenuto la pagina **app** , è possibile:

- [Modificare le informazioni sul profilo](my-apps-portal-end-user-update-profile.md)

- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md)

- [Eseguire verifiche di accesso personali](my-apps-portal-end-user-access-reviews.md)
