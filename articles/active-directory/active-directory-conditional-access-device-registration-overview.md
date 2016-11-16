---
title: Panoramica di Registrazione dispositivo Azure Active Directory | Documentazione Microsoft
description: "rappresenta il fondamento per gli scenari di accesso condizionale basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo di Azure Active Directory effettua il provisioning del dispositivo con un&quot;identità che viene usata per autenticare il dispositivo quando l&quot;utente esegue l&quot;accesso."
services: active-directory
keywords: registrazione dispositivo, abilitare registrazione dispositivo, registrazione dispositivo e software MDM
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: 1e92c1a2-01b8-4225-950b-373cd601b035
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: Markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 98f1b1856a6c457349decefeb277208a3b483bad


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Introduzione a Registrazione dispositivo Azure Active Directory
Registrazione dispositivo di Azure Active Directory rappresenta il fondamento per gli scenari di accesso condizionale basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo Azure Active Directory fornisce al dispositivo un'identità che viene usata per autenticare il dispositivo quando l'utente esegue l'accesso. Il dispositivo autenticato e gli attributi del dispositivo possono quindi essere usati per imporre criteri di accesso condizionale per le applicazioni locali e ospitate nel cloud.

Insieme a una soluzione di gestione dei dispositivi mobili (MDM) come Microsoft Intune, gli attributi del dispositivo in Azure Active Directory vengono aggiornati con informazioni aggiuntive sul dispositivo. Ciò permette di creare regole di accesso condizionale che subordinano l'accesso dai dispositivi al rispetto dei propri standard di sicurezza e conformità. Per altre informazioni sulla registrazione dei dispositivi in Microsoft Intune, vedere [Registrare i dispositivi per la gestione in Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Scenari abilitati da Registrazione dispositivo di Azure Active Directory
Registrazione dispositivo Azure Active Directory include il supporto per dispositivi iOS, Android e Windows. È possibile che i singoli scenari in cui viene usato il servizio Registrazione dispositivo di Azure AD prevedano requisiti più specifici e il supporto di determinate piattaforme. Questi scenari sono i seguenti:

* **Accesso condizionale alle applicazioni ospitate in locale**: è possibile usare i dispositivi registrati con criteri di accesso per le applicazioni configurate per l'uso di AD FS con Windows Server 2012 R2. Per altre informazioni su come configurare l'accesso condizionale in locale, vedere [Configurazione dell'accesso condizionale in locale usando il servizio Registrazione del dispositivo di Azure Active Directory](active-directory-conditional-access-on-premises-setup.md).
* **Accesso condizionale per applicazioni di Office 365 con Microsoft Intune** : gli amministratori IT possono effettuare il provisioning dei criteri di accesso condizionale dei dispositivi per proteggere le risorse aziendali, consentendo allo stesso tempo agli operatori dei sistemi informativi che usano dispositivi compatibili di accedere ai servizi. Per altre informazioni, vedere [Criteri di accesso condizionale dei dispositivi per i servizi di Office 365](active-directory-conditional-access-device-policies.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Configurazione della Registrazione dispositivo di Azure Active Directory
È necessario abilitare Registrazione dispositivo Azure AD nel portale di Azure in modo che i dispositivi mobili possano individuare il servizio cercando record DNS noti. È necessario configurare il DNS della società in modo che i dispositivi Windows 10, Windows 8.1, Windows 7, Android e iOS possano trovare e usare il servizio.
È possibile visualizzare e abilitare/disabilitare i dispositivi registrati tramite il portale dell'amministratore in Azure Active Directory.

> [!NOTE]
> Per le istruzioni più recenti su come configurare la registrazione automatica dei dispositivi, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

### <a name="enable-azure-active-directory-device-registration-service"></a>Abilitare il servizio Registrazione dispositivo Azure Active Directory
1. Accedere al portale di Microsoft Azure come amministratore.
2. Nel riquadro sinistro selezionare **Active Directory**.
3. Selezionare la propria directory nella scheda **Directory** .
4. Selezionare la scheda **Configura** .
5. Scorrere fino alla sezione **Dispositivi**.
6. Selezionare **Tutti** per **Gli utenti possono aggiungere dispositivi all'area di lavoro**.
7. Selezionare il numero massimo di dispositivi da autorizzare per l'utente.

> [!NOTE]
> La registrazione con Microsoft Intune o con Gestione dispositivi mobili per Office 365 richiede l'aggiunta all'area di lavoro. Se è stato configurato uno di questi servizi, sarà selezionata l'opzione TUTTI e il pulsante sarà disabilitato.
> 
> 

Per impostazione predefinita, l'autenticazione a due fattori non è abilitata per il servizio. L'autenticazione a due fattori è tuttavia consigliabile quando si registra un dispositivo.

* Prima di richiedere l'autenticazione a due fattori per questo servizio, è necessario configurare un provider di autenticazione a due fattori in Azure Active Directory e configurare gli account utente per Multi-Factor Authentication. Vedere l'articolo relativo all'[aggiunta di Multi-Factor Authentication ad Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).
* Se si usa AD FS con Windows Server 2012 R2, è necessario configurare un modulo di autenticazione a due fattori in AD FS. Vedere l'articolo relativo all'[uso di Multi-Factor Authentication con Active Directory Federation Services](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurare l'individuazione di Registrazione dispositivo di Azure Active Directory
I dispositivi client Windows 7 e Windows 8.1 individueranno il servizio Registrazione dispositivo Azure Active Directory combinando il nome dell'account utente con il nome di un server di registrazione dispositivo noto.

È necessario creare un record DNS CNAME che punti al record A associato al servizio Registrazione dispositivo Azure Active Directory. Il record CNAME deve usare il prefisso noto enterpriseregistration seguito dal suffisso UPN usato per gli account utente nell'organizzazione. Se l'organizzazione usa più suffissi UPN, è necessario creare più record CNAME nel DNS.

Ad esempio, se nell'organizzazione si usano due suffissi UPN denominati @contoso.com e @region.contoso.com,, si creeranno i record DNS seguenti.

| Voce | Tipo | Indirizzo |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Visualizzare e gestire gli oggetti dispositivo di Azure Active Directory
1. Nel portale dell'amministratore di Azure è possibile visualizzare, bloccare e sbloccare i dispositivi. Un dispositivo bloccato non avrà più accesso alle applicazioni configurate per consentire solo i dispositivi registrati.
2. Accedere al portale di Microsoft Azure come Amministratore.
3. Nel riquadro sinistro selezionare **Active Directory**.
4. Selezionare la propria directory.
5. Selezionare la scheda **Utenti** , quindi selezionare un utente per visualizzare i relativi dispositivi.
6. Selezionare la scheda **Dispositivi** .
7. Selezionare **Dispositivi registrati** dal menu a discesa.
8. Qui è possibile visualizzare, bloccare o sbloccare i dispositivi registrati dell'utente.

## <a name="additional-topics"></a>Argomenti aggiuntivi
È possibile registrare i dispositivi Windows 7 e Windows 8.1 aggiunti a un dominio con Registrazione dispositivo di Azure AD. Gli argomenti seguenti includono altre informazioni sui prerequisiti e sulle procedure necessarie per configurare la registrazione del dispositivo per i dispositivi Windows 7 e Windows 8.1.

* [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration.md)
* [Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Configurazione della registrazione automatica per i dispositivi appartenenti a un dominio di Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows 10 aggiunti a un dominio](active-directory-azureadjoin-devices-group-policy.md)




<!--HONumber=Nov16_HO2-->


