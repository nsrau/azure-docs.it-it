---
title: Condizioni nei criteri di accesso condizionale - Azure Active DirectoryConditions in Conditional Access policy - Azure Active Directory
description: Quali sono le condizioni in un criterio di accesso condizionale di Azure ADWhat are conditions in an Azure AD Conditional Access policy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295341"
---
# <a name="conditional-access-conditions"></a>Accesso condizionale: condizioniConditional Access: Conditions

All'interno di un criterio di accesso condizionale, un amministratore può utilizzare i segnali provenienti da condizioni come rischio, piattaforma del dispositivo o posizione per migliorare le decisioni relative ai criteri. 

![Definire un criterio di accesso condizionale e specificare le condizioniDefine a Conditional Access policy and specify conditions](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

È possibile combinare più condizioni per creare criteri di accesso condizionale specifici e con granularità fine.

Ad esempio, quando si accede a un'applicazione sensibile, un amministratore può considerare le informazioni sui rischi di accesso da Identity Protection e posizione nella decisione di accesso oltre ad altri controlli come l'autenticazione a più fattori.

## <a name="sign-in-risk"></a>Rischio di accesso

Per i clienti con accesso a [Identity Protection](../identity-protection/overview-identity-protection.md), il rischio di accesso può essere valutato come parte di un criterio di accesso condizionale. Il rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. Ulteriori informazioni sul rischio di accesso sono disponibili negli articoli [What is risk](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) e How [To: Configure and enable risk policies](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Piattaforme del dispositivo

La piattaforma del dispositivo è caratterizzata dal sistema operativo in esecuzione su un dispositivo. Azure AD identifica la piattaforma usando le informazioni fornite dal dispositivo, ad esempio le stringhe agente utente. Poiché le stringhe agente utente possono essere modificate, queste informazioni non vengono verificate. Piattaforma del dispositivo deve essere utilizzata in accordo con i criteri di conformità dei dispositivi di Microsoft Intune o come parte di un'istruzione di blocco. L'impostazione predefinita è che viene applicata a tutte le piattaforme per dispositivi.

L'accesso condizionale di Azure AD supporta le piattaforme di dispositivi seguenti:Azure AD Conditional Access supports the following device platforms:

- Android
- iOS
- Windows Phone
- WINDOWS
- macOS

Se si blocca l'autenticazione legacy usando la condizione **Altri client,** è anche possibile impostare la condizione della piattaforma del dispositivo.

## <a name="locations"></a>Percorsi

Quando si configura la posizione come condizione, le organizzazioni possono scegliere di includere o escludere le ubicazioni. Queste posizioni denominate possono includere le informazioni di rete IPv4 pubbliche, il paese o l'area geografica o anche aree sconosciute che non eseguono il mapping a paesi o aree geografiche specifici. Solo gli intervalli IP possono essere contrassegnati come percorso attendibile.

Quando si include **una posizione**, questa opzione include qualsiasi indirizzo IP su Internet non solo configurato posizioni denominate. Quando si seleziona **una posizione,** gli amministratori possono scegliere di escludere **tutti i** percorsi attendibili o **selezionati.**

Ad esempio, alcune organizzazioni possono scegliere di non richiedere l'autenticazione a più fattori quando gli utenti sono connessi alla rete in un percorso attendibile, ad esempio la sede centrale fisica. Gli amministratori possono creare un criterio che includa qualsiasi posizione, ma escluda le posizioni selezionate per le reti della sede centrale.

Ulteriori informazioni sui percorsi sono disponibili nell'articolo Qual è la condizione di [posizione in Azure Active Directory Conditional Access](location-condition.md).

## <a name="client-apps-preview"></a>App client (anteprima)Client apps (preview)

I criteri di accesso condizionale per impostazione predefinita si applicano alle applicazioni e alle applicazioni basate su browser che utilizzano protocolli di autenticazione moderni. Oltre a queste applicazioni, gli amministratori possono scegliere di includere i client Exchange ActiveSync e altri client che utilizzano protocolli legacy.

- Browser
   - Queste includono applicazioni basate sul Web che utilizzano protocolli come SAML, WS-Federation, OpenID Connect o servizi registrati come client riservati OAuth.
- App per dispositivi mobili e client desktop
   - Client di autenticazione moderni
      - Questa opzione include applicazioni come le applicazioni desktop e telefoniche di Office.
   - Client Exchange ActiveSync
      - Per impostazione predefinita, questo include tutti gli utilizzi del protocollo Exchange ActiveSync (EAS). La scelta di **Applica criteri solo alle piattaforme supportate** limiterà alle piattaforme supportate come iOS, Android e Windows.Choosing Apply policy only to supported platforms will limit to supported platforms like iOS, Android, and Windows.
      - Quando i criteri bloccano l'utilizzo di Exchange ActiveSync, l'utente interessato riceverà un singolo messaggio di posta elettronica di quarantena. Questo messaggio di posta elettronica con fornire informazioni sul motivo per cui sono bloccati e includere istruzioni di correzione, se possibile.
   - Altri client
      - Questa opzione include i client che utilizzano protocolli di autenticazione di base/legacy che non supportano l'autenticazione moderna.
         - SMTP autenticato - Utilizzato dai client POP e IMAP per inviare messaggi di posta elettronica.
         - Individuazione automatica - Utilizzato dai client Outlook e EAS per trovare e connettersi alle cassette postali in Exchange Online.
         - Exchange Online PowerShell - Utilizzato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per PowerShell di Exchange Online, è necessario utilizzare il modulo PowerShell di Exchange Online per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell utilizzando l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Servizi Web Exchange (EWS): interfaccia di programmazione utilizzata da Outlook, Outlook per Mac e app di terze parti.
         - IMAP4 - Utilizzato dai client di posta elettronica IMAP.
         - MAPI su HTTP (MAPI/HTTP) - Utilizzato da Outlook 2010 e versioni successive.
         - Rubrica offline -OAB- Una copia delle raccolte di elenchi di indirizzi scaricate e utilizzate da Outlook.
         - Outlook via Internet (RPC su HTTP) - Utilizzato da Outlook 2016 e versioni precedenti.
         - Servizio Outlook - Utilizzato dall'app Posta e Calendario per Windows 10.
         - POP3 - Utilizzato dai client di posta elettronica POP.
         - Reporting Web Services: utilizzato per recuperare i dati dei report in Exchange Online.

Queste condizioni vengono comunemente utilizzate quando si richiede un dispositivo gestito, si blocca l'autenticazione legacy e si bloccano le applicazioni Web, ma si consentono le app per dispositivi mobili o desktop.

### <a name="supported-browsers"></a>Browser supportati

Questa impostazione funziona con tutti i browser. Tuttavia, per soddisfare un criterio dei dispositivi, ad esempio un requisito di un dispositivo conforme, sono supportati i sistemi operativi e browser seguenti:

| OS | Browser |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Perché viene visualizzata una richiesta di certificato nel browser

In Windows 7, iOS, Android e macOS Azure AD identifica il dispositivo usando un certificato client di cui viene eseguito il provisioning quando il dispositivo viene registrato con Azure AD.  Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente deve selezionare questo certificato prima di utilizzare il browser.

#### <a name="chrome-support"></a>Supporto di Chrome

Per il supporto di Chrome in **Windows 10 Creators Update (versione 1703)** o versione successiva, installa l'estensione [Account di Windows 10.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) Questa estensione è necessaria quando un criterio di accesso condizionale richiede dettagli specifici del dispositivo.

Per distribuire automaticamente questa estensione ai browser Chrome, creare la chiave del Registro di sistema seguente:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Nome | 1 |
| Type | REG_SZ (String) |
| Dati | ppnbnpeolgkicgegkbjmhlideopijiji;https\://clients2.google.com/service/update2/crx |

Per il supporto di Chrome in **Windows 8.1 e 7**, creare la chiave del Registro di sistema seguente:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Nome | 1 |
| Type | REG_SZ (String) |
| Dati | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Questi browser supportano l'autenticazione del dispositivo, consentendo al dispositivo di essere identificato e convalidato rispetto a un criterio. Il controllo del dispositivo ha esito negativo se il browser è in esecuzione in modalità privata.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applicazioni per dispositivi mobili e client desktop supportati

Le organizzazioni possono selezionare **Le app per dispositivi mobili e i client desktop** come app client.

Questa impostazione interessa i tentativi di accesso eseguiti dalle app per dispositivi mobili e client desktop seguenti:

| App client | Servizio di destinazione | Piattaforma |
| --- | --- | --- |
| Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| App Posta/Calendario/Contatti, Outlook 2016, Outlook 2013 (con autenticazione moderna)| Office 365 Exchange Online | Windows 10 |
| MFA e criteri relativi alle applicazioni. I criteri basati su dispositivo non sono supportati.| Qualsiasi servizio app Mie app | Android e iOS |
| Microsoft Team Services consente di controllare tutti i servizi che supportano Microsoft Teams e tutte le app client: Windows Desktop, iOS, Android, WP e Web Client | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| App di Office 2016, Office 2013 (con autenticazione moderna), [client di sincronizzazione di OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| App di Office 2016, app di Office universale, Office 2013 (con autenticazione moderna), [client di sincronizzazione DiOneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (solo Word, Excel, PowerPoint e OneNote). | Office 365 SharePoint Online | macOS |
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

### <a name="exchange-activesync-clients"></a>Client Exchange ActiveSync

- Le organizzazioni possono selezionare i client Exchange ActiveSync solo quando assegnano criteri a utenti o gruppi. Se si seleziona **Tutti gli utenti**, Tutti gli utenti guest ed **esterni**o I **ruoli Directory** verranno bloccati.
- Quando si crea un criterio assegnato ai client Exchange ActiveSync, **Office 365 Exchange Online** deve essere l'unica applicazione cloud assegnata al criterio. 
- Le organizzazioni possono limitare l'ambito di questo criterio a piattaforme specifiche utilizzando la condizione **Piattaforme dispositivo.**

Se il controllo di accesso assegnato al criterio utilizza **Richiedi app client approvata**, l'utente viene indirizzato a installare e utilizzare il client Outlook mobile. Nel caso in cui sia necessaria **l'autenticazione a più fattori,** gli utenti interessati vengono bloccati, perché l'autenticazione di base non supporta l'autenticazione a più fattori.

Per altre informazioni, vedere gli articoli seguenti:

- [Bloccare l'autenticazione legacy con l'accesso condizionaleBlock legacy authentication with Conditional Access](block-legacy-authentication.md)
- [Richiedere app client approvate con accesso condizionaleRequiring approved client apps with Conditional Access](app-based-conditional-access.md)

### <a name="other-clients"></a>Altri client

Selezionando **Altri client** è possibile specificare una condizione che influisce sulle app che usano l'autenticazione di base con protocolli di posta elettronica come IMAP, MAPI, POP, SMTP e le app Office meno recenti che non usano l'autenticazione moderna.

## <a name="device-state-preview"></a>Stato del dispositivo (anteprima)Device state (preview)

La condizione di stato del dispositivo può essere usata per escludere i dispositivi aggiunti ad Azure AD ibrido e/o i dispositivi contrassegnati come conformi ai criteri di conformità di Microsoft Intune dai criteri di accesso condizionale di un'organizzazione.

Ad esempio, *tutti gli utenti* che accedono all'app cloud di gestione di Microsoft *Azure,* incluso **Tutto lo stato del dispositivo,** escluso **dispositivo ibrido di Azure AD unito** e **Dispositivo contrassegnato come conforme** e per i controlli di *accesso*, **Blocco**. 
   - Questo esempio consente di creare criteri che consentono solo l'accesso a Gestione di Microsoft Azure da dispositivi aggiunti ad Azure AD e/o a dispositivi contrassegnati come conformi.

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: sovvenzione](concept-conditional-access-grant.md)

- [Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)
