---
title: Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio | Microsoft Docs
description: Registrare con Azure Active Directory i dispositivi Windows aggiunti a un dominio in modo automatico e invisibile all&quot;utente.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 374046ce97e3fec9b94815acb5805250d867b6f3
ms.openlocfilehash: a0bb6e4dc2e6ce80674841e2857e036384140ef2


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Introduzione a Registrazione dispositivo Azure Active Directory

Registrazione dispositivo Azure Active Directory rappresenta il fondamento per gli scenari di accesso condizionale basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo Azure Active Directory fornisce al dispositivo un'identità che viene usata per autenticare il dispositivo quando l'utente esegue l'accesso. Il dispositivo autenticato e gli attributi del dispositivo possono quindi essere usati per imporre criteri di accesso condizionale per le applicazioni locali e ospitate nel cloud.

Insieme a una soluzione di gestione dei dispositivi mobili (MDM) come Microsoft Intune, gli attributi del dispositivo in Azure Active Directory vengono aggiornati con informazioni aggiuntive sul dispositivo. Ciò permette di creare regole di accesso condizionale che subordinano l'accesso dai dispositivi al rispetto dei propri standard di sicurezza e conformità. Per altre informazioni sulla registrazione dei dispositivi in Microsoft Intune, vedere l'articolo "Registrare i dispositivi per la gestione in Intune".
Gli scenari consentiti da Registrazione dispositivo Azure Active Directory includono il supporto per dispositivi iOS, Android e Windows. È possibile che i singoli scenari in cui viene usato il servizio Registrazione dispositivo di Azure AD prevedano requisiti più specifici e il supporto di determinate piattaforme. 

Questi scenari sono i seguenti:

- **Accesso condizionale per applicazioni di Office 365 con Microsoft Intune:** gli amministratori IT possono effettuare il provisioning dei criteri di accesso condizionale dei dispositivi per proteggere le risorse aziendali, consentendo allo stesso tempo agli Information Worker che usano dispositivi compatibili di accedere ai servizi. Per altre informazioni, vedere Criteri di accesso condizionale dei dispositivi per i servizi di Office 365.

- **Accesso condizionale alle applicazioni ospitate in locale:** è possibile usare i dispositivi registrati con criteri di accesso per le applicazioni configurate per l'uso di AD FS con Windows Server 2012 R2. Per altre informazioni su come configurare l'accesso condizionale in locale, vedere [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-on-premises-setup.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Configurazione della Registrazione dispositivo di Azure Active Directory

Per configurare il servizio Registrazione dispositivo, è possibile procedere in diversi modi:

1.  Registrazione dei dispositivi quando vengono aggiunti a un dominio di Azure AD. Per altre informazioni, è possibile approfondire gli argomenti relativi all'aggiunta ad Azure AD e alle impostazioni necessarie per l'aggiunta di utenti a un dominio di Azure AD.

2.  Registrazione dei dispositivi quando gli utenti aggiungono account aziendali o dell'istituto di istruzione a Windows in un dispositivo personale o quando i dispositivi mobili si connettono a risorse di lavoro che richiedono la registrazione. A tale scopo, è necessario abilitare Registrazione dispositivo Azure AD nel portale di Azure. 

3.  Registrazione automatica dei dispositivi per i computer aggiunti a un dominio tradizionale. È necessario configurare Azure AD Connect prima di proseguire con la registrazione automatica dei dispositivi.

Per le istruzioni più aggiornate, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).  
È anche possibile visualizzare e abilitare/disabilitare i dispositivi registrati usando il portale amministratore in Azure Active Directory.

## <a name="enable-the-azure-active-directory-device-registration-service"></a>Abilitare il servizio Registrazione dispositivo Azure Active Directory

**Per abilitare il servizio Registrazione dispositivo Azure Active Directory:**

1.  Accedere al portale di Microsoft Azure come amministratore.

2.  Nel riquadro sinistro selezionare **Active Directory**.

3.  Selezionare la propria directory nella scheda Directory.

4.  Fare clic su **Configure**.

5.  Scorrere fino a **Dispositivi**.

6.  Selezionare Tutti per Gli utenti possono registrare i propri dispositivi in Azure AD.

7.  Selezionare il numero massimo di dispositivi da autorizzare per l'utente.

L'iscrizione a Microsoft Intune o Gestione dei dispositivi mobili per Office 365 richiede la registrazione del dispositivo. Se è stato configurato uno di questi servizi, sarà selezionata l'opzione **Tutti** e disabilitata l'opzione **Nessuno**. Assicurarsi che siano configurati correttamente e di avere le licenze appropriate.

Per impostazione predefinita, l'autenticazione a due fattori non è abilitata per il servizio. L'autenticazione a due fattori è tuttavia consigliabile quando si registra un dispositivo.

- Prima di richiedere l'autenticazione a due fattori per questo servizio, è necessario configurare un provider di autenticazione a due fattori in Azure Active Directory e configurare gli account utente per Multi-Factor Authentication. Vedere Aggiunta di Multi-Factor Authentication ad Azure Active Directory.

- Se si usa AD FS con Windows Server 2012 R2, è necessario configurare un modulo di autenticazione a due fattori in AD FS. Vedere l'articolo relativo all'uso di Multi-Factor Authentication con Active Directory Federation Services.

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Visualizzare e gestire gli oggetti dispositivo di Azure Active Directory

Nel portale dell'amministratore di Azure è possibile visualizzare, bloccare e sbloccare i dispositivi. Un dispositivo bloccato non avrà più accesso alle applicazioni configurate per consentire solo i dispositivi registrati.

**Per visualizzare e gestire oggetti dispositivo in Azure Active Directory:**
 
1.  Accedere al portale di Microsoft Azure come amministratore.

2.  Nel riquadro sinistro selezionare **Active Directory**.

3.  Selezionare la propria directory.

4.  Selezionare **Utenti**. 

5.  Fare clic sull'utente per il quale si vogliono visualizzare i dispositivi.

6.  Selezionare **Dispositivi**.

7.  Selezionare **Dispositivi registrati**.

A questo punto è possibile esaminare, bloccare o sbloccare i dispositivi registrati dell'utente.
I dispositivi Windows 10 aggiunti a un dominio locale e registrati automaticamente non vengono visualizzati nella scheda Utenti. Usare il comando di PowerShell Get-MsolDevice per trovare tutti i dispositivi dell'organizzazione. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).





<!--HONumber=Feb17_HO2-->


