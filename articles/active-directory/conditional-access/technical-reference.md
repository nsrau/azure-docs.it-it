---
title: Informazioni di riferimento sulle impostazioni di accesso condizionale Azure Active Directory | Microsoft Docs
description: Ottenere una panoramica delle impostazioni supportate in un Azure Active Directory Criteri di accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 216316249197071bdd7a175dfc5339caeddee50a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086839"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Informazioni di riferimento sulle impostazioni di accesso condizionale Azure Active Directory

È possibile usare [l'accesso condizionale Azure Active Directory (Azure ad)](../active-directory-conditional-access-azure-portal.md) per controllare come gli utenti autorizzati possono accedere alle risorse.

Questo articolo fornisce informazioni di supporto per le opzioni di configurazione seguenti in un criterio di accesso condizionale:

- Assegnazioni delle applicazioni cloud
- Condizione per le piattaforme del dispositivo
- Condizione per le applicazioni client
- Requisito per le applicazioni client approvate

Se queste informazioni non soddisfano le aspettative, lasciare un commento alla fine di questo articolo.

## <a name="cloud-apps-assignments"></a>Assegnazioni di app cloud

Con i criteri di accesso condizionale è possibile controllare il modo in cui gli utenti accedono alle [app Cloud](conditions.md#cloud-apps-and-actions). Quando si configura un criterio di accesso condizionale, è necessario selezionare almeno un'app cloud. 

![Selezionare le app cloud per il criterio](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Applicazioni cloud Microsoft

È possibile assegnare un criterio di accesso condizionale alle app Cloud seguenti da Microsoft:

- Azure Analysis Services
- Azure DevOps
- Database SQL di Azure e data warehouse- [altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection- [altre informazioni](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Gestione Microsoft Azure- [altre informazioni](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Gestione delle sottoscrizioni Microsoft Azure
- Microsoft Cloud App Security
- Portale di controllo di accesso di Microsoft Commerce Tools
- Servizio di autenticazione di Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Registrazione di Microsoft Intune
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Approfondimento su Office
- Office Sway
- Outlook Groups
- Servizio Power BI
- Project Online
- Skype for Business Online
- Rete privata virtuale (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Altre applicazioni

Oltre alle app cloud Microsoft, è possibile assegnare un criterio di accesso condizionale ai tipi seguenti di app Cloud:

- Applicazioni connesse ad Azure AD
- Applicazione SaaS (Software as a Service) federata preintegrata
- Applicazioni che usano password Single Sign-On (SSO)
- Applicazioni line-of-business
- Applicazioni che usano il proxy di applicazione di Azure AD

## <a name="device-platform-condition"></a>Condizione per le piattaforme del dispositivo

In un criterio di accesso condizionale è possibile configurare la condizione della piattaforma del dispositivo per associare i criteri al sistema operativo di un client. Azure AD l'accesso condizionale supporta le piattaforme per dispositivi seguenti:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Collegare il criterio di accesso al sistema operativo client](./media/technical-reference/41.png)

Se si blocca l'autenticazione legacy usando la condizione di **altri client** , è anche possibile impostare la condizione della piattaforma del dispositivo.

## <a name="client-apps-condition"></a>Condizione per le app client

Nel criterio di accesso condizionale è possibile configurare la condizione delle [app client](conditions.md#client-apps) per associare i criteri all'app client che ha avviato un tentativo di accesso. Impostare la condizione per le app client in modo da concedere o bloccare l'accesso quando viene eseguito un tentativo di accesso da questi tipi di app client:

- Browser
- App per dispositivi mobili e app desktop

![Controllare l'accesso in relazione alle app client](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Browser supportati

Nel criterio di accesso condizionale è possibile selezionare **browser** come app client.

![Controllare l'accesso in relazione ai browser supportati](./media/technical-reference/05.png)

Questa impostazione funziona con tutti i browser. Tuttavia, per soddisfare un criterio dei dispositivi, ad esempio un requisito di un dispositivo conforme, sono supportati i sistemi operativi e browser seguenti:

| OS                     | Browser                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8/8.1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Perché viene visualizzata una richiesta di certificato nel browser

In Windows 7, iOS, Android e macOS Azure AD identifica il dispositivo usando un certificato client di cui viene effettuato il provisioning quando il dispositivo viene registrato con Azure AD.  Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente deve selezionare questo certificato prima di usare il browser.

#### <a name="chrome-support"></a>Supporto di Chrome

Per il supporto di Chrome in **Windows 10 Creators Update (versione 1703)** o versioni successive, installare l' [estensione account Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Questa estensione è obbligatoria quando i criteri di accesso condizionale richiedono dettagli specifici del dispositivo.

Per distribuire automaticamente questa estensione ai browser Chrome, creare la chiave del Registro di sistema seguente:

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; HTTPS\://clients2.Google.com/service/update2/crx |

Per il supporto di Chrome in **Windows 8.1 e 7**, creare la chiave del Registro di sistema seguente:

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Questi browser supportano l'autenticazione del dispositivo, consentendo al dispositivo di essere identificato e convalidato rispetto a un criterio. Il controllo del dispositivo ha esito negativo se il browser è in esecuzione in modalità privata.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applicazioni per dispositivi mobili e client desktop supportati

Nel criterio di accesso condizionale è possibile selezionare app per **dispositivi mobili e client desktop** come app client.

![Controllare l'accesso in relazione alle app per dispositivi mobili o ai client desktop supportati](./media/technical-reference/06.png)

Questa impostazione interessa i tentativi di accesso eseguiti dalle app per dispositivi mobili e client desktop seguenti:

| App client | Servizio di destinazione | Piattaforma |
| --- | --- | --- |
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
| App Power BI | servizio Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype for Business Online | Office 365 Exchange Online| Android, iOS |
| App Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

## <a name="support-for-legacy-authentication"></a>Supporto per l'autenticazione legacy

Selezionando **Altri client** è possibile specificare una condizione che influisce sulle app che usano l'autenticazione di base con protocolli di posta elettronica come IMAP, MAPI, POP, SMTP e le app Office meno recenti che non usano l'autenticazione moderna.  

![Altri client](./media/technical-reference/11.png)

Per altre informazioni, vedere la sezione relativa alle [app client](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Requisito per le app client approvate

Nei criteri di accesso condizionale è possibile richiedere che venga eseguito un tentativo di accesso alle app Cloud selezionate da un'app client approvata. 

![Controllare l'accesso in relazione alle app client approvate](./media/technical-reference/21.png)

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
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

## <a name="app-protection-policy-requirement"></a>Requisito dei criteri di protezione delle app 

Nei criteri di accesso condizionale è possibile richiedere che un criterio di protezione delle app sia presente nell'app client prima che l'accesso sia disponibile per le app Cloud selezionate. 

![Controllare l'accesso con i criteri di protezione delle app](./media/technical-reference/22.png)

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Cortana
- Microsoft Edge
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Osservazioni:**

- Le app per i criteri di protezione delle app supportano la funzionalità di gestione delle applicazioni mobili di Intune con protezione dei criteri.
- Richiede i requisiti dei **criteri di protezione delle app** :
    - Supporta solo iOS e Android come [condizione per le piattaforme del dispositivo](#device-platform-condition).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'accesso condizionale, vedere [che cos'è l'accesso condizionale in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Se si è pronti per configurare i criteri di accesso condizionale nell'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
