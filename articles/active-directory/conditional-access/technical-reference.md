---
title: Informazioni di riferimento sulle impostazioni di accesso condizionale di Azure Active Directory | Microsoft Docs
description: Panoramica delle impostazioni supportate nei criteri di accesso condizionale di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e87a4c7ebafd8ddcfa54c87b189316b0ce98b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301267"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Informazioni di riferimento sulle impostazioni di accesso condizionale di Azure Active Directory

È possibile usare l'[accesso condizionale di Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) per controllare il modo in cui gli utenti autorizzati possono accedere alle risorse.

Questo articolo contiene informazioni di supporto per le opzioni di configurazione seguenti di un criterio di accesso condizionale:

- Assegnazioni delle applicazioni cloud
- Condizione per le piattaforme del dispositivo
- Condizione per le applicazioni client
- Requisito per le applicazioni client approvate

Se queste informazioni non soddisfano le aspettative, lasciare un commento alla fine di questo articolo.

## <a name="cloud-apps-assignments"></a>Assegnazioni di app cloud

Grazie ai criteri di accesso condizionale è possibile controllare la modalità di accesso degli utenti alle [app cloud](conditions.md#cloud-apps). Quando si configura un criterio di accesso condizionale, è necessario selezionare almeno un'app cloud. 

![Selezionare le app cloud per il criterio](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Applicazioni cloud Microsoft

È possibile assegnare un criterio di accesso condizionale alle app cloud seguenti di Microsoft:

- Azure Analysis Services
- Azure DevOps
- Database SQL di Azure e Data Warehouse - [altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analitica
- Microsoft Azure Information Protection - [altre informazioni](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Gestione di Microsoft Azure - [altre informazioni](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- RemoteApp di Microsoft Azure
- Gestione delle sottoscrizioni Microsoft Azure
- Microsoft Cloud App Security
- Portale di controllo di accesso di strumenti di Microsoft e-Commerce
- Servizio di autenticazione di Microsoft Commerce strumenti
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Registrazione di Microsoft Intune
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Microsoft ricerca in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Project Online
- Skype for Business Online
- Virtual Private Network (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Altre applicazioni

Oltre che alle app cloud Microsoft, è possibile assegnare un criterio di accesso condizionale ai tipi seguenti di app cloud:

- Applicazioni connesse ad Azure AD
- Applicazione SaaS (Software as a Service) federata preintegrata
- Applicazioni che usano password Single Sign-On (SSO)
- Applicazioni line-of-business
- Applicazioni che usano il proxy di applicazione di Azure AD

## <a name="device-platform-condition"></a>Condizione per le piattaforme del dispositivo

In un criterio di accesso condizionale è possibile configurare la condizione per le piattaforme del dispositivo in modo da collegare il criterio al sistema operativo di un client. L'accesso condizionale di Azure AD supporta le piattaforme seguenti per i dispositivi:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Collegare il criterio di accesso al sistema operativo client](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Condizione per le app client

Nel criterio di accesso condizionale è possibile configurare le condizioni delle [app client](conditions.md#client-apps) per associare il criterio all'app client che ha avviato un tentativo di accesso. Impostare la condizione per le app client in modo da concedere o bloccare l'accesso quando viene eseguito un tentativo di accesso da questi tipi di app client:

- Browser
- App per dispositivi mobili e app desktop

![Controllare l'accesso in relazione alle app client](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Browser supportati

Nel criterio di accesso condizionale è possibile selezionare **Browser** come app client.

![Controllare l'accesso in relazione ai browser supportati](./media/technical-reference/05.png)

Questa impostazione funziona con tutti i browser. Tuttavia, per soddisfare un criterio dei dispositivi, ad esempio un requisito di un dispositivo conforme, sono supportati i sistemi operativi e browser seguenti:

| OS                     | Browser                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8/8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Microsoft Edge, Intune Managed Browser |
| Android                | Chrome, Edge, Microsoft Intune Managed Browser |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Perché viene visualizzato un certificato richiesto nel browser

In Windows 7, iOS, Android e macOS Azure AD identifica il dispositivo usando un certificato client che viene eseguito il provisioning quando il dispositivo è registrato con Azure AD.  Quando un utente accede la prima volta tramite il browser all'utente viene richiesto di selezionare il certificato. L'utente deve selezionare questo certificato prima di usare il browser.

#### <a name="chrome-support"></a>Supporto di Chrome

Per il supporto di Chrome in **Windows 10 Creators Update (versione 1703)** o versioni successive, installare [questa estensione](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Per distribuire automaticamente questa estensione ai browser Chrome, creare la chiave del Registro di sistema seguente:

|    |    |
| --- | --- |
| path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (String) |
| Dati | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Per il supporto di Chrome in **Windows 8.1 e 7**, creare la chiave del Registro di sistema seguente:

|    |    |
| --- | --- |
| path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (String) |
| Dati | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Questi browser supportano l'autenticazione del dispositivo, consentendo al dispositivo di essere identificato e convalidato rispetto a un criterio. Il controllo del dispositivo ha esito negativo se il browser è in esecuzione in modalità privata.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applicazioni per dispositivi mobili e client desktop supportati

Nel criterio di accesso condizionale è possibile selezionare **App per dispositivi mobili e client desktop** come app client.

![Controllare l'accesso in relazione alle app per dispositivi mobili o ai client desktop supportati](./media/technical-reference/06.png)

Questa impostazione interessa i tentativi di accesso eseguiti dalle app per dispositivi mobili e client desktop seguenti:

| App client | Servizio di destinazione | Piattaforma |
| --- | --- | --- |
| App Azure Remote | Servizio app Azure Remote | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| App Posta/Calendario/Contatti, Outlook 2016, Outlook 2013 (con autenticazione moderna)| Office 365 Exchange Online | Windows 10 |
| MFA e criteri relativi alle applicazioni. I criteri basati su dispositivo non sono supportati.| Qualsiasi servizio app Mie app| Android e iOS |
| Microsoft Team Services consente di controllare tutti i servizi che supportano Microsoft Teams e tutte le app client: Windows Desktop, iOS, Android, WP e Web Client | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| App di Office 2016, Office 2013 (con autenticazione moderna), client sincronizzazione OneDrive (vedere le [note](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| App di Office 2016, app di Office universale, Office 2013 (con autenticazione moderna), client sincronizzazione OneDrive (vedere le [note](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), supporto per i gruppi di Office pianificato per il futuro, supporto per l'app SharePoint pianificato per il futuro | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (solo Word, Excel, PowerPoint e OneNote). OneDrive per il supporto Business pianificato in futuro| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| App Office per dispositivi mobili | Office 365 SharePoint Online | Android, iOS |
| App Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office per macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| App Outlook Mobile | Office 365 Exchange Online | Android, iOS |
| App Power BI | Servizio Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype for Business Online | Office 365 Exchange Online| Android, iOS |
| App Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

## <a name="support-for-legacy-authentication"></a>Supporto per l'autenticazione legacy

Selezionando **Altri client** è possibile specificare una condizione che influisce sulle app che usano l'autenticazione di base con protocolli di posta elettronica come IMAP, MAPI, POP, SMTP e le app Office meno recenti che non usano l'autenticazione moderna.  

![Altri client](./media/technical-reference/11.png)

Per altre informazioni, vedere la sezione relativa alle [app client](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Requisito per le app client approvate

Nel criterio di accesso condizionale è possibile impostare l'esecuzione dei tentativi di accesso alle app cloud selezionate da un'app client approvata. 

![Controllare l'accesso in relazione alle app client approvate](./media/technical-reference/21.png)

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Azure Information Protection
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Osservazioni:**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.
- Il requisito **Richiedi app client approvata**:
   - Supporta solo iOS e Android come [condizione per le piattaforme del dispositivo](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Requisito dei criteri di protezione App 

Nel criterio di accesso condizionale, è possibile richiedere che un criterio di protezione delle app siano presenti nell'app client prima di accesso è disponibile per le app cloud selezionate. 

![Controllare l'accesso con criteri di protezione delle app](./media/technical-reference/22.png)

Questa impostazione è valida solo per le app client seguenti:

- Microsoft OneDrive
- Microsoft Outlook

**Osservazioni:**

- Le App per criteri di protezione delle app supportano la funzionalità di gestione di applicazioni mobili di Intune con criteri di protezione.
- Il **richiedono criteri di protezione delle app** requisiti:
    - Supporta solo iOS e Android come [condizione per le piattaforme del dispositivo](#device-platform-condition).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
- Se si è pronti per configurare criteri di accesso condizionale nell'ambiente in uso, vedere [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
