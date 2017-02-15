---
title: Accesso condizionale di Azure Active Directory | Documentazione Microsoft
description: Usare il controllo di accesso condizionale di Azure Active Directory per controllare condizioni specifiche durante il processo di autenticazione per l&quot;accesso alle applicazioni.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/16/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: af104d47d316b3e46108e7e2ed0771bd06f360af
ms.openlocfilehash: 6c3b547b052bca6999e47ddc10fcce5e1e56fe09


---
# <a name="conditional-access-in-azure-active-directory"></a>Accesso condizionale in Azure Active Directory

> [!div class="op_single_selector"]
> * [Portale di Azure classico](active-directory-conditional-access.md)
> * [Portale di Azure](active-directory-conditional-access-azure-portal.md)

Le funzionalità di controllo dell'accesso condizionale di Azure Active Directory (Azure AD) consentono di proteggere con facilità le risorse sia sul cloud che in locale. I criteri di accesso condizionale, ad esempio l'autenticazione a più fattori, offrono una protezione contro il rischio di furto e phishing delle credenziali. Altri criteri di accesso condizionale garantiscono la sicurezza dei dati dell'organizzazione. Ad esempio, oltre alla richiesta delle credenziali, è possibile applicare un criterio in base al quale possono accedere ai servizi riservati dell'organizzazione solo i dispositivi registrati in un sistema di gestione di dispositivi mobili, come Microsoft Intune.

## <a name="prerequisites"></a>Prerequisiti
L'accesso condizionale di Azure AD è una funzionalità di [Azure Active Directory Premium](http://www.microsoft.com/identity). Ogni utente che accede a un'applicazione a cui sono applicati i criteri di accesso condizionale deve avere una licenza di Azure AD Premium. Per altre informazioni sui requisiti relativi alle licenza, vedere [Report Utilizzo senza licenza](https://aka.ms/utc5ix).

## <a name="how-is-conditional-access-control-enforced"></a>Applicazione del controllo di accesso condizionale
Dopo l'implementazione del controllo di accesso condizionale, Azure AD verifica condizioni specifiche quando si imposta un utente per l'accesso a un'applicazione. Una volta soddisfatti i requisiti di accesso, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.  

![Panoramica dell'accesso condizionale](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Condizioni
Di seguito sono riportate condizioni che è possibile includere in un criterio di accesso condizionale:

* **Appartenenza a un gruppo**. Controllare che un utente acceda sulla base dell'appartenenza a un gruppo.
* **Località**. Usare la posizione dell'utente per attivare l'autenticazione a più fattori e usare i controlli di blocco quando un utente non si trova su una rete attendibile.
* **Piattaforma del dispositivo**. Usare la piattaforma del dispositivo, ad esempio iOS, Android, Windows Mobile e Windows, come condizione per l'applicazione dei criteri.
* **Dispositivo abilitato**. Lo stato di abilitazione o disabilitazione del dispositivo viene convalidato durante la valutazione dei criteri del dispositivo. Se nella directory si disabilita un dispositivo perso o rubato, tale dispositivo non rispetta più i requisiti dei criteri.
* **Accesso e rischi per l'utente**. È possibile usare [Azure AD Identity Protection](active-directory-identityprotection.md) per i criteri di rischio dell'accesso condizionale. Tali criteri assicurano all'organizzazione una protezione avanzata in base a eventi di rischio e attività di accesso anomale.

## <a name="controls"></a>Controlli
Di seguito sono riportati controlli che è possibile usare per applicare un criterio di accesso condizionale:

* **Multi-Factor Authentication**. È possibile richiedere l'autenticazione avanzata tramite l'autenticazione a più fattori. È possibile usare l'autenticazione a più fattori con Azure Multi-Factor Authentication o mediante un provider di autenticazione a più fattori, in combinazione con AD FS (Active Directory Federation Services). L'uso dell'autenticazione a più fattori assicura la protezione delle risorse nel caso in cui un utente non autorizzato abbia avuto l'accesso alle credenziali di un utente valido.
* **Blocco**. È possibile applicare condizioni quali la posizione dell'utente per bloccare l'accesso dell'utente stesso. È ad esempio possibile bloccare l'accesso quando un utente non si trova in una rete attendibile.
* **Dispositivi conformi**. È possibile impostare criteri di accesso condizionale a livello di dispositivo. È possibile impostare un criterio in base al quale possono accedere alle risorse dell'organizzazione soltanto i computer aggiunti a un dominio o registrati in un sistema di gestione di dispositivi mobili. Ad esempio, è possibile usare Intune per verificare la conformità dei dispositivi e quindi segnalarla a Azure AD affinché ne tenga conto durante l'accesso alle applicazioni. Per istruzioni dettagliate su come usare Intune per proteggere le applicazioni e i dati, vedere [Proteggere le applicazioni e i dati con Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). È inoltre possibile usare Intune per applicare la protezione dei dati per i dispositivi smarriti o rubati. Per altre informazioni, vedere [Proteggere i dati con la cancellazione completa o selettiva tramite Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Applicazioni
È possibile applicare un criterio di accesso condizionale a livello di applicazione. Impostare i livelli di accesso per le applicazioni e i servizi nel cloud o in locale. I criteri vengono applicati direttamente al sito Web o al servizio. I criteri vengono applicati per l'accesso al browser e alle applicazioni che hanno accesso al servizio.

## <a name="device-based-conditional-access"></a>Accesso condizionale basato su dispositivo
È possibile limitare l'accesso alle applicazioni da dispositivi registrati con Azure AD e che soddisfano determinate condizioni. L'accesso condizionale basato su dispositivo protegge le risorse di un'organizzatine da tentativi di accesso dalle posizioni seguenti:

* Dispositivi sconosciuti o non gestiti.
* Dispositivi che non soddisfano i criteri di sicurezza impostati dall'organizzazione.

I criteri possono essere impostati in base ai requisiti seguenti:

* **Dispositivi aggiunti a un dominio**. È possibile impostare criteri per limitare l'accesso ai soli dispositivi aggiunti a un dominio di Active Directory locale e registrati con Azure AD. Questo criterio si applica ai computer desktop, portatili o tablet aziendali Windows.
  Per altre informazioni su come configurare la registrazione automatica dei dispositivi aggiunti a un dominio con Azure AD, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Dispositivi conformi**. È possibile impostare criteri per limitare l'accesso ai dispositivi contrassegnati come **conformi** nella directory del sistema di gestione. Con questo criterio, l'accesso viene limitato ai soli dispositivi conformi ai criteri di sicurezza, ad esempio l'applicazione della crittografia dei file. È possibile usare questo criterio per limitare l'accesso dai dispositivi seguenti:
  
  * **Dispositivi Windows aggiunti a un dominio**. Dispositivi gestiti da System Center Configuration Manager Current Branch in una configurazione ibrida.
  * **Dispositivi Windows 10 mobili o personali**. Dispositivi gestiti da Intune o da un sistema di gestione di dispositivi mobili di terze parti supportato.
  * **Dispositivi iOS e Android**. Dispositivi gestiti da Intune.

Gli utenti che intendono accedere ad applicazioni protette da un criterio dell'autorità di certificazione basato su dispositivo devono accedere all'applicazione da un dispositivo che soddisfa i requisiti di questo criterio. Se si tenta di accedere da un dispositivo che non soddisfa i requisiti del criterio, l'accesso viene negato.

Per informazioni su come configurare un criterio dell'autorità di certificazione basato su dispositivo in Azure AD, vedere [Impostare criteri di accesso condizionale basato su dispositivo per applicazioni connesse a Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Risorse
Vedere le categorie di risorse e gli articoli seguenti per informazioni sull'impostazione dell'accesso condizionale per l'organizzazione.

### <a name="multi-factor-authentication-and-location-policies"></a>Criteri basati sull'autenticazione a più fattori e la località
* [Introduzione all'accesso condizionale alle app connesse di Azure AD basate su gruppi, località e criteri di autenticazione a più fattori](active-directory-conditional-access-azuread-connected-apps.md)
* [Applicazioni supportate](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>Accesso condizionale basato su dispositivo
* [Impostare criteri di accesso condizionale basato su dispositivo per applicazioni connesse a Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Risoluzione dei problemi di accesso di Azure Active Directory](active-directory-conditional-access-device-remediation.md)
* [Proteggere i dati con la cancellazione selettiva o completa tramite Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>Protezione delle risorse in base ai rischi di accesso
* [Azure AD identity protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti sull'accesso condizionale](active-directory-conditional-faqs.md)
* [Riferimento tecnico](active-directory-conditional-access-technical-reference.md)




<!--HONumber=Feb17_HO2-->


