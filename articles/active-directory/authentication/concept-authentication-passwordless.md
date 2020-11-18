---
title: Azure Active Directory l'accesso senza password (anteprima)
description: Informazioni sulle opzioni per l'accesso senza password per Azure Active Directory usando le chiavi di sicurezza FIDO2 o l'app Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbe22a1af8fbf61629fa9df1267416ca3822884b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840084"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opzioni di autenticazione con password per Azure Active Directory

Funzionalità come l'autenticazione a più fattori sono un ottimo modo per proteggere l'organizzazione, ma gli utenti spesso sono frustrati con il livello di sicurezza aggiuntivo oltre a dover ricordare le proprie password. I metodi di autenticazione senza password sono più convenienti perché la password viene rimossa e sostituita da un elemento, più un elemento o un elemento che si conosce.

| Authentication  | Un'informazione disponibile | Un elemento o una conoscenza |
| --- | --- | --- |
| Accesso senza password | Dispositivo Windows 10, telefono o chiave di sicurezza | Biometrico o PIN |

Ogni organizzazione ha esigenze diverse per quanto riguarda l'autenticazione. Microsoft offre le tre opzioni di autenticazione senza password seguenti che si integrano con Azure Active Directory (Azure AD):

- Windows Hello for Business
- App Microsoft Authenticator
- Chiavi di sicurezza FIDO2

![Autenticazione: sicurezza e convenienza](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello for business è ideale per gli Information Worker che dispongono di un proprio PC Windows designato. Le credenziali biometriche e PIN sono direttamente collegate al PC dell'utente, che impedisce l'accesso a utenti diversi dal proprietario. Con l'integrazione dell'infrastruttura a chiave pubblica (PKI) e il supporto integrato per Single Sign-On (SSO), Windows Hello for business offre un metodo pratico per accedere facilmente alle risorse aziendali in locale e nel cloud.

![Esempio di accesso utente con Windows Hello for business](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

I passaggi seguenti illustrano il funzionamento del processo di accesso con Azure AD:

![Diagramma che descrive i passaggi necessari per l'accesso utente con Windows Hello for business](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Un utente accede a Windows usando biometrico o un movimento PIN. Il movimento Sblocca la chiave privata di Windows Hello for business e viene inviata all'Security Support Provider di autenticazione cloud, denominata *provider di app Cloud*.
1. Il provider AP cloud richiede un parametro nonce (un numero arbitrario casuale che può essere usato una sola volta) da Azure AD.
1. Azure AD restituisce un parametro nonce valido per 5 minuti.
1. Il provider AP cloud firma il parametro nonce usando la chiave privata dell'utente e restituisce il parametro nonce firmato al Azure AD.
1. Azure AD convalida il parametro nonce firmato usando la chiave pubblica registrata in modo sicuro dell'utente sulla firma nonce. Dopo la convalida della firma, Azure AD convalida il parametro nonce firmato restituito. Quando il parametro nonce viene convalidato, Azure AD crea un token di aggiornamento primario (PRT) con la chiave della sessione crittografata con la chiave di trasporto del dispositivo e la restituisce al provider di app cloud.
1. Il provider AP cloud riceve il PRT crittografato con la chiave della sessione. Usando la chiave di trasporto privata del dispositivo, il provider AP cloud decrittografa la chiave della sessione e protegge la chiave della sessione usando la Trusted Platform Module del dispositivo (TPM).
1. Il provider AP cloud restituisce una risposta di autenticazione riuscita a Windows. L'utente può quindi accedere a Windows e alle applicazioni cloud e locali senza la necessità di eseguire nuovamente l'autenticazione (SSO).

La [Guida alla pianificazione](/windows/security/identity-protection/hello-for-business/hello-planning-guide) di Windows Hello for business può essere usata per prendere decisioni sul tipo di distribuzione di Windows Hello for business e sulle opzioni che è necessario prendere in considerazione.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

È anche possibile consentire al telefono del dipendente di diventare un metodo di autenticazione con password. È possibile che si stia già usando l'app Microsoft Authenticator come comoda opzione di autenticazione a più fattori, oltre a una password. È anche possibile usare l'app Authenticator come opzione con password.

![Accedere a Microsoft Edge con l'app Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

L'app Authenticator converte qualsiasi telefono iOS o Android in una credenziale complessa senza password. Gli utenti possono accedere a qualsiasi piattaforma o browser inviando una notifica al telefono, associando un numero visualizzato sullo schermo a quello sul telefono e quindi usando la relativa biometrica (tocco o viso) o PIN per confermare. Per informazioni dettagliate [sull'installazione, vedere scaricare e installare l'app Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) .

L'accesso senza password con l'app Microsoft Authenticator Azure AD è attualmente in anteprima. L'uso dell'app Microsoft Authenticator per l'autenticazione secondaria per Azure AD Multi-Factor Authentication, la reimpostazione della password self-service (SSPR) o i token software per il GIURAmento sono GA. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'autenticazione con password con l'app Authenticator segue lo stesso modello di base di Windows Hello for business. È un po' più complicato perché l'utente deve essere identificato in modo che Azure AD possa trovare la versione dell'app Microsoft Authenticator usata:

![Diagramma che descrive i passaggi necessari per l'accesso utente con l'app Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. L'utente immette il proprio nome utente.
1. Azure AD rileva che l'utente dispone di una credenziale complessa e avvia il flusso di credenziali complesse.
1. Viene inviata una notifica all'app tramite Apple Push Notification Service (APNS) nei dispositivi iOS o tramite Firebase Cloud Messaging (FCM) su dispositivi Android.
1. L'utente riceve la notifica push e apre l'app.
1. L'app chiama Azure AD e riceve una richiesta di verifica della presenza e un parametro nonce.
1. L'utente completa la richiesta immettendo la relativa biometrica o il PIN per sbloccare la chiave privata.
1. Il parametro nonce viene firmato con la chiave privata e restituito a Azure AD.
1. Azure AD esegue la convalida della chiave pubblica/privata e restituisce un token.

Per iniziare a usare l'accesso senza password, completare le procedure seguenti:

> [!div class="nextstepaction"]
> [Abilitare il segno senza password con l'app Authenticator](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

L'Alleanza FIDO (Fast IDentity online) aiuta a promuovere standard di autenticazione aperti e a ridurre l'uso delle password come una forma di autenticazione. FIDO2 è lo standard più recente che incorpora lo standard di autenticazione Web (WebAuthn).

Le chiavi di sicurezza di FIDO2 sono un metodo di autenticazione senza password basato su standard unphishable che può provenire da qualsiasi fattore di forma. Fast Identity online (FIDO) è uno standard aperto per l'autenticazione con password. FIDO consente a utenti e organizzazioni di sfruttare lo standard per accedere alle risorse senza nome utente o password usando una chiave di sicurezza esterna o una chiave della piattaforma incorporata in un dispositivo.

Gli utenti possono registrarsi e quindi selezionare una chiave di sicurezza FIDO2 nell'interfaccia di accesso come mezzo di autenticazione principale. Le chiavi di sicurezza di FIDO2 sono in genere dispositivi USB, ma possono anche usare il Bluetooth o l'NFC. Grazie a un dispositivo hardware che gestisce l'autenticazione, la sicurezza di un account viene aumentata poiché non vi è una password che può essere esposta o individuata.

Le chiavi di sicurezza di FIDO2 possono essere usate per accedere ai dispositivi Azure AD o ibridi Azure AD aggiunti a dispositivi Windows 10 e ottenere l'accesso Single Sign-on alle risorse cloud e locali. Gli utenti possono anche accedere ai browser supportati. Le chiavi di sicurezza di FIDO2 sono un'ottima opzione per le aziende che hanno una sicurezza molto sensibile o hanno scenari o dipendenti che non sono disposti o in grado di usare il telefono come secondo fattore.

L'accesso con chiavi di sicurezza FIDO2 per Azure AD è attualmente in fase di anteprima. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Accedere a Microsoft Edge con una chiave di sicurezza](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Il processo seguente viene usato quando un utente accede con una chiave di sicurezza FIDO2:

![Diagramma che descrive i passaggi necessari per l'accesso utente con una chiave di sicurezza FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. L'utente inserisce la chiave di sicurezza FIDO2 nel computer.
2. Windows rileva la chiave di sicurezza FIDO2.
3. Windows invia una richiesta di autenticazione.
4. Azure AD restituisce un parametro nonce.
5. L'utente completa il proprio movimento per sbloccare la chiave privata archiviata nell'enclave protetta della chiave di sicurezza FIDO2.
6. La chiave di sicurezza FIDO2 firma il parametro nonce con la chiave privata.
7. La richiesta del token di aggiornamento principale (PRT) con parametro nonce firmato viene inviata a Azure AD.
8. Azure AD verifica il nonce firmato usando la chiave pubblica FIDO2.
9. Azure AD restituisce PRT per consentire l'accesso alle risorse locali.

Sebbene esistano molte chiavi FIDO2 certificate dall'alleanza di FIDO, Microsoft richiede l'implementazione da parte del fornitore di alcune estensioni facoltative della specifica del protocollo client-to-Authenticator (CTAP) di FIDO2 per garantire la massima sicurezza e l'esperienza ottimale.

Una chiave di sicurezza **deve** implementare le seguenti funzionalità ed estensioni dal protocollo CTAP di FIDO2 per essere compatibile con Microsoft:

| # | Funzionalità/attendibilità dell'estensione | Perché questa funzionalità o estensione è necessaria? |
| --- | --- | --- |
| 1 | Chiave residente | Questa funzionalità consente la portabilità della chiave di sicurezza, in cui le credenziali sono archiviate nella chiave di sicurezza. |
| 2 | PIN client | Questa funzionalità consente di proteggere le credenziali con un secondo fattore e si applica alle chiavi di sicurezza che non dispongono di un'interfaccia utente. |
| 3 | HMAC-segreto | Questa estensione garantisce che sia possibile accedere al dispositivo quando è offline o in modalità aereo. |
| 4 | Più account per RP | Questa funzionalità garantisce la possibilità di usare la stessa chiave di sicurezza tra più servizi, ad esempio l'account Microsoft e Azure Active Directory. |

### <a name="fido2-security-key-providers"></a>Provider di chiavi di sicurezza FIDO2

I provider seguenti offrono chiavi di sicurezza FIDO2 di diversi fattori di forma che sono noti come compatibili con l'esperienza senza password. Si consiglia di valutare le proprietà di sicurezza di queste chiavi contattando il fornitore e l'Alleanza FIDO.

| Provider | Contatto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Soluzioni TrustKey | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (gruppo Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| Onespan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Se si acquista e si prevede di usare le chiavi di sicurezza basate su NFC, è necessario un lettore NFC supportato per la chiave di sicurezza. Il lettore NFC non è un requisito o una limitazione di Azure. Rivolgersi al fornitore della chiave di sicurezza basata su NFC per un elenco dei lettori NFC supportati.

Se si è un fornitore e si desidera ottenere il dispositivo in questo elenco di dispositivi supportati, contattare [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

Per iniziare a usare le chiavi di sicurezza di FIDO2, completare le procedure seguenti:

> [!div class="nextstepaction"]
> [Abilita il segno senza password con le chiavi di sicurezza FIDO2](howto-authentication-passwordless-security-key.md)

## <a name="what-scenarios-work-with-the-preview"></a>Quali scenari funzionano con l'anteprima?

Azure AD funzionalità di accesso senza password sono attualmente in anteprima. Si applicano le considerazioni seguenti:

- Gli amministratori possono abilitare i metodi di autenticazione con password per il tenant
- Gli amministratori possono fare riferimento a tutti gli utenti o selezionare utenti/gruppi nel tenant per ogni metodo
- Gli utenti finali possono registrare e gestire questi metodi di autenticazione con password nel portale per gli account
- Gli utenti finali possono accedere con questi metodi di autenticazione senza password
   - App Microsoft Authenticator: funziona in scenari in cui viene usata l'autenticazione Azure AD, incluso in tutti i browser, durante l'installazione di Windows 10 (configurazione guidata) e con app per dispositivi mobili integrate in qualsiasi sistema operativo.
   - Chiavi di sicurezza: usare la schermata di blocco per Windows 10 e il Web in browser supportati come Microsoft Edge (sia legacy che New Edge).

## <a name="choose-a-passwordless-method"></a>Scegliere un metodo con password

La scelta tra queste tre opzioni con password dipende dai requisiti di sicurezza, piattaforma e app dell'azienda.

Di seguito sono riportati alcuni fattori da considerare quando si sceglie la tecnologia con password Microsoft:

||**Windows Hello for business**|**Accesso senza password con l'app Microsoft Authenticator**|**Chiavi di sicurezza FIDO2**|
|:-|:-|:-|:-|
|**Prerequisiti**| Windows 10 versione 1809 o successiva<br>Azure Active Directory| App Microsoft Authenticator<br>Telefono (dispositivi iOS e Android che eseguono Android 6,0 o versione successiva).|Windows 10, versione 1903 o successiva<br>Azure Active Directory|
|**Modalità**|Piattaforma|Software|Hardware|
|**Sistemi e dispositivi**|PC con un Trusted Platform Module incorporato (TPM)<br>Riconoscimento del PIN e della biometria |Riconoscimento del PIN e della biometria sul telefono|Dispositivi di sicurezza FIDO2 compatibili con Microsoft|
|**Esperienza utente**|Accedere con un PIN o un riconoscimento biometrico (facciale, Iris o impronta digitale) con i dispositivi Windows.<br>L'autenticazione di Windows Hello è associata al dispositivo. per accedere alle risorse aziendali, l'utente deve disporre sia del dispositivo sia di un componente di accesso, ad esempio un PIN o un fattore biometrico.|Eseguire l'accesso con un telefono cellulare con impronta digitale, riconoscimento facciale o Iris oppure PIN.<br>Gli utenti possono accedere al proprio account di lavoro o personale dal PC o dal telefono cellulare.|Accedere con il dispositivo di sicurezza FIDO2 (biometria, PIN e NFC)<br>L'utente può accedere al dispositivo in base ai controlli dell'organizzazione ed eseguire l'autenticazione in base al PIN, alla biometria usando dispositivi quali chiavi di sicurezza USB e smart card, chiavi o indossabili abilitati per NFC.|
|**Scenari abilitati**| Esperienza senza password con il dispositivo Windows.<br>Applicabile per PC di lavoro dedicati con la possibilità di Single Sign-On al dispositivo e alle applicazioni.|Soluzione senza password con il telefono cellulare.<br>Applicabile per accedere alle applicazioni aziendali o personali sul Web da qualsiasi dispositivo.|Esperienza senza password per i dipendenti che usano biometria, PIN e NFC.<br>Applicabile per i PC condivisi e in cui un telefono cellulare non è un'opzione valida, ad esempio per il personale help desk, il chiosco pubblico o il team ospedaliero|

Usare la tabella seguente per scegliere il metodo che supporterà i requisiti e gli utenti.

|Utente tipo|Scenario|Ambiente|Tecnologia con password|
|:-|:-|:-|:-|
|**Admin**|Proteggere l'accesso a un dispositivo per le attività di gestione|Dispositivo Windows 10 assegnato|Chiave di sicurezza di Windows Hello for business e/o FIDO2|
|**Admin**|Attività di gestione su dispositivi non Windows| Dispositivo mobile o non Windows|Accesso senza password con l'app Microsoft Authenticator|
|**Information Worker**|Lavoro di produttività|Dispositivo Windows 10 assegnato|Chiave di sicurezza di Windows Hello for business e/o FIDO2|
|**Information Worker**|Lavoro di produttività| Dispositivo mobile o non Windows|Accesso senza password con l'app Microsoft Authenticator|
|**Ruolo di lavoro Frontline**|Chioschi in una fabbrica, impianto, vendita al dettaglio o immissione di dati|Dispositivi Windows 10 condivisi|Chiavi di sicurezza FIDO2|

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare senza password in Azure AD, completare una delle procedure seguenti:

* [Abilita l'accesso senza password della chiave di sicurezza FIDO2](howto-authentication-passwordless-security-key.md)
* [Abilitare l'accesso senza password basato su telefono con l'app Authenticator](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Collegamenti esterni

* [FIDO Alliance](https://fidoalliance.org/)
* [Specifica FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
