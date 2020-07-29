---
title: Configurare le password dell'app per Azure Multi-Factor Authentication-Azure Active Directory
description: Informazioni su come configurare e usare le password dell'app per le applicazioni legacy in Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b247b64d563bc2b12c5bffff6a460d77cb96207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485484"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Abilitare e usare Multi-Factor Authentication di Azure con le applicazioni legacy usando le password dell'app

Alcune applicazioni, ad esempio Office 2010 o versioni precedenti e Apple Mail prima di iOS 11, non supportano la funzionalità di autenticazione a più fattori. Le app non sono configurate per accettare una forma secondaria di autenticazione o richiesta. Per usare queste applicazioni in modo sicuro con Azure Multi-Factor Authentication abilitato per gli account utente, è possibile usare le password dell'app. Queste password dell'app hanno sostituito la password tradizionale per consentire a un'app di ignorare l'autenticazione a più fattori e di funzionare correttamente.

L'autenticazione moderna è supportata per i client di Microsoft Office 2013 e versioni successive. I client Office 2013, tra cui Outlook, supportano i protocolli di autenticazione moderni e possono essere abilitati per lavorare con la verifica in due passaggi. Dopo averlo abilitato, il client non richiede le password dell'app.

Questo articolo illustra come abilitare e usare le password dell'app per le applicazioni legacy che non supportano i prompt di autenticazione a più fattori.

>[!NOTE]
> Le password dell'app non funzionano con i criteri di autenticazione a più fattori basati sull'accesso condizionale e l'autenticazione moderna.

## <a name="overview-and-considerations"></a>Panoramica e considerazioni

Quando un account utente è abilitato per Azure Multi-Factor Authentication, il prompt di accesso normale viene interrotto da una richiesta di verifica aggiuntiva. Alcune applicazioni meno recenti non comprendono questa pausa nel processo di accesso, quindi l'autenticazione ha esito negativo. Per gestire la sicurezza degli account utente e lasciare abilitata la Multi-Factor Authentication di Azure, è possibile usare le password dell'app anziché il nome utente e la password normali dell'utente. Quando si usa una password dell'app durante l'accesso, non c'è alcuna richiesta di verifica aggiuntiva, quindi l'autenticazione ha esito positivo.

Le password dell'app vengono generate automaticamente, non specificate dall'utente. Questa password generata automaticamente rende più difficile l'individuazione di un utente malintenzionato, quindi è più sicura. Gli utenti non devono tenere traccia delle password o immetterle ogni volta che le password dell'app vengono immesse una sola volta per ogni applicazione.

Quando si usano le password dell'app, si applicano le considerazioni seguenti:

* È previsto un limite di 40 password di app per utente.
* Le applicazioni che memorizzano le password nella cache e le usano in scenari locali possono avere esito negativo perché la password dell'app non è nota all'esterno dell'account aziendale o dell'Istituto di istruzione Un esempio di questo scenario è rappresentato dai messaggi di posta elettronica di Exchange in locale, mentre la posta archiviata si trova nel cloud. In questo scenario non funziona la stessa password.
* Dopo l'abilitazione di Azure Multi-Factor Authentication sull'account di un utente, le password dell'app possono essere usate con la maggior parte dei client non basati su browser, ad esempio Outlook e Microsoft Skype for business. Tuttavia, non è possibile eseguire azioni amministrative usando password di app tramite applicazioni non basate su browser, ad esempio Windows PowerShell. Tali azioni non possono essere eseguite anche se l'utente dispone di un account amministrativo.
    * Per eseguire gli script di PowerShell, creare un account di servizio con una password complessa e non abilitare l'account per la verifica in due passaggi.

>[!WARNING]
> Le password delle app non funzionano in ambienti ibridi in cui i client comunicano con endpoint di individuazione automatica sia locali sia cloud. Le password del dominio sono necessarie per l'autenticazione locale. Le password dell'app sono necessarie per l'autenticazione con il cloud.

### <a name="app-password-names"></a>Nomi delle password dell'app

Per le password dell'app è consigliabile usare nomi che riflettano il dispositivo in cui vengono usate. Se si usa un portatile con app non basate su browser come Outlook, Word ed Excel, creare una sola password dell'app denominata **Portatile** per queste app. Creare un'altra password dell'app denominata **Desktop** per le stesse applicazioni eseguite nel computer desktop.

È consigliabile creare una password dell'app per ogni dispositivo, anziché una password dell'app per ogni applicazione.

## <a name="federated-or-single-sign-on-app-passwords"></a>Password dell'app Single Sign-On o federate

Azure AD supporta la Federazione, o Single Sign-On (SSO), con Active Directory Domain Services locale (AD DS). Se l'organizzazione è federata con Azure AD e si usa Azure Multi-Factor Authentication, si applicano le seguenti considerazioni sulle password per le app:

>[!NOTE]
> I punti seguenti si applicano solo ai clienti federati (SSO).

* Le password dell'app vengono verificate da Azure AD e di conseguenza ignorano la federazione. La federazione viene usata attivamente solo durante l'impostazione delle password dell'app.
* Per gli utenti federati (SSO) non viene contattato il provider di identità (IdP), a differenza del flusso passivo. Le password dell'app vengono archiviate nell'account aziendale o dell'istituto di istruzione. Se un utente lascia l'azienda, le sue informazioni passano all'account aziendale o dell'istituto di istruzione tramite **DirSync** in tempo reale. Per la sincronizzazione, la disabilitazione o l'eliminazione dell'account può richiedere fino a tre ore, che può ritardare la disabilitazione o l'eliminazione della password dell'app in Azure AD.
* Le impostazioni locali di Controllo di accesso client non vengono rispettate dalla funzionalità password dell'app.
* Con la funzionalità password per le app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione locale.

Alcune architetture avanzate richiedono una combinazione di credenziali per l'autenticazione a più fattori con i client. Queste credenziali possono includere nome utente e password dell'account aziendale o dell'istituto di istruzione, oltre alle password dell'app. I requisiti dipendono dal modo in cui viene eseguita l'autenticazione. Per i client che si autenticano in un'infrastruttura locale, sono necessari un nome utente e una password dell'account aziendale o dell'istituto di formazione. Per i client che eseguono l'autenticazione in AD Azure, è necessaria la password dell'app.

Ad esempio, si supponga di disporre dell'architettura seguente:

* Si esegue la federazione dell'istanza locale di Active Directory con Azure AD.
* Si usa Exchange Online.
* Usi Skype for business in locale.
* Usare Multi-Factor Authentication di Azure.

In questo scenario usare le credenziali seguenti:

* Per accedere a Skype for Business, usare il nome utente e la password dell'account aziendale o dell'istituto di istruzione.
* Per accedere alla rubrica da un client Outlook che si connette a Exchange Online, usare una password dell'app.

## <a name="allow-users-to-create-app-passwords"></a>Consentire agli utenti di creare password dell'app

Per impostazione predefinita, gli utenti non possono creare password dell'app. La funzionalità password per le app deve essere abilitata prima che gli utenti possano usarla. Per consentire agli utenti di creare password dell'app, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare e selezionare **Azure Active Directory**, quindi scegliere **utenti**.
3. Selezionare **multi-factor authentication** sulla barra di spostamento nella parte superiore della finestra *utenti* .
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio** selezionare l'opzione **Consenti agli utenti di creare password dell'app per accedere alle app non basate su browser**.

    ![Screenshot del portale di Azure che mostra le impostazioni del servizio per l'autenticazione a più fattori per consentire all'utente di creare password dell'app](media/concept-authentication-methods/app-password-authentication-method.png)

## <a name="create-an-app-password"></a>Creare una password di app

Quando gli utenti completano la registrazione iniziale per Azure Multi-Factor Authentication, è possibile creare password di app al termine del processo di registrazione.

Gli utenti possono creare password delle app anche dopo la registrazione. Per altre informazioni e procedure dettagliate per gli utenti, vedere [Che cosa sono le password per le app in Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come consentire agli utenti di registrarsi rapidamente per Multi-Factor Authentication di Azure, vedere [Cenni preliminari sulla registrazione delle informazioni di sicurezza combinata](concept-registration-mfa-sspr-combined.md).
