---
title: Documentazione tecnica sull'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Informazioni su come usare il controllo di accesso condizionale in Azure Active Directory. Specificare le condizioni per l'autenticazione degli utenti e il controllo dell'accesso all'applicazione. Quando vengono soddisfatte le condizioni specificate, gli utenti vengono autenticati e viene concesso loro l'accesso all'applicazione.
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 4bcca3ee5862a0455b6064d0f9c0a91388db9516
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Documentazione tecnica sull'accesso condizionale di Azure Active Directory

È possibile usare l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) per ottimizzare il modo in cui gli utenti autorizzati possono accedere alle risorse.  

Questo argomento contiene informazioni di supporto per le opzioni di configurazione seguenti di un criterio di accesso condizionale: 

- Assegnazioni delle applicazioni cloud

- Condizione per le piattaforme del dispositivo 

- Condizione per le applicazioni client

- Requisito per le applicazioni client approvate



## <a name="cloud-apps-assignments"></a>Assegnazioni di app cloud

Quando si configura un criterio di accesso condizionale, è necessario [selezionare le app cloud che useranno il criterio](active-directory-conditional-access-azure-portal.md#who). 

![Selezionare le app cloud per il criterio](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Applicazioni cloud Microsoft

È possibile assegnare un criterio di accesso condizionale alle app cloud seguenti di Microsoft:

- Azure Information Protection: [altre informazioni](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (include OneDrive for Business)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Altre applicazioni 

Oltre che alle app cloud Microsoft, è possibile assegnare un criterio di accesso condizionale ai tipi seguenti di app cloud:

- Applicazioni connesse ad Azure AD

- Applicazione SaaS (Software as a Service) federata preintegrata

- Applicazioni che usano password Single Sign-On (SSO)

- Applicazioni line-of-business

- Applicazioni che usano il proxy di applicazione di Azure AD


## <a name="device-platform-condition"></a>Condizione per le piattaforme del dispositivo

In un criterio di accesso condizionale è possibile configurare la condizione per le piattaforme del dispositivo in modo da collegare il criterio al sistema operativo di un client.

![Collegare il criterio di accesso al sistema operativo client](./media/active-directory-conditional-access-technical-reference/41.png)

L'accesso condizionale di Azure AD supporta le piattaforme seguenti per i dispositivi:

- Android

- iOS

- Windows Phone

- Windows

- macOS (anteprima)



## <a name="client-apps-condition"></a>Condizione per le app client 

Quando si configura un criterio di accesso condizionale, è possibile [selezionare app client](active-directory-conditional-access-azure-portal.md#client-apps) per questa condizione. Impostare la condizione per le app client in modo da concedere o bloccare l'accesso quando viene eseguito un tentativo di accesso da questi tipi di app client:

- Browser
- App per dispositivi mobili e app desktop

![Controllare l'accesso in relazione alle app client](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Browser supportati 

Controllare l'accesso tramite browser usando l'opzione **Browser** nel criterio di accesso condizionale. L'accesso viene concesso solo quando il tentativo di accesso viene eseguito da un browser supportato. I tentativi di accesso eseguiti da browser non supportati verranno bloccati.

![Controllare l'accesso in relazione ai browser supportati](./media/active-directory-conditional-access-technical-reference/05.png)

Nel criterio di accesso condizionale sono supportati i browser seguenti: 


| OS                     | Browser                    | Supporto     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer, Microsoft Edge     | ![Controllo][1] |
| Windows 10             | Chrome                      | ![Controllo][1] |
| Windows 8/8.1        | Internet Explorer, Chrome   | ![Controllo][1] |
| Windows 7              | Internet Explorer, Chrome   | ![Controllo][1] |
| iOS                    | Safari, Intune Managed Browser                      | ![Controllo][1] |
| Android                | Chrome, Intune Managed Browser                      | ![Controllo][1] |
| Windows Phone          | Internet Explorer, Microsoft Edge     | ![Controllo][1] |
| Windows Server 2016    | Internet Explorer, Microsoft Edge     | ![Controllo][1] |
| Windows Server 2016    | Chrome                      | Presto disponibile |
| Windows Server 2012 R2 | Internet Explorer, Chrome   | ![Controllo][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome   | ![Controllo][1] |
| macOS                  | Safari                      | ![Controllo][1] |
| macOS                  | Chrome                      | Presto disponibile |

> [!NOTE]
> Per il supporto di Chrome è necessario usare Windows 10 Creators Update (versione 1703) o versioni successive.<br>
> È possibile installare [questa estensione](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applicazioni per dispositivi mobili e client desktop supportati

Per controllare l'accesso tramite client e app, usare l'opzione **App per dispositivi mobili e client desktop** nel criterio di accesso condizionale. L'accesso viene concesso solo quando il tentativo di accesso viene eseguito da un app per dispositivi mobili o un client desktop supportato. I tentativi di accesso eseguiti da app o client non supportati verranno bloccati.

![Controllare l'accesso in relazione alle app per dispositivi mobili o ai client desktop supportati](./media/active-directory-conditional-access-technical-reference/06.png)

Le app per dispositivi mobili e i client desktop seguenti supportano l'accesso condizionale per Office 365 e altre applicazioni di servizio connesse ad Azure AD:


| App client| Servizio di destinazione| Piattaforma |
| --- | --- | --- |
| App Azure Remote| Servizio app Azure Remote| Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X|
| Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| App Posta/Calendario/Contatti, Outlook 2016, Outlook 2013 (con autenticazione moderna)| Office 365 Exchange Online| Windows 10|
| MFA e criteri relativi alle applicazioni. I criteri basati su dispositivo non sono supportati.| Qualsiasi servizio app Mie app| Android e iOS|
| Microsoft Team Services consente di controllare tutti i servizi che supportano Microsoft Teams e tutte le app client: Windows Desktop, iOS, Android, WP e Web Client| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| App di Office 2016, Office 2013 (con autenticazione moderna), client sincronizzazione OneDrive (vedere le [note](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| App di Office 2016, app di Office universale, Office 2013 (con autenticazione moderna), client sincronizzazione OneDrive (vedere le [note](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), supporto per i gruppi di Office pianificato per il futuro, supporto per l'app SharePoint pianificato per il futuro| Office 365 SharePoint Online| Windows 10|
| Office 2016 per macOS (solo Word, Excel, PowerPoint e OneNote). OneDrive per il supporto Business pianificato in futuro| Office 365 SharePoint Online| Mac OS X|
| App Office per dispositivi mobili| Office 365 SharePoint Online| iOS, Android|
| App Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| Outlook 2016 (Office per macOS)| Office 365 Exchange Online| Mac OS X|
| Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| App Outlook Mobile| Office 365 Exchange Online| iOS|
| App PowerBI. L'app Power BI per Android non supporta attualmente l'accesso condizionale basato su dispositivo.| Servizio PowerBI| Windows 10, Windows 8.1, Windows 7 e iOS|
| Skype for Business Online| Office 365 Exchange Online| Android, iOS|
| App Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS e Android|



## <a name="approved-client-app-requirement"></a>Requisito per le app client approvate 

Per controllare le connessioni client, usare l'opzione **Richiedi app client approvata** nel criterio di accesso condizionale. L'accesso viene concesso solo quando il tentativo di connessione viene eseguito da un'app client approvata.

![Controllare l'accesso in relazione alle app client approvate](./media/active-directory-conditional-access-technical-reference/21.png)

Con il requisito per le applicazioni client approvate è possibile usare le app client seguenti:

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype for Business

- Microsoft Teams

- Microsoft Visio

- Microsoft Word


**Osservazioni**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.

- Il requisito **Richiedi app client approvata**:

    - Supporta solo iOS e Android come [condizione per le piattaforme del dispositivo](#device-platforms-condition).

    - Non supporta l'opzione **Browser** come [condizione per le app client](#supported-browsers).
    
    - Sostituisce l'opzione **App per dispositivi mobili e client desktop**, se selezionata, come [condizione per le app client](#supported-mobile-apps-and-desktop-clients).


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Se si è pronti per configurare criteri di accesso condizionale nell'ambiente in uso, vedere [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


