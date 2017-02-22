---
title: Impostare criteri di accesso condizionale basato su dispositivo per applicazioni connesse ad Azure Active Directory | Microsoft Docs
description: Impostare criteri di accesso condizionale basato su dispositivo per applicazioni connesse ad Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133


---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Impostare criteri di accesso condizionale basato su dispositivo per applicazioni connesse ad Azure Active Directory
L'accesso condizionale basato su dispositivo di Azure AD (Azure Active Directory) consente di proteggere le risorse dell'organizzazione dalle minacce seguenti:

* Tentativi di accesso da dispositivi sconosciuti o non gestiti.
* Dispositivi che non soddisfano i criteri di sicurezza dell'organizzazione.

Per una panoramica dell'accesso condizionale, vedere [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md).

È possibile impostare i criteri di accesso condizionale basato su dispositivo per proteggere le applicazioni seguenti:

* Office 365 SharePoint Online per la protezione dei documenti e dei siti dell'organizzazione
* Office 365 Exchange Online per la sicurezza dei messaggi di posta elettronica aziendali
* Applicazioni SaaS (Software as a Service) connesse ad Azure AD a scopo di autenticazione
* Applicazioni locali pubblicate usando i servizi del proxy dell'applicazione di Azure AD

Per impostare un criterio di accesso condizionale basato su dispositivo, nel portale di Azure accedere all'applicazione desiderata nella relativa directory.

  ![Elenco delle applicazioni nella directory del portale di Azure](./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")

Selezionare l'applicazione e quindi fare clic sulla scheda **Configura** per impostare i criteri di accesso condizionale.  

  ![Configurare l'applicazione](./media/active-directory-conditional-access-policy-connected-applications/02.png "Device based access rules")

Per impostare i criteri di accesso condizionale basato su dispositivo, nella sezione **Regole di accesso in base al dispositivo**, alla voce **Abilita regole di accesso**, selezionare **On**.

Un criterio di accesso condizionale basato su dispositivo è costituito da tre componenti:

* **Applica a**. L'ambito di utenti a cui viene applicato il criterio.
* **Regole dispositivo**. Le condizioni che i dispositivi devono soddisfare per poter accedere all'applicazione.
* **Imposizione dell'applicazione**. Le applicazioni client (native e browser) a cui si applicano i criteri.
  
  ![I tre componenti di un criterio di accesso condizionale basato su dispositivo](./media/active-directory-conditional-access-policy-connected-applications/03.png "Device based access rules")

## <a name="select-the-users-the-policy-applies-to"></a>Selezionare gli utenti a cui vengono applicati i criteri
Nella sezione **Apply to** (Applica a) è possibile selezionare l'ambito degli utenti a cui si applica questo criterio.

Per la creazione di un ambito di criteri di accesso per gli utenti sono disponibili due opzioni:

* **Tutti gli utenti**. I criteri vengono applicati a tutti gli utenti che accedono all'applicazione.
* **Gruppi**. L'applicazione dei criteri viene limitata agli utenti membri di un gruppo specifico.

![Applicazione dei criteri a tutti gli utenti o a un gruppo](./media/active-directory-conditional-access-policy-connected-applications/11.png "Apply to")

 Per escludere un utente da un criterio, selezionare la casella di controllo **Escludi**. Questo risulta utile quando è necessario assegnare autorizzazioni a un utente specifico per l'accesso temporaneo all'applicazione. Selezionare questa opzione, ad esempio, se alcuni utenti possiedono dispositivi non predisposti per l'accesso condizionale. È possibile che i dispositivi non siano ancora registrati o che la loro conformità stia per scadere.

## <a name="select-the-conditions-that-devices-must-meet"></a>Selezionare le condizioni che i dispositivi devono soddisfare
Usare **Regole dispositivo** per impostare le condizioni che un dispositivo deve soddisfare per poter accedere all'applicazione.

È possibile impostare criteri di accesso condizionale basato su dispositivo per i tipi di dispositivi seguenti:

* Aggiornamento dell'anniversario di Windows 10, Windows 8.1 e Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2
* Dispositivi iOS (iPad, iPhone)
* Dispositivi Android

Presto sarà disponibile il supporto per dispositivi Mac.

  ![Applicazione dei criteri ai dispositivi](./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")

> [!NOTE]
> Per informazioni sulle differenze tra dispositivi aggiunti a un dominio e dispositivi aggiunti ad Azure AD, vedere [Uso di dispositivi Windows 10 in azienda](active-directory-azureadjoin-windows10-devices.md).
> 
> 

Le opzioni disponibili sono due:

* **Tutti i dispositivi devono essere conformi**. Tutte le piattaforme dei dispositivi che accedono all'applicazione devono essere conformi. Ai dispositivi in esecuzione su piattaforme che non supportano l'accesso condizionale basato su dispositivo viene negato l'accesso.
* **Solo i dispositivi selezionati devono essere compatibili**. Devono essere conformi solo le piattaforme dei dispositivi selezionati. Viene consentito l'accesso alle altre piattaforme o ad altre piattaforme che possono accedere all'applicazione.
  
  ![Impostazione dell'ambito per le regole dispositivo](./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")

I dispositivi aggiunti al dominio Azure AD sono conformi se contrassegnati con la dicitura **Conforme** nella directory da Intune o da un sistema di gestione di dispositivi mobili di terze parti che si integra con Azure AD.

Un dispositivo aggiunto al dominio è conforme se:

* È registrato con Azure AD. Molte organizzazioni considerano attendibili i dispositivi aggiunti a un dominio.
* È contrassegnato come **Conforme** in Azure AD da System Center Configuration Manager.
  
  ![Dispositivi conformi aggiunti a un dominio](./media/active-directory-conditional-access-policy-connected-applications/06.png "Device Rules")

I dispositivi personali Windows sono conformi se contrassegnati con la dicitura **Conforme** nella directory da Intune o da un sistema di gestione di dispositivi mobili di terze parti che si integra con Azure AD.

I dispositivi non Windows sono conformi se contrassegnati con la dicitura **Conforme** nella directory da Intune.

> [!NOTE]
> Per altre informazioni su come configurare Azure AD per la conformità dei dispositivi in differenti sistemi di gestione, vedere [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md).
> 
> 

Per un criterio di accesso basato su dispositivo è possibile selezionare una o più piattaforme di dispositivi. Sono inclusi Android, iOS, Windows Mobile (cellulari e tablet Windows 8.1) e Windows (tutti gli altri dispositivi Windows, compresi tutti i dispositivi Windows 10).
La valutazione dei criteri viene effettuata solo sulle piattaforme selezionate. Si supponga che un dispositivo che non esegue una delle piattaforme selezionate tenti di accedere a un'applicazione: tale dispositivo può accedere all'applicazione se l'utente ha accesso all'applicazione stessa. Non viene valutato alcun criterio del dispositivo.

![Selezione delle piattaforme per le regole dispositivo](./media/active-directory-conditional-access-policy-connected-applications/07.png "Device Rules")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>Impostare la valutazione dei criteri per un tipo di applicazione
Nella sezione **Imposizione dell'applicazione** impostare il tipo di applicazioni per cui verranno valutati i criteri per l'accesso degli utenti o dei dispositivi.

Per il tipo di applicazione da includere sono disponibili due opzioni:

* Browser e applicazioni native
* Solo applicazioni native

![Scelta tra browser e applicazioni native](./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")

Per applicare i criteri di accesso per le applicazioni, selezionare **Per browser e applicazioni native**. È quindi possibile includere:

* Browser (ad esempio Microsoft Edge in Windows 10 o Safari in iOS).
* Applicazioni che usano Active Directory Authentication Library (ADAL) in qualsiasi piattaforma oppure l'API di Gestione account Web in Windows 10.

> [!NOTE]
> Per altre informazioni sui browser supportati e per considerazioni sugli utenti che accedono ad applicazioni con protezione dell'autorità di certificazione in base al dispositivo, vedere [Accesso condizionale in Azure Active Directory](active-directory-conditional-access.md).
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Proteggere l'accesso alla posta elettronica da applicazioni basate su Exchange Active Sync
Nelle applicazioni di Office 365 Exchange Online è possibile usare Exchange ActiveSync per bloccare l'accesso alla posta elettronica alle applicazioni di posta elettronica basate su Exchange ActiveSync.

![Opzioni di conformità a Exchange ActiveSync](./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")

![Richiedi un dispositivo conforme per l'accesso alla posta elettronica](./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")

## <a name="next-steps"></a>Passaggi successivi
* [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO4-->


