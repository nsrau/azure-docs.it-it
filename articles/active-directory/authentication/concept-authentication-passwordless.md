---
title: Accesso senza password di Azure Active Directory (anteprima)Azure Active Directory passwordless sign-in (preview)
description: Informazioni sulle opzioni per l'accesso senza password ad Azure Active Directory usando le chiavi di sicurezza FIDO2 o l'app Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 402eaecbf03fd52fbb5e871fdd196da2bc9a3e1f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743529"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opzioni di autenticazione senza password per Azure Active DirectoryPasswordless authentication options for Azure Active Directory

L'autenticazione a più fattori (MFA) è un ottimo modo per proteggere l'organizzazione, ma gli utenti spesso si sentono frustrati con il livello di sicurezza aggiuntivo oltre a dover ricordare le password. I metodi di autenticazione senza password sono più convenienti perché la password viene rimossa e sostituita con qualcosa che hai, più qualcosa che sei o qualcosa che conosci.

|   | Qualcosa che hai | Qualcosa che sei o sai |
| --- | --- | --- |
| Accesso senza password | Windows 10 Dispositivo, telefono o chiave di sicurezza | Biometrici o PIN |

Ogni organizzazione ha esigenze diverse quando si tratta di autenticazione. Microsoft offre le tre opzioni di autenticazione senza password seguenti che si integrano con Azure Active Directory (Azure AD):

- Windows Hello for Business
- App Microsoft Authenticator
- Chiavi di protezione FIDO2

![Autenticazione: sicurezza e convenienza](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello for Business è ideale per gli information worker che dispongono di un proprio PC Windows designato. Il biometrico e il PIN sono direttamente legati al PC dell'utente, il che impedisce l'accesso a persone diverse dal proprietario. Con l'integrazione dell'infrastruttura a chiave pubblica (PKI) e il supporto integrato per l'accesso Single Sign-On (SSO), Windows Hello for Business offre un metodo pratico per accedere senza problemi alle risorse aziendali in locale e nel cloud.

![Esempio di accesso utente con Windows Hello for Business](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

The following steps show how the sign-in process works with Azure Active Directory.

![Diagramma che descrive i passaggi necessari per l'accesso degli utenti con Windows Hello for Business](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Un utente accede a Windows utilizzando il gesto biometrico o PIN. Il gesto sblocca la chiave privata di Windows Hello for Business e viene inviato al provider di supporto di sicurezza per l'autenticazione cloud, denominato *provider Cloud AP*.
1. Il provider Cloud AP richiede un nonce da Azure AD.
1. Azure AD restituisce un nonce valido per 5 minuti.
1. Il provider Cloud AP firma il nonce usando la chiave privata dell'utente e restituisce il nonce firmato ad Azure AD.
1. Azure AD convalida il nonce firmato usando la chiave pubblica registrata in modo sicuro dell'utente rispetto alla firma nonce. Dopo aver convalidato la firma, Azure AD convalida il nonce firmato restituito. Quando il nonce viene convalidato, Azure AD crea un token di aggiornamento primario (PRT) con chiave di sessione crittografata nella chiave di trasporto del dispositivo e lo restituisce al provider Cloud AP.
1. Il provider Cloud AP riceve il PRT crittografato con chiave di sessione. Utilizzando la chiave di trasporto privata del dispositivo, il provider di AP cloud decrittografa la chiave di sessione e protegge la chiave di sessione utilizzando il TPM (Trusted Platform Module) del dispositivo.
1. Il provider Cloud AP restituisce una risposta di autenticazione riuscita a Windows.The Cloud AP provider returns a successful authentication response to Windows. L'utente è quindi in grado di accedere alle applicazioni Windows, nonché cloud e locali senza la necessità di eseguire nuovamente l'autenticazione (SSO).

La guida alla [pianificazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) di Windows Hello for Business può essere utilizzata per prendere decisioni sul tipo di distribuzione di Windows Hello for Business e sulle opzioni che è necessario considerare.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

Consenti al telefono del tuo dipendente di diventare un metodo di autenticazione senza password. È possibile che si stia già utilizzando l'app Microsoft Authenticator come una comoda opzione di autenticazione a più fattori oltre a una password. È inoltre possibile utilizzare l'app Authenticator come opzione senza password.

![Accedere a Microsoft Edge con l'app Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

L'applicazione Authenticator trasforma qualsiasi telefono iOS o Android in una forte, credenziali senza password. Gli utenti possono accedere a qualsiasi piattaforma o browser ricevendo una notifica al proprio telefono, facendo corrispondere un numero sullo schermo a quello del telefono e quindi utilizzando il loro percorso biometrico (tocco o volto) o PIN per confermare. Per informazioni dettagliate sull'installazione, vedere [Scaricare e installare l'app Microsoft Authenticator.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)

L'autenticazione senza password tramite l'app Authenticator segue lo stesso modello di base di Windows Hello for Business. È un po' più complicato in quanto l'utente deve essere identificato in modo che Azure AD possa trovare la versione dell'app Microsoft Authenticator in uso:It's a little more complicated as the user needs to be identified so that Azure AD can find the Microsoft Authenticator App version being used:

![Diagramma che descrive i passaggi necessari per l'accesso dell'utente con l'app Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. L'utente immette il proprio nome utente.
1. Azure AD rileva che l'utente dispone di credenziali complesse e avvia il flusso di credenziali complesse.
1. Una notifica viene inviata all'app tramite Apple Push Notification Service (APNS) su dispositivi iOS o tramite Firebase Cloud Messaging (FCM) su dispositivi Android.
1. L'utente riceve la notifica push e apre l'app.
1. L'app chiama Azure AD e riceve una richiesta di verifica della presenza e un nonce.
1. L'utente completa la sfida immettendo il proprio PIN o biometrico per sbloccare la chiave privata.
1. Il nonce è firmato con la chiave privata e inviato ad Azure AD.
1. Azure AD esegue la convalida della chiave pubblica/privata e restituisce un token.

## <a name="fido2-security-keys"></a>Chiavi di protezione FIDO2

Le chiavi di sicurezza FIDO2 sono un metodo di autenticazione senza password senza standard infallibile che può essere fornito in qualsiasi fattore di forma. Fast Identity Online (FIDO) è uno standard aperto per l'autenticazione senza password. FIDO consente agli utenti e alle organizzazioni di sfruttare lo standard per accedere alle proprie risorse senza un nome utente o una password utilizzando una chiave di sicurezza esterna o una chiave di piattaforma incorporata in un dispositivo.

Per l'anteprima pubblica, i dipendenti possono usare le chiavi di sicurezza per accedere ai dispositivi Windows 10 aggiunti ad Azure AD o azure AD ibrido e ottenere l'accesso Single Sign-On alle risorse cloud e locali. Gli utenti possono anche accedere ai browser supportati. Le chiavi di sicurezza FIDO2 sono un'ottima opzione per le aziende che sono molto sensibili alla sicurezza o hanno scenari o dipendenti che non sono disposti o in grado di utilizzare il telefono come secondo fattore.

![Accedere a Microsoft Edge con una chiave di sicurezza](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Il processo seguente viene utilizzato quando un utente accede con una chiave di protezione FIDO2:The following process is used when a user signs in with a FIDO2 security key:

![Diagramma che descrive i passaggi necessari per l'accesso dell'utente con una chiave di protezione FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. L'utente collega la chiave di protezione FIDO2 nel proprio computer.
2. Windows rileva la chiave di protezione FIDO2.
3. Windows invia una richiesta di autenticazione.
4. Azure AD restituisce un nonce.
5. L'utente completa il gesto per sbloccare la chiave privata archiviata nell'enclave sicura della chiave di protezione FIDO2.
6. La chiave di sicurezza FIDO2 firma il nonce con la chiave privata.
7. La richiesta di token di aggiornamento primario (PRT) con nonce firmato viene inviata ad Azure AD.
8. Azure AD verifica il nonce firmato usando la chiave pubblica FIDO2.
9. Azure AD restituisce PRT per consentire l'accesso alle risorse locali.

Sebbene il fornitore implementi molte chiavi certificate FIDO2 da FIDO Alliance, Microsoft richiede che alcune estensioni facoltative della specifica CTAP (Client-to-Authenticator Protocol) FIDO2 siano implementate dal fornitore per garantire la massima sicurezza e la migliore esperienza.

Una chiave di protezione **DEVE** implementare le funzionalità e le estensioni seguenti dal protocollo FIDO2 CTAP per essere compatibile con Microsoft:

| # | Fiducia caratteristica/estensione | Perché è necessaria questa funzionalità o estensione? |
| --- | --- | --- |
| 1 | Chiave residente | Questa funzionalità consente di utilizzare la chiave di sicurezza in modo che sia portabile, in cui le credenziali sono archiviate nella chiave di sicurezza. |
| 2 | Pin client | Questa funzionalità consente di proteggere le credenziali con un secondo fattore e si applica alle chiavi di sicurezza che non dispongono di un'interfaccia utente. |
| 3 | hmac-segreto | Questa estensione garantisce l'accesso al dispositivo quando è off-line o in modalità aereo. |
| 4 | Più account per RP | Questa funzionalità consente di usare la stessa chiave di sicurezza in più servizi, ad esempio Microsoft Account e Azure Active Directory.This feature ensures you can use the same security key across multiple services like Microsoft Account and Azure Active Directory. |

I provider seguenti offrono chiavi di sicurezza FIDO2 di diversi fattori di forma che sono noti per essere compatibili con l'esperienza senza password. Ti invitiamo a valutare le proprietà di sicurezza di queste chiavi contattando il fornitore e FIDO Alliance.

| Provider | Contatto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| L'esurità | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Gruppo Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Se si acquista e si prevede di utilizzare chiavi di sicurezza basate su NFC, è necessario un lettore NFC supportato per la chiave di sicurezza. Il lettore NFC non è un requisito o una limitazione di Azure.The NFC reader isn't an Azure requirement or limitation. Rivolgersi al fornitore per la chiave di protezione basata su NFC per un elenco dei lettori NFC supportati.

Se si è un fornitore e si desidera ottenere il [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)dispositivo in questo elenco di dispositivi supportati, contattare .

## <a name="what-scenarios-work-with-the-preview"></a>Quali scenari funzionano con l'anteprima?

- Gli amministratori possono abilitare i metodi di autenticazione senza password per il tenant
- Gli amministratori possono scegliere come target tutti gli utenti o selezionare utenti/gruppi all'interno del tenant per ogni metodo
- Gli utenti finali possono registrare e gestire questi metodi di autenticazione senza password nel portale degli account
- Gli utenti finali possono accedere con questi metodi di autenticazione senza password
   - App Microsoft Authenticator: funziona in scenari in cui viene usata l'autenticazione di Azure AD, inclusi tutti i browser, durante l'installazione di Windows 10 Out of Box e con app per dispositivi mobili integrate in qualsiasi sistema operativo.
   - Tasti di sicurezza: lavora sulla schermata di blocco per Windows 10 e sul Web nei browser supportati come Microsoft Edge (sia legacy che il nuovo Edge).

## <a name="choose-a-passwordless-method"></a>Scegliere un metodo senza password

La scelta tra queste tre opzioni senza password dipende dalla sicurezza, dalla piattaforma e dai requisiti dell'app della tua azienda.

Ecco alcuni fattori da considerare quando si sceglie la tecnologia Microsoft passwordless:

||**Windows Hello for Business**|**Accesso senza password con l'app Microsoft Authenticator**|**Chiavi di protezione FIDO2**|
|:-|:-|:-|:-|
|**Prerequisito**| Windows 10 versione 1809 o successiva<br>Azure Active Directory| App Microsoft Authenticator<br>Telefono (dispositivi iOS e Android con Android 6.0 o versione successiva).|Windows 10 versione 1809 o successiva<br>Azure Active Directory|
|**Modalità**|Piattaforma|Software|Hardware|
|**Sistemi e dispositivi**|PC con un TPM (Trusted Platform Module) integrato<br>Riconoscimento del PIN e della biometria |Riconoscimento del PIN e della biometria sul telefono|Dispositivi di sicurezza FIDO2 compatibili con Microsoft|
|**Esperienza utente**|Accedere utilizzando un PIN o un riconoscimento biometrico (facciale, iride o impronta digitale) con i dispositivi Windows.<br>L'autenticazione di Windows Hello è legata al dispositivo; l'utente necessita sia del dispositivo che di un componente di accesso, ad esempio un PIN o un fattore biometrico per accedere alle risorse aziendali.|Accedere utilizzando un telefono cellulare con scansione delle impronte digitali, riconoscimento facciale o dell'iride o PIN.<br>Gli utenti accedono al lavoro o all'account personale dal PC o dal telefono cellulare.|Accedere utilizzando il dispositivo di sicurezza FIDO2 (biometria, PIN e NFC)<br>L'utente può accedere ai dispositivi in base ai controlli dell'organizzazione ed eseguire l'autenticazione in base al PIN, alla biometria utilizzando dispositivi quali chiavi di sicurezza USB e smart card, chiavi o dispositivi indossabili abilitati per NFC.|
|**Scenari abilitati**| Esperienza senza password con il dispositivo Windows.<br>Applicabile per PC di lavoro dedicato con possibilità di single sign-on su dispositivi e applicazioni.|Soluzione senza password ovunque utilizzando il telefono cellulare.<br>Applicabile per l'accesso ad applicazioni di lavoro o personali sul Web da qualsiasi dispositivo.|Esperienza senza password per i lavoratori che utilizzano biometria, PIN e NFC.<br>Applicabile per PC condivisi e in cui un telefono cellulare non è un'opzione praticabile (ad esempio per il personale dell'help desk, il chiosco pubblico o il team ospedaliero)|

Utilizzare la tabella seguente per scegliere il metodo che supporterà le esigenze e gli utenti.

|Persona|Scenario|Environment|Tecnologia senza password|
|:-|:-|:-|:-|
|**Amministrazione**|Accesso sicuro a un dispositivo per le attività di gestioneSecure access to a device for management tasks|Dispositivo Windows 10 assegnato|Chiave di protezione Windows Hello for Business e/o FIDO2|
|**Amministrazione**|Attività di gestione su dispositivi non Windows| Dispositivo mobile o non Windows|Accesso senza password con l'app Microsoft Authenticator|
|**Information Worker**|Produttività lavoro|Dispositivo Windows 10 assegnato|Chiave di protezione Windows Hello for Business e/o FIDO2|
|**Information Worker**|Produttività lavoro| Dispositivo mobile o non Windows|Accesso senza password con l'app Microsoft Authenticator|
|**Lavoratore di prima linea**|Chioschi in una fabbrica, un impianto, una vendita al dettaglio o l'immissione di dati|Dispositivi Windows 10 condivisi|Chiavi di protezione FIDO2|

## <a name="next-steps"></a>Passaggi successivi

[Abilitare le opzioni di protezione FIDO2 senza password nell'organizzazione](howto-authentication-passwordless-security-key.md)

[Abilitare le opzioni di senza password basate su telefono nell'organizzazione](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Collegamenti esterni

[Alleanza FIDO](https://fidoalliance.org/)

[Specifiche FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
