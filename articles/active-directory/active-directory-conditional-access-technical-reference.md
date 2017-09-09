---
title: Documentazione tecnica sull'accesso condizionale in Azure Active Directory | Documentazione Microsoft
description: Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.
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
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: f96189735512090f993f61c0d64a249f650ea2a2
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Documentazione tecnica sull'accesso condizionale di Azure Active Directory

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), è possibile ottimizzare come gli utenti autorizzati possono accedere alle risorse.  
Questo argomento contiene informazioni sul supporto per gli elementi seguenti di un criterio di accesso condizionale: 

- Assegnazioni di app cloud

- Condizioni per le app client



## <a name="cloud-apps-assignments"></a>Assegnazioni di app cloud

Quando si configura un criterio di accesso condizionale, è necessario [selezionare le app cloud a cui il criterio si applica](active-directory-conditional-access-azure-portal.md#who). 

![Controllo](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>App cloud Microsoft

È possibile assegnare un criterio di accesso condizionale alle app cloud seguenti di Microsoft:

- App remote di Azure

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (include OneDrive for Business)

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft Teams


### <a name="other-apps"></a>Altre app 

Oltre che alle app cloud Microsoft, è possibile assegnare un criterio di accesso condizionale ai tipi seguenti di app cloud:

- Applicazioni connesse ad Azure Active Directory (Azure AD)

- Applicazioni software come un servizio (SaaS) federate preintegrate

- Applicazioni che usano password Single Sign-On (SSO)

- Applicazioni line-of-business

- Applicazioni che usano proxy di applicazione Azure AD. 


## <a name="client-apps-conditions"></a>Condizioni per le app client 

Quando si configura un criterio di accesso condizionale, è possibile impostare una [condizione per le app client](active-directory-conditional-access-azure-portal.md#client-apps). La condizione per le app client consente di concedere o bloccare l'accesso quando viene eseguito un tentativo di accesso da questi tipi di app client:

- Browser
- App per dispositivi mobili e app desktop

![Controllo](./media/active-directory-conditional-access-technical-reference/03.png)


### <a name="supported-browsers"></a>Browser supportati 

Se si seleziona *Browser* nel criterio di accesso condizionale per concedere l'accesso alle risorse, l'accesso viene concesso solo quando il tentativo di accesso viene effettuato con un browser supportato. Quando viene effettuato un tentativo di accesso con un browser non supportato, il tentativo viene bloccato.

![Browser supportati](./media/active-directory-conditional-access-technical-reference/05.png)

Nel criterio di accesso condizionale sono supportati i browser seguenti: 


| OS                     | Browser                 | Supporto     |
| :--                    | :--                      | :-:         |
| Windows 10                 | Internet Explorer, Edge                 | ![Controllo][1] |
| Windows 10                 | Chrome                   | Preview     |
| Windows 8/8.1            | IE, Chrome               | ![Controllo][1] |
| Windows 7                  | IE, Chrome               | ![Controllo][1] |
| iOS                    | Safari                   | ![Controllo][1] |
| Android                | Chrome                   | ![Controllo][1] |
| Windows Phone          | Internet Explorer, Edge                 | ![Controllo][1] |
| Windows Server 2016    | Internet Explorer, Edge                 | ![Controllo][1] |
| Windows Server 2016    | Chrome                   | Presto disponibile |
| Windows Server 2012 R2 | IE, Chrome               | ![Controllo][1] |
| Windows Server 2008 R2 | IE, Chrome               | ![Controllo][1] |
| Mac OS                 | Safari                   | ![Controllo][1] |
| Mac OS                 | Chrome                   | Presto disponibile |

> [!NOTE]
> Per il supporto di Chrome, è necessario utilizzare Windows 10 Creators Update e installare l'estensione disponibile [qui](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).


### <a name="supported-mobile-apps-and-desktop-clients"></a>App per dispositivi mobili e client desktop supportati

Se si seleziona **App per dispositivi mobili e client desktop** nel criterio di accesso condizionale per concedere l'accesso alle risorse, l'accesso viene concesso solo quando il tentativo di accesso viene effettuato con app per dispositivi mobili o client desktop supportati. Quando viene effettuato un tentativo di accesso con un'app per dispositivi mobili o un client desktop non supportato, il tentativo viene bloccato.

![Controllo](./media/active-directory-conditional-access-technical-reference/06.png)

Le app per dispositivi mobili e i client desktop seguenti supportano l'accesso condizionale per Office 365 e altre applicazioni di servizio connesse ad Azure AD:


| App client| Servizio di destinazione| Piattaforma |
| :-- | --- | --- |
| MFA e criteri relativi alle applicazioni. I criteri basati su dispositivo non sono supportati.| Qualsiasi servizio app Mie app| Android e iOS|
| App Azure Remote| Servizio app Azure Remote| Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X|
| Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| Microsoft Team Services consente di controllare tutti i servizi che supportano i team Microsoft e tutte le app client: Windows Desktop, MAC OS X, iOS, Android, WP e web client| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS/Android e MAC OSX|
| App Mail/Calendar/People, Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| App Outlook Mobile| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office per Mac OS)| Office 365 Exchange Online| Mac OS X|
| App di Office 2016, app di Office universale, Office 2013 (con autenticazione moderna), client sincronizzazione OneDrive (vedere le [note](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), supporto per i gruppi di Office pianificato per il futuro, supporto per l'app SharePoint pianificato per il futuro| Office 365 SharePoint Online| Windows 10|
| App di Office 2016, Office 2013 (con autenticazione moderna), client sincronizzazione OneDrive (vedere le [note](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| App Office per dispositivi mobili| Office 365 SharePoint Online| iOS, Android|
| Office 2016 per macOS (Word, Excel, PowerPoint, solo OneNote). OneDrive per il supporto Business pianificato in futuro| Office 365 SharePoint Online| Mac OS X|
| App Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| App PowerBI. L'app Power BI per Android non supporta attualmente l'accesso condizionale basato su dispositivo.| Servizio PowerBI| Windows 10, Windows 8.1, Windows 7 e iOS|
| App Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS e Android|














## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- Se si è pronti per configurare i criteri di accesso condizionale nell'ambiente in uso, vedere [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



