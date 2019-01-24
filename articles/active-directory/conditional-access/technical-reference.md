---
title: Informazioni di riferimento sulle impostazioni di accesso condizionale di Azure Active Directory | Microsoft Docs
description: Panoramica delle impostazioni supportate nei criteri di accesso condizionale di Azure Active Directory.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/13/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 0971b5abee872d9a7010f0ce931f09c47808eb80
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452140"
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

- Azure Information Protection: [altre informazioni](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (include OneDrive for Business e Project Online)

- Microsoft Power BI 

- Azure DevOps

- Microsoft Teams


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

-  Windows

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


| OS                     | Browser                            | Supporto     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     | ![Controllo][1] |
| Windows 8/8.1        | Internet Explorer, Chrome           | ![Controllo][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Controllo][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Controllo][1] |
| Android                | Chrome, Intune Managed Browser      | ![Controllo][1] |
| Windows Phone          | Internet Explorer, Microsoft Edge             | ![Controllo][1] |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             | ![Controllo][1] |
| Windows Server 2016    | Chrome                              | Presto disponibile |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Controllo][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Controllo][1] |
| macOS                  | Chrome, Safari                      | ![Controllo][1] |



#### <a name="chrome-support"></a>Supporto di Chrome

Per il supporto di Chrome in **Windows 10 Creators Update (versione 1703)** o versioni successive, installare [questa estensione](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Per distribuire automaticamente questa estensione ai browser Chrome, creare la chiave del Registro di sistema seguente:

|    |    |
|--- | ---|
|path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|NOME | 1 |
|type | REG_SZ (String) |
|Dati | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

Per il supporto di Chrome in **Windows 8.1 e 7**, creare la chiave del Registro di sistema seguente:

|    |    |
|--- | ---|
|path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|NOME | 1 |
|type | REG_SZ (String) |
|Dati | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Questi browser supportano l'autenticazione del dispositivo, consentendo al dispositivo di essere identificato e convalidato rispetto a un criterio. Il controllo del dispositivo ha esito negativo se il browser è in esecuzione in modalità privata. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Applicazioni per dispositivi mobili e client desktop supportati

Nel criterio di accesso condizionale è possibile selezionare **App per dispositivi mobili e client desktop** come app client.


![Controllare l'accesso in relazione alle app per dispositivi mobili o ai client desktop supportati](./media/technical-reference/06.png)


Questa impostazione interessa i tentativi di accesso eseguiti dalle app per dispositivi mobili e client desktop seguenti: 


|App client|Servizio di destinazione|Piattaforma|
|---|---|---|
|App Azure Remote|Servizio app Azure Remote|Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X|
|Dynamics CRM|Dynamics CRM|Windows 10, Windows 8.1, iOS e Android|
|App Mail/Calendar/People, Outlook 2016, Outlook 2013 |Office 365 Exchange Online|Windows 10|
|MFA e criteri relativi alle applicazioni. I criteri basati su dispositivo non sono supportati. |Qualsiasi servizio app Mie app|Android e iOS|
|Microsoft Team Services consente di controllare tutti i servizi che supportano Microsoft Teams e tutte le app client: Windows Desktop, iOS, Android, WP e Web Client|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
|App Office 2016, Office 2013, client di sincronizzazione OneDrive (vedere le [note](https://support.office.com/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|App di Office 2016, app di Office universale, Office 2013, client sincronizzazione OneDrive (vedere le [note](https://support.office.com/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), supporto per i gruppi di Office pianificato per il futuro, supporto per l'app SharePoint pianificato per il futuro|Office 365 SharePoint Online|Windows 10|
|Office 2016 per macOS (solo Word, Excel, PowerPoint e OneNote). OneDrive per il supporto Business pianificato in futuro|Office 365 SharePoint Online|Mac OS X|
|App Office per dispositivi mobili|Office 365 SharePoint Online|Android, iOS|
|App Office Yammer|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office per macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013, Skype for Business|Office 365 Exchange Online|Windows 8.1, Windows 7|
|App Outlook Mobile|Office 365 Exchange Online|Android, iOS|
|App PowerBI|Servizio PowerBI|Windows 10, Windows 8.1, Windows 7, Android e iOS|
|Skype for Business Online|Office 365 Exchange Online|Android, iOS |
|App Azure DevOps|Azure DevOps|Windows 10, Windows 8.1, Windows 7, iOS e Android|


## <a name="support-for-legacy-authentication"></a>Supporto per l'autenticazione legacy

Selezionando **Altri client** è possibile specificare una condizione che influisce sulle app che usano l'autenticazione di base con protocolli di posta elettronica come IMAP, MAPI, POP, SMTP e le app Office meno recenti che non usano l'autenticazione moderna.  

![Altri client](./media/technical-reference/11.png)

Per altre informazioni, vedere la sezione relativa alle [app client](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Requisito per le app client approvate 

Nel criterio di accesso condizionale è possibile impostare l'esecuzione dei tentativi di accesso alle app cloud selezionate da un'app client approvata. 

![Controllare l'accesso in relazione alle app client approvate](./media/technical-reference/21.png)

Questa impostazione è valida solo per le app client seguenti:


- Microsoft Intune Managed Browser
- Microsoft Power BI
- Microsoft Invoicing
- Microsoft Launcher
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word
- Microsoft To-Do
- Microsoft Stream
- Microsoft Edge



**Osservazioni:**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.

- Il requisito **Richiedi app client approvata**:

    - Supporta solo iOS e Android come [condizione per le piattaforme del dispositivo](#device-platforms-condition).


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
- Se si è pronti per configurare criteri di accesso condizionale nell'ambiente in uso, vedere [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).



<!--Image references-->
[1]: ./media/technical-reference/01.png


