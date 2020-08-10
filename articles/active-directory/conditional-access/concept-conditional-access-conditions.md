---
title: Condizioni nei criteri di accesso condizionale-Azure Active Directory
description: Cosa sono le condizioni in un Azure AD criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b887c91a289730c3d92efe753a2df162f36a047
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032127"
---
# <a name="conditional-access-conditions"></a>Accesso condizionale: condizioni

All'interno di un criterio di accesso condizionale, un amministratore può usare i segnali provenienti da condizioni come il rischio, la piattaforma del dispositivo o la località per migliorare le decisioni relative ai criteri. 

[![Definire un criterio di accesso condizionale e specificare le condizioni](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

È possibile combinare più condizioni per creare criteri specifici per l'accesso condizionale e con granularità fine.

Ad esempio, quando si accede a un'applicazione sensibile, un amministratore può fattorizzare le informazioni di rischio di accesso da Identity Protection e location nella decisione di accesso, oltre ad altri controlli come l'autenticazione a più fattori.

## <a name="sign-in-risk"></a>Rischio di accesso

Per i clienti che hanno accesso a [Identity Protection](../identity-protection/overview-identity-protection.md), il rischio di accesso può essere valutato come parte di un criterio di accesso condizionale. Il rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. Per altre informazioni sul rischio di accesso, vedere gli articoli relativi a [rischi](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) e [procedura: configurare e abilitare i criteri di rischio](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>Rischio utente 

Per i clienti che hanno accesso a [Identity Protection](../identity-protection/overview-identity-protection.md), il rischio utente può essere valutato come parte di un criterio di accesso condizionale. Il rischio utente rappresenta la probabilità che venga compromessa una determinata identità o un determinato account. Altre informazioni sui rischi per gli utenti sono disponibili negli articoli, [che cosa sono i rischi](../identity-protection/concept-identity-protection-risks.md#user-risk) e [procedura: configurare e abilitare i criteri di rischio](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Piattaforme per dispositivi

La piattaforma del dispositivo è caratterizzata dal sistema operativo in esecuzione su un dispositivo. Azure AD identifica la piattaforma usando le informazioni fornite dal dispositivo, ad esempio le stringhe agente utente. Poiché le stringhe agente utente possono essere modificate, queste informazioni non vengono verificate. È consigliabile usare la piattaforma del dispositivo insieme ai criteri di conformità Microsoft Intune dispositivo o come parte di un'istruzione Block. Per impostazione predefinita, si applica a tutte le piattaforme del dispositivo.

Azure AD l'accesso condizionale supporta le piattaforme per dispositivi seguenti:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Se si blocca l'autenticazione legacy usando la condizione di **altri client** , è anche possibile impostare la condizione della piattaforma del dispositivo.

## <a name="locations"></a>Percorsi

Quando si configura il percorso come condizione, le organizzazioni possono scegliere di includere o escludere percorsi. Queste località denominate possono includere le informazioni sulla rete IPv4 pubbliche, il paese o l'area geografica o anche aree sconosciute che non sono mappate a specifici paesi o aree geografiche. Solo gli intervalli IP possono essere contrassegnati come un percorso attendibile.

Quando si include **un percorso qualsiasi**, questa opzione include qualsiasi indirizzo IP in Internet non appena configurato località denominate. Quando si seleziona **un percorso qualsiasi**, gli amministratori possono scegliere di escludere **tutti i percorsi attendibili** o **selezionati**.

Ad esempio, alcune organizzazioni possono scegliere di non richiedere l'autenticazione a più fattori quando gli utenti sono connessi alla rete in una posizione attendibile, ad esempio la sede fisica. Gli amministratori possono creare un criterio che include qualsiasi percorso, ma esclude le località selezionate per le rispettive reti centrali.

Altre informazioni sulle località sono disponibili nell'articolo, [Qual è la condizione della posizione in Azure Active Directory accesso condizionale](location-condition.md).

## <a name="client-apps"></a>App client

Per impostazione predefinita, tutti i criteri di accesso condizionale appena creati verranno applicati a tutti i tipi di app client anche se la condizione delle app client non è configurata. 

> [!NOTE]
> Il comportamento della condizione delle app client è stato aggiornato nel 2020 agosto. Se sono presenti criteri di accesso condizionale, questi rimarranno invariati. Tuttavia, se si fa clic su un criterio esistente, l'interruttore Configura è stato rimosso e le app client a cui si applica il criterio sono selezionate.

> [!IMPORTANT]
> Gli accessi dai client di autenticazione legacy non supportano l'autenticazione a più fattori e non passano le informazioni sullo stato del dispositivo a Azure AD, quindi verranno bloccate dai controlli di concessione dell'accesso condizionale, ad esempio richiedere l'autenticazione a più fattori o dispositivi conformi Se sono presenti account che devono usare l'autenticazione legacy, è possibile escludere tali account dal criterio o configurare i criteri in modo che vengano applicati solo ai client di autenticazione moderni.

L'interruttore **Configura** quando impostato su **Sì** si applica agli elementi selezionati, quando è impostato su **No** , si applica a tutte le app client, inclusi i client di autenticazione moderni e legacy. Questo interruttore non viene visualizzato nei criteri creati prima del 2020 agosto.

- Client di autenticazione moderni
   - Browser
      - Sono incluse le applicazioni basate sul Web che usano protocolli quali SAML, WS-Federation, OpenID Connect o i servizi registrati come client Confidential OAuth.
   - App per dispositivi mobili e client desktop
      -  Questa opzione include applicazioni come le applicazioni desktop e per telefoni di Office.
- Client di autenticazione legacy
   - Client Exchange ActiveSync
      - Sono inclusi tutti gli utilizzi del protocollo Exchange ActiveSync (EAS).
      - Quando i criteri bloccano l'uso di Exchange ActiveSync, l'utente interessato riceverà un messaggio di posta elettronica di quarantena singolo. Questo messaggio di posta elettronica con fornisce informazioni sul motivo del blocco e include le istruzioni per la correzione, se possibile.
      - Gli amministratori possono applicare i criteri solo alle piattaforme supportate (ad esempio iOS, Android e Windows) tramite l'API Graph MS di accesso condizionale.
   - Altri client
      - Questa opzione include i client che usano i protocolli di autenticazione di base/legacy che non supportano l'autenticazione moderna.
         - SMTP autenticato: usato dai client POP e IMAP per inviare messaggi di posta elettronica.
         - Individuazione automatica: usata dai client Outlook e EAS per trovare le cassette postali in Exchange Online e connettervisi.
         - Exchange Online PowerShell: usato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per Exchange Online PowerShell, è necessario usare il modulo Exchange Online PowerShell per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell con l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Servizi Web Exchange: interfaccia di programmazione usata da Outlook, Outlook per Mac e app di terze parti.
         - IMAP4: usato dai client di posta elettronica IMAP.
         - MAPI su HTTP (MAPI/HTTP): usato da Outlook 2010 e versioni successive.
         - Rubrica offline: copia delle raccolte di elenchi di indirizzi scaricate e usate da Outlook.
         - Outlook via Internet (RPC su HTTP): usato da Outlook 2016 e versioni precedenti.
         - Servizio Outlook: usato dall'app di posta elettronica e di calendario per Windows 10.
         - POP3: usato dai client di posta elettronica POP.
         - Servizi Web per la creazione di report: funzionalità usata per recuperare i dati dei report in Exchange Online.

Queste condizioni vengono comunemente usate quando si richiede un dispositivo gestito, bloccando l'autenticazione legacy e bloccando le applicazioni Web, ma consentendo le app per dispositivi mobili o desktop.

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

In Windows 7, iOS, Android e macOS Azure AD identifica il dispositivo usando un certificato client di cui viene effettuato il provisioning quando il dispositivo viene registrato con Azure AD.  Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente deve selezionare questo certificato prima di usare il browser.

#### <a name="chrome-support"></a>Supporto di Chrome

Per il supporto di Chrome in **Windows 10 Creators Update (versione 1703)** o versioni successive, installare l' [estensione account Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Questa estensione è obbligatoria quando i criteri di accesso condizionale richiedono dettagli specifici del dispositivo.

Per distribuire automaticamente questa estensione ai browser Chrome, creare la chiave del Registro di sistema seguente:

- Percorso HKEY_LOCAL_MACHINE \Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Nome 1
- Tipo REG_SZ (stringa)
- Ppnbnpeolgkicgegkbkbjmhlideopiji di dati; HTTPS \: //clients2.Google.com/service/update2/crx

Per il supporto di Chrome in **Windows 8.1 e 7**, creare la chiave del Registro di sistema seguente:

- Percorso HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- Nome 1
- Tipo REG_SZ (stringa)
- Data {"pattern": " https://device.login.microsoftonline.com ", "Filter": {"Issuer": {"CN": "MS-Organization-Access"}}}

Questi browser supportano l'autenticazione del dispositivo, consentendo al dispositivo di essere identificato e convalidato rispetto a un criterio. Il controllo del dispositivo ha esito negativo se il browser è in esecuzione in modalità privata.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applicazioni per dispositivi mobili e client desktop supportati

Le organizzazioni possono selezionare app per **dispositivi mobili e client desktop** come app client.

Questa impostazione interessa i tentativi di accesso eseguiti dalle app per dispositivi mobili e client desktop seguenti:

| App client | Servizio di destinazione | Piattaforma |
| --- | --- | --- |
| Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| App Posta/Calendario/Contatti, Outlook 2016, Outlook 2013 (con autenticazione moderna)| Office 365 Exchange Online | Windows 10 |
| MFA e criteri relativi alle applicazioni. I criteri basati su dispositivo non sono supportati.| Qualsiasi servizio app Mie app | Android e iOS |
| Microsoft Team Services consente di controllare tutti i servizi che supportano Microsoft Teams e tutte le app client: Windows Desktop, iOS, Android, WP e Web Client | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| App Office 2016, Office 2013 (con autenticazione moderna), [client di sincronizzazione OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| App di Office 2016, app di Office universale, Office 2013 (con autenticazione moderna), [client di sincronizzazione OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (solo Word, Excel, PowerPoint e OneNote). | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| App Office per dispositivi mobili | Office 365 SharePoint Online | Android, iOS |
| App Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office per macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| App Outlook per dispositivi mobili | Office 365 Exchange Online | Android, iOS |
| App Power BI | Servizio Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype for Business | Office 365 Exchange Online| Android, iOS |
| App Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

### <a name="exchange-activesync-clients"></a>Client Exchange ActiveSync

- Le organizzazioni possono selezionare solo i client di Exchange ActiveSync quando assegnano criteri a utenti o gruppi. Se si seleziona **tutti gli**utenti, tutti gli utenti **guest ed External**o i **ruoli della directory** , tutti gli utenti verranno bloccati.
- Quando si crea un criterio assegnato ai client di Exchange ActiveSync, **Office 365 Exchange Online** deve essere l'unica applicazione cloud assegnata ai criteri. 
- Le organizzazioni possono limitare l'ambito di questo criterio a piattaforme specifiche usando la condizione per le **piattaforme del dispositivo** .

Se il controllo di accesso assegnato ai criteri USA **Richiedi app client approvata**, l'utente viene indirizzato per l'installazione e l'uso di Outlook Mobile client. Nel caso in cui sia necessaria **l'autenticazione** a più fattori, gli utenti interessati vengono bloccati perché l'autenticazione di base non supporta l'autenticazione a più fattori.

Per altre informazioni, vedere gli articoli seguenti:

- [Blocca l'autenticazione legacy con accesso condizionale](block-legacy-authentication.md)
- [Richiesta di app client approvate con accesso condizionale](app-based-conditional-access.md)

### <a name="other-clients"></a>Altri client

Selezionando **Altri client** è possibile specificare una condizione che influisce sulle app che usano l'autenticazione di base con protocolli di posta elettronica come IMAP, MAPI, POP, SMTP e le app Office meno recenti che non usano l'autenticazione moderna.

## <a name="device-state-preview"></a>Stato del dispositivo (anteprima)

La condizione di stato del dispositivo può essere usata per escludere i dispositivi ibridi Azure AD aggiunti e/o i dispositivi contrassegnati come conformi ai criteri di conformità di Microsoft Intune dai criteri di accesso condizionale di un'organizzazione.

Ad esempio, *tutti gli utenti* che accedono all'app Cloud *Microsoft Azure Management* , incluso **lo stato del dispositivo** , escluso il dispositivo **Azure ad ibrido aggiunto** e **il dispositivo contrassegnato come conforme** e per i *controlli di accesso*, **blocca**. 
   - Questo esempio crea un criterio che consente l'accesso solo a Microsoft Azure Management da dispositivi ibridi Azure AD Uniti e/o dispositivi contrassegnati come conformi.

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: Concedi](concept-conditional-access-grant.md)

- [Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)
