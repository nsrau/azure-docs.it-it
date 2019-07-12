---
title: Comprensione di una realtà senza password con Azure Active Directory | Microsoft Docs
description: Questa guida è utile CEO, CIO, CISO, Chief Identity Architect, Enterprise Architects e sicurezza e responsabili delle decisioni IT responsabile della scelta di un metodo di autenticazione senza password per la propria implementazione di Azure Active Directory.
services: active-directory
keywords: passwordless, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723441"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Un mondo senza password con Azure Active Directory

È possibile suddividere la relazione con le password. Le password siano state positive per noi in passato, ma nell'area di lavoro digitale di oggi si sono diventati un vettore di attacco relativamente facile pirati informatici. Pirati informatici amare le password e non è difficile visualizzare il motivo per cui se si considera che in genere rifiutato le password in Azure Active Directory (Azure AD) includono condizioni, ad esempio l'anno, mese, la stagione o una variabile locale sportivi team. Inoltre, [research ha dimostrato](https://aka.ms/passwordguidance) che tradizionale consigli per la gestione delle password, ad esempio i requisiti di lunghezza, i requisiti di complessità e le frequenze di modifica controproducente per diversi motivi correlati a natura umana.

Sono tre tipi di attacchi comunemente usati per compromettere gli account utente spray password, phishing e violazioni di riproduzione. Funzionalità di Azure AD, ad esempio [blocco smart](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [password vietate](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), e [protezione con password](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) offrono protezione contro questi tipi di attacchi. Analogamente, che implementa [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA), o verifica in due passaggi, fornisce ulteriore sicurezza richiedendo una seconda forma di autenticazione. Ma nel lungo termine, una soluzione senza password è la soluzione migliore per garantire il metodo di autenticazione più sicuro.

Questo articolo è l'inizio del percorso che consentono di comprendere e implementare soluzioni senza password di Microsoft e consentono di che scegliere tra uno o più delle opzioni seguenti:

* **Windows Hello for Business**. In Windows 10, Windows Hello for Business sostituisce le password con l'autenticazione a due fattori avanzata su PC e dispositivi mobili. Questo processo di autenticazione è costituito da un nuovo tipo di credenziale utente, che è associata a un dispositivo e usa un sensore biometrico o un PIN.

* **Accedi senza password con Microsoft Authenticator**. L'app Microsoft Authenticator è utilizzabile per accedere a un account Azure AD senza usare una password. Come per la tecnologia di Windows Hello for Business, Microsoft Authenticator Usa l'autenticazione basata su chiave per consentire una credenziale utente che è associata a un dispositivo e Usa un elemento biometrico o PIN.

* **Le chiavi di sicurezza FIDO2**. FIDO2 fornisce le credenziali di accesso di servizio di crittografia che sono univoci per ogni sito Web e vengono archiviate in un dispositivo locale come Windows Hello o le chiavi di protezione esterne. Queste chiavi di sicurezza sono resistente ai rischi di phishing, furto di password e attacchi di tipo replay. In combinazione con la verifica utente tramite la biometria o PIN, la soluzione è due esigenze di sicurezza moderne riunione verifica fattore.

## <a name="the-future-of-passwordless-authentication"></a>Il futuro dell'autenticazione senza password

Oggi, le banche, le società della carta di credito e altre organizzazioni e servizi online spesso proteggono il tuo account tramite la richiesta di verifica dell'identità due volte: una volta con la password, quindi nuovamente dal telefono, testo o un'app. Durante l'autenticazione a più fattori per risolvere il problema di sicurezza delle password condivisi, rubato o è stata ipotizzata, non vengono fornite informazioni il fattore di inconvenienti quando si tenta di ricordarli. Quali utenti e organizzazioni nell'era del cloud di oggi è metodi di autenticazione senza password sono altamente sicure *e* pratico.

![Sicurezza e praticità](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello for Business, senza password Accedi con Microsoft Authenticator e chiavi di sicurezza FIDO2 che tutte condividono un'architettura semplice e comune, offrendo agli utenti un metodo di autenticazione sicura e conveniente utilizzare. Tutte e tre sono basati sulla tecnologia di chiave pubblica/privata, è necessario un movimento locale, ad esempio un elemento biometrico o PIN e le chiavi private associate a un singolo dispositivo e in modo sicuro archiviati e mai condiviso.

## <a name="windows-hello-for-business"></a>Windows Hello for Business

In Windows 10, Windows Hello for Business sostituisce le password con l'autenticazione a due fattori avanzata su PC e dispositivi. L'autenticazione è costituito da un nuovo tipo di credenziali dell'utente che sono associato a un dispositivo e viene utilizzato un movimento biometrico o PIN che consente agli utenti di autenticarsi con Azure AD, nonché di un server di Active Directory locale. È sufficiente firma in un dispositivo con Windows Hello for Business è semplice. È usare un PIN o un movimento biometrico, ad esempio il riconoscimento facciale o impronta digitale.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello per scenari di Business

Windows Hello for Business è ideale per gli information worker che dispongono di propri PC Windows designato. La biometrica e le credenziali sono direttamente associate al PC dell'utente, che impedisce l'accesso da chiunque, eccetto il proprietario. Integrazione di infrastruttura a chiave pubblica e il supporto incorporato per il single sign-on (SSO), Windows Hello for Business fornisce un metodo semplice e pratico per accedere facilmente alle risorse aziendali in locale e nel cloud.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello per le considerazioni sulla distribuzione di Business

Windows Hello for Business è un sistema distribuito che vengono usati numerosi componenti per eseguire l'operazione di registrazione del dispositivo, il provisioning e autenticazione. Pertanto, la distribuzione richiede una pianificazione appropriata tra più team all'interno dell'organizzazione. Di Windows Hello for Business [Guida alla pianificazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) utilizzabile per aiutare a prendere decisioni sul tipo di Windows Hello per la distribuzione di Business e le opzioni è necessario prendere in considerazione.

Per le distribuzioni ibride o in locale, si prevede di avere:

* Una rete ben connessa, funzionante

* Accesso a Internet

* Server multi-factor Authentication per supporta l'autenticazione MFA durante Windows Hello per il provisioning di Business

* Risoluzione dei nomi corretta, i nomi interni ed esterni

* Active Directory e un numero sufficiente di controller di dominio per ogni sito per supportare l'autenticazione

* Servizi certificati Active 2012 o versione successivo

* Uno o più workstation che eseguono Windows 10, versione 1903

Poiché tutti i tipi di Windows Hello per le distribuzioni Business usano certificati emessi da enterprise per i controller di dominio come una radice di attendibilità, è consigliabile rinnovare automaticamente i certificati scaduti da [configurare la registrazione automatica per server e l'utente i certificati](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment). Per le distribuzioni locali, il provider di identità è il server locale che esegue il ruolo di Windows Server Active Directory Federation Services (ADFS). I sistemi federati richiedono in genere un array di server con bilanciamento del carico, noto come farm. Questa farm è configurata in una topologia di rete interna e perimetrale per garantire la disponibilità elevata per le richieste di autenticazione.

Quando si impostano criteri di gruppo in modo da richiedere Windows Hello for Business nell'ambiente di lavoro, è possibile preparare gli utenti dell'organizzazione tramite cui viene spiegato come usare Windows Hello. Ad esempio, quando un utente configura un nuovo dispositivo, viene richiesto di scegliere tra **questo dispositivo appartiene alla mia organizzazione** oppure **si tratta di un dispositivo persona**. Per i dispositivi aziendali, è necessario selezionare il primo. Gli utenti dovranno anche essere detto quale opzione di connessione è necessario selezionare: **Aggiunta ad Azure AD** oppure **configurare un account locale (aggiunta a un dominio in un secondo momento)** .

È comune per gli utenti sono abituati a usare un movimento biometrico per autenticare ogni giorno per infine dimentica il PIN. Per evitare le chiamate all'help desk, è consigliabile fornire agli utenti la possibilità di reimpostare dimenticato [password](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) e [pin](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset).

Sono disponibili molte opzioni da cui è possibile scegliere durante la distribuzione di Windows Hello for Business. Con svariate opzioni garantisce che quasi tutte le organizzazioni possono distribuire Windows Hello for Business. Prendere in considerazione i seguenti tipi di distribuzioni supportate:

* [Azure AD ibrido distribuzione attendibilità chiave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Azure AD ibrido distribuzione attendibilità del certificato](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Guide alla distribuzione del servizio Single Sign-on di aggiunta ad Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Nella distribuzione con attendibilità totale chiave locale](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Distribuzione Trust certificato locale](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Molte opzioni rende la distribuzione complessa. Tuttavia, la maggior parte delle organizzazioni probabilmente determinerà che avere già implementato la maggior parte dell'infrastruttura di cui di Windows Hello for Business deployment dipende. Indipendentemente da ciò, è importante tenere presente che Windows Hello for Business è un sistema distribuito e una pianificazione appropriata è consigliata.

È consigliabile leggere [pianificazione di un Windows Hello for Business Deployment](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) che consentono di scegliere il modello di distribuzione migliore adatto per l'organizzazione specifica. Quindi, in base alla pianificazione è apportare, vedere la [Windows Hello for Business Deployment Guide](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) al fine di garantire una corretta distribuzione di Windows Hello for Business nell'ambiente esistente.

### <a name="how-windows-hello-for-business-works"></a>Come funziona Windows Hello for Business

#### <a name="user-sets-up-windows-hello-for-business"></a>Configurazione di Windows Hello for Business

Dopo una verifica in due passaggi iniziali dell'utente durante la registrazione, Windows Hello è configurato nel dispositivo dell'utente e Windows chiede all'utente di impostare un movimento, che può essere un elemento biometrico, ad esempio il riconoscimento facciale o impronta digitale o un PIN. Una volta impostato, l'utente fornisce il movimento per verificare la propria identità. Windows Usa quindi Windows Hello per autenticare gli utenti.

In base alle funzionalità del dispositivo Windows 10, si avrà un'enclave protetta incorporata, nota come un hardware trusted platform module (TPM) o un software di TPM. Il TPM archivia la chiave privata, che richiede il viso, tramite impronta digitale o PIN per sbloccarlo. I dati biometrici non viene effettuato il roaming e non vengono mai inviati al server o dispositivi esterni. È inutile singola raccolta, che un utente malintenzionato può compromettere per rubare dati biometrici, perché Windows Hello archivia solo i dati di identificazione biometrica nel dispositivo.

> [!TIP]
> Nell'area di un PIN aspetto di una password, ma è effettivamente più sicura. Un'importante differenza tra una password e un PIN è che il PIN è associato al dispositivo specifico in cui è stato configurato. Qualcuno ruba le password possa accedere all'account da qualsiasi posizione. Ma se essi rubare il PIN, dovranno essere rubare il dispositivo fisico troppo! Inoltre, poiché un PIN è locale per il dispositivo, quest'ultima non viene trasmessa in qualsiasi punto in modo che non possa essere intercettato durante la trasmissione o rubato da un server.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Con Windows Hello for Business per l'accesso utente

Windows Hello per le letture biometriche di Business e i codici PIN asimmetrica (chiave pubblica/privata) di usare la crittografia per l'autenticazione. Durante l'autenticazione, la crittografia è legata alla chiave di sessione TLS, che protegge il processo di autenticazione in modo che un attacco di tipo man-in-the-middle (MiTM) non può rubare il token di sicurezza risultante o dell'elemento e riprodurla da altre posizioni.

Windows Hello for Business offre un'esperienza di accesso conveniente che autentica l'utente ad Azure AD e le risorse di Active Directory. Dispositivi aggiunti AD ad Azure l'autenticazione ad Azure durante l'accesso e possono facoltativamente eseguire l'autenticazione ad Active Directory. I dispositivi aggiunti ad Azure Active Directory ibrido autenticarsi in Active Directory durante l'accesso ed eseguire l'autenticazione ad Azure Active Directory in background.

![Vedere l'immagine di origine](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

I passaggi seguenti illustrano l'autenticazione dell'accesso ad Azure Active Directory.

![Schermata di blocco di Windows 10](./media/azure-ad/azure-ad-pwdless-image3.png)

1. Accesso degli utenti in Windows usando biometrico o PIN movimenti. Il movimento Sblocca di Windows Hello per la chiave privata aziendale e viene inviato al provider di supporto di protezione Cloud Authentication, definito come provider di Cloud app.

2. Il provider di Cloud AP richiede un parametro nonce da Azure Active Directory.

3. Azure AD restituisce un parametro nonce è valido per 5 minuti.

4. Il provider di Cloud AP firma il parametro nonce usando la chiave privata dell'utente e restituisce il parametro nonce signed ad Azure Active Directory.

5. Azure Active Directory convalida il parametro nonce firmato usando la chiave dell'utente registrato in modo sicuro pubblico in base alla firma nonce. Dopo aver convalidato la firma, quindi Azure AD convalida il parametro nonce firmato restituito. Dopo aver verificato il parametro nonce, Azure AD crea un PRT con chiave della sessione che viene crittografato per la chiave di trasporto del dispositivo e lo restituisce al provider di Cloud PA.

6. Il provider di Cloud AP riceve la PRT crittografata con chiave della sessione. Usando la chiave del dispositivo trasporto privato, il provider di Cloud AP decrittografa la chiave di sessione e protegge la chiave di sessione tramite TPM del dispositivo.

7. Il provider di Cloud AP restituisce una risposta di autenticazione di Windows dopo il quale l'utente è in grado di accedere a Windows, nonché cloud e applicazioni senza la necessità di nuovo l'autenticazione in locale (SSO).

Per un approfondimento sul processo di autenticazione in altri scenari che coinvolgono Windows Hello for Business, vedere [Windows Hello for Business e l'autenticazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Utente gestisce le Windows Hello per le credenziali aziendali

Il [servizi di reimpostazione PIN Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) è una funzionalità in Azure AD che consente agli utenti di reimpostare il PIN, se necessario. Tramite criteri di gruppo, Microsoft Intune o con un MDM compatibile, un amministratore può configurare i dispositivi Windows 10 per il servizio di reimpostazione PIN Microsoft che consente agli utenti di reimpostare il PIN dimenticato tramite le impostazioni o sopra la schermata di blocco senza la necessità di usare in modo sicuro nuova registrazione.

In alcuni casi gli utenti dovranno eseguire il fallback all'utilizzo delle password. [Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) è un'altra funzionalità di Azure AD che consente agli utenti di reimpostare le password senza dover contattare il personale IT. Gli utenti devono registrare per o essere registrati per la prima di usare il servizio di reimpostazione della password self-service. Durante la registrazione, l'utente sceglie uno o più metodi di autenticazione abilitati dall'organizzazione. SSPR consente agli utenti di essere sbloccati rapidamente e continuare a funzionare indipendentemente dalla loro posizione o l'ora del giorno. Consentendo agli utenti di sbloccare autonomamente, l'organizzazione può ridurre il tempo non produttivo ed elevati costi di assistenza per problemi più comuni correlati alle password.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Accedi senza password con Microsoft Authenticator

Accedi senza password con Microsoft Authenticator sono può essere usata per accedere agli account di Azure AD con accesso tramite telefono in un'altra soluzione senza password. È comunque necessario verificare la tua identità, fornendo un elemento che noto e un elemento è disponibile, ma phone accesso ti permette di ignorare immettendo la password ed esegue tutti la verifica dell'identità sul tuo dispositivo mobile con l'impronta digitale, volto o PIN.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Scenari senza password di Microsoft Authenticator

L'app Microsoft Authenticator consente agli utenti di verificare la propria identità e l'autenticazione al loro account aziendale o personale. Può essere utilizzato anche per aumentare una password con un passcode monouso o inviare notifiche push o sostituire completamente la necessità di una password. Invece di usare una password, gli utenti confermare la propria identità usando il proprio telefono per dispositivi mobili tramite analisi di impronte digitali, riconoscimento facciale o iris o PIN. Basato su tecnologia sicura simile a ciò che Windows Hello viene utilizzato, questo strumento è compresso in una semplice app in un dispositivo mobile rende un'opzione utile per gli utenti. L'app Microsoft Authenticator è disponibile per Android e iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Considerazioni sulla distribuzione di Microsoft Authenticator

Prerequisiti per l'uso dell'app Microsoft Authenticator per eseguire l'operazione di accesso senza password in Azure ad includono quanto segue:

* Gli utenti finali sono abilitati per Azure multi-Factor Authentication

* La possibilità per gli utenti registrare i propri dispositivi usando Microsoft Intune o con una soluzione di gestione (MDM) dei dispositivi mobili di terze parti

Supponendo che siano soddisfatti questi requisiti, gli amministratori abilitare l'accesso tramite telefono nel tenant tramite [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Dopo l'accesso tramite telefono in abilitazione nel tenant, gli utenti finali la possibilità di rifiutare di accesso tramite telefono selezionando l'account aziendale o dell'istituto di istruzione nel **conti** schermata dell'app, quindi selezionando **abilitare accesso tramite telefono in** .

Gli utenti finali se accedi senza password sono abilitata da un amministratore, sarà necessario soddisfare i requisiti seguenti:

* Registrati in Azure multi-Factor Authentication

* Versione più recente di Microsoft Authenticator installata su dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva

* Account aziendale o dell'istituto di istruzione con le notifiche push aggiunta all'app

Per evitare il rischio di rimanere bloccati fuori il tuo account o che sia necessario ricreare gli account in un nuovo dispositivo, è consigliabile usare Microsoft Authenticator per [le credenziali dell'account di backup](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) nel cloud. Al termine del backup, è anche possibile usare l'app per ripristinare le informazioni in un nuovo dispositivo, evitando potenzialmente di rimanere bloccati senza accesso al dispositivo o di dover ricreare gli account.

Poiché la maggior parte degli utenti sono abituati a usare le password solo per l'autenticazione, è importante che l'organizzazione allo scopo di informare gli utenti su questo processo. Consapevolezza può ridurre la probabilità che gli utenti chiamare l'help desk per qualsiasi [problemi](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) relativi all'accesso tramite l'app Microsoft Authenticator.

> [!NOTE]
> Un potenziale punto di errore per questa soluzione è quando un roaming utente si trova in una posizione in cui è presente nessuna connettività a Internet. Le chiavi di sicurezza FIDO2 e Windows Hello for Business non sono soggetti alle stesse limitazioni.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Accedi come senza password con Microsoft Authenticator funziona

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>L'utente imposta Accedi senza password con Microsoft Authenticator

L'app Microsoft Authenticator può essere usato come una soluzione senza password per accedere a un account Azure AD, passaggi devono essere eseguiti da un amministratore e gli utenti finali.

In primo luogo, un amministratore dovrà [abilitare l'uso dell'app come credenziale](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) nel tenant usando Windows PowerShell. L'amministratore è necessario anche consentire agli utenti finali per Azure multi-Factor Authentication (MFA di Azure) e configurare l'app Microsoft Authenticator come uno dei [metodi di verifica](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Gli utenti finali dovranno [scaricare e installare](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) l'app Microsoft Authenticator e [configurare il proprio account](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) usino l'app Microsoft Authenticator come uno dei metodi di verifica.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Utente con Microsoft Authenticator per l'accesso senza password

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Mentre la schermata di blocco di Windows 10 non include l'app Microsoft Authenticator come opzione di accesso, gli utenti possono comunque immettere il nome utente e quindi ricevere una notifica push sul dispositivo mobile per verificare di presenza. Gli utenti di confermare la propria presenza, corrispondenza di un numero nella schermata di accesso, quindi fornire un'analisi viso, tramite impronta digitale o PIN per sbloccare la chiave privata e completare l'autenticazione. Questo metodo di verifica a più fattori è più sicuro di una password ed è più pratico piuttosto che semplicemente immettere una password e un codice.

![Accedi Authenticator](./media/azure-ad/azure-ad-pwdless-image4.png)

Autenticazione senza password con Microsoft Authenticator segue lo stesso modello di base di Windows Hello for Business, ma è un po' più complesso perché l'utente deve essere identificato in modo che Azure AD è possibile trovare la versione di app Microsoft Authenticator viene utilizzato.

![Processo di autenticazione](./media/azure-ad/azure-ad-pwdless-image5.png)

1. L'utente immette il nome utente.

2. Azure AD rileva che l'utente dispone di una credenziale sicura e avvia il flusso delle credenziali sicuro.

3. Notifica viene inviata all'app tramite Apple Push Notification Service (APNS) nei dispositivi iOS o tramite Firebase Cloud Messaging (FCM) nei dispositivi Android.

4. L'utente riceve la notifica push e apre l'app.

5. L'app chiama Azure AD e riceve una richiesta di proof-of-presence e parametro nonce.

6. L'utente ha completato la richiesta di verifica immettendo la biometria o PIN per sbloccare la chiave privata.

7. Il parametro nonce viene firmato con la chiave privata e inviato ad Azure AD.

8. Azure AD esegue la convalida di chiavi pubblica/privata e restituisce un token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Utente gestisce le senza password Accedi con credenziali di Microsoft Authenticator

Con [combinati registrazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), gli utenti possono registrarsi e ottenere i vantaggi di Azure multi-Factor Authentication e reimpostazione della password self-service. Gli utenti di registrare e gestire queste impostazioni, passare a loro [pagina del profilo personale](https://aka.ms/mysecurityinfo). Oltre ad abilitare SSPR, combinati registrazione supporta più metodi di autenticazione e le azioni.

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

FIDO2 è la versione più recente dello standard FIDO Alliance e include due componenti, standard di autenticazione Web (WebAuthN) di W3C e il protocollo di Client-a-Authenticator FIDO Alliance (CTAP2) corrispondente. Gli standard FIDO2 consentono agli utenti di sfruttare hardware, per dispositivi mobili e basato su dati biometrici autenticatori per autenticare con facilità con molti siti Web in ambienti desktop e per dispositivi mobili e App.

I partner Microsoft e del settore hanno lavorato insieme di dispositivi di sicurezza FIDO2 per Windows Hello abilitare l'autenticazione semplice e sicuro nei dispositivi condivisi. Le chiavi di sicurezza FIDO2 consentono contengono le credenziali con l'utente e in modo sicuro l'autenticazione a un [AD Azure](https://aka.ms/azuread418)-unite in join i dispositivi Windows 10 che fa parte della propria organizzazione.

WebAuthN definisce un'API che consente di sviluppo e implementazione di forte, l'autenticazione senza password da servizi e App web. Il protocollo CTAP consente a dispositivi esterni, ad esempio le chiavi di sicurezza FIDO conforme a lavorare con WebAuthN e vengono usati come autenticatori. Con l'autenticazione Web, gli utenti possono accedere ai servizi online con i loro viso, tramite impronta digitale, PIN o le chiavi di sicurezza FIDO2 portabile, sfruttando le credenziali a chiave pubblica sicure anziché le password. Attualmente WebAuthN è supportata in Microsoft Edge e il supporto per Chrome e Firefox è in fase di sviluppo.

I dispositivi e i token che aderiscono ai protocolli FIDO2 WebAuthN e CTAP trasferire su una soluzione multi-piattaforma di autenticazione avanzata senza uso di password. I partner Microsoft stanno lavorando a un'ampia gamma di fattori di forma chiave di sicurezza, ad esempio le chiavi di sicurezza USB e abilitato per NFC smart card.

### <a name="fido2-security-keys-scenarios"></a>Scenari di chiavi di sicurezza FIDO2

Le chiavi di sicurezza FIDO2 utilizzabile per accedere ad Azure AD, scegliere la chiave di sicurezza come il provider di credenziali nella schermata di blocco di Windows 10. Un nome utente o password non è necessario che rende una soluzione ideale per i lavoratori riga prima che condividono i PC tra più utenti. Sono anche un'opzione di autenticazione eccellenti quando i criteri aziendali stabilisce che le credenziali dell'utente devono essere fisicamente separate dal proprio dispositivo. Gli utenti possono anche scegliere di accedere a siti web usando la propria chiave di sicurezza FIDO2 all'interno del browser Microsoft Edge in Windows 10, versione 1809 o versione successiva.

### <a name="fido2-security-keys-deployment-considerations"></a>Considerazioni sulla distribuzione delle chiavi di sicurezza FIDO2

Gli amministratori possono abilitare il supporto FIDO2 in Azure AD e assegnare la funzionalità per utenti o gruppi. I criteri possono essere creati anche per come chiavi vengono eseguito il provisioning e configurare le restrizioni, ad esempio per consentire o bloccare un set specifico di chiavi di protezione hardware. Le chiavi devono essere fisicamente distribuite agli utenti finali.

**I requisiti per abilitare l'accedono ad Azure AD e siti web utilizzando le chiavi di sicurezza FIDO2 includono quanto segue:**

* Azure AD

* Azure Multi-Factor Authentication

* Combinazione di anteprima di registrazione

* Anteprima della sicurezza chiave FIDO2 richiede una chiave di sicurezza FIDO2 compatibile

* L'autenticazione Web (WebAuthN) richiede Microsoft Edge in Windows 10 versione 1809 o versione successiva

* FIDO2 basati su Windows Accedi richiede Azure AD aggiunti a Windows 10, versione 1809 o versione successiva (è un'esperienza ottimale in Windows 10, versione 1903 o versioni successive)

Fattori di forma conformi FIDO2 includono dispositivi USB, NFC e Bluetooth. Si consiglia si sceglie il fattore di forma che soddisfa le esigenze specifiche perché alcune piattaforme e i browser non sono ancora conformi FIDO2.

È inoltre consigliabile che ogni organizzazione di creare un protocollo per gli utenti e gli amministratori devono una protezione chiave venga perso o rubato. Gli utenti devono segnalare la chiave smarrita o rubata in modo che gli amministratori o l'utente può eliminare le chiavi di sicurezza dal profilo dell'utente e il provisioning di una nuova.

### <a name="how-fido2-security-keys-works"></a>Funzionamento delle chiavi di sicurezza FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>L'utente imposta la chiave di sicurezza FIDO2

Mentre gli amministratori possono [manualmente il provisioning delle chiavi](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) e distribuirli agli utenti finali, il provisioning e abilitare il provider di credenziali FIDO2 nella schermata di blocco di Windows 10 sarà supportate tramite [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Gli amministratori dovranno anche usare il [portale di Azure](https://portal.azure.com/) per consentire ai dispositivi di token hardware come metodo di autenticazione senza password.

Distribuzione delle chiavi di sicurezza FIDO2 richiede anche che gli utenti registrano le chiavi usando [combinati registrazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Con la registrazione combinata, gli utenti registrare una sola volta e sfrutta i vantaggi di Azure multi-Factor Authentication e reimpostazione della password single sign-on (SSPR).

Oltre a selezionare il token hardware come metodo predefinito per l'autenticazione a più fattori, è consigliabile selezionare anche un'opzione di verifica aggiuntiva.

* Microsoft Authenticator - notifica

* App Authenticator o all'hardware del token, ovvero del codice

* Chiamata telefonica

* SMS

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Usando la chiave di sicurezza FIDO2 per l'accesso utente

FIDO2 fornisce un livello di astrazione tra il fattore di forma viene usato come l'autenticazione e crittografia a chiave pubblica/privata per abilitare gli autenticatori di piattaforma predefinite, ad esempio le chiavi di Windows Hello e sicurezza risolvere una chiave privata e fornire una chiave pubblica accedere a risorse esterne, che può essere utilizzato come identificatore. Le chiavi di sicurezza FIDO2 sono dotate i propri incorporati enclave protetta che archivia la chiave privata e la biometria o PIN per sbloccarlo, è necessario. Credenziali non possono essere riutilizzate, riprodotta, o condivisi tra servizi e non sono soggetti ad attacchi MiTM e phishing o un server violazioni della sicurezza.

![Accedi FIDO2](./media/azure-ad/azure-ad-pwdless-image6.png)

Le chiavi di sicurezza FIDO2 forniscono l'autenticazione sicura, indipendentemente dal fattore di forma. La chiave di sicurezza contiene le credenziali e deve essere protetto con un secondo fattore, ad esempio un'impronta digitale (integrate la chiave di sicurezza) o un PIN da immettere in sign-in di Windows aggiuntivo. I partner Microsoft stanno lavorando a un'ampia gamma di fattori di forma di chiave di sicurezza. Alcuni esempi includono le chiavi di sicurezza USB e NFC abilitato le smart card.

> [!NOTE]
> Una chiave di sicurezza deve implementare determinate caratteristiche e le estensioni del protocollo FIDO2 CTAP sia [compatibile con Microsoft](https://aka.ms/fido2securitykeys). Microsoft ha testato queste soluzioni per la compatibilità con Windows 10 e Azure Active Directory.

![Processo di accesso FIDO2](./media/azure-ad/azure-ad-pwdless-image9.png)

1. L'utente viene inserito il dispositivo FIDO2 nel computer.

2. Windows rileva la chiave di sicurezza FIDO2.

3. Windows invia una richiesta di autenticazione.

4. Azure AD invia un nonce.

5. L'utente completa i movimenti per sbloccare la chiave privata archiviata in enclave protetta della chiave di sicurezza FIDO2.

6. La chiave di sicurezza FIDO2 firma il parametro nonce con la chiave privata.

7. La richiesta di token PRT con nonce con segno viene inviata ad Azure AD.

8. Azure AD verifica il parametro nonce firmato usando la chiave pubblica FIDO2.

9. Azure AD restituisce PRT per abilitare l'accesso alle risorse locali.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Utente gestisce le credenziali chiave di sicurezza FIDO2

Come per l'app Microsoft Authenticator, gestione delle credenziali per le chiavi di sicurezza FIDO2 si basa sull'esperienza di registrazione combinato per gli utenti finali.

## <a name="deciding-a-passwordless-method"></a>Scelta di un metodo senza password

La scelta tra queste tre opzioni senza password dipende dalla sicurezza dell'azienda, piattaforma e requisiti per le app.

Ecco alcuni fattori da prendere in considerazione quando si sceglie la tecnologia Microsoft senza password:

||**Windows Hello for Business** (Configurare Windows Hello for Business)|**Accedi senza password con l'app Microsoft Authenticator**|**Chiavi di sicurezza FIDO2**|
|:-|:-|:-|:-|
|**Prerequisito**| Windows 10, versione 1809 o versione successiva<br>Azure Active Directory| App Microsoft Authenticator<br>Telefono (iOS e dispositivi Android che eseguono Android 6.0 o versioni successive.)|Windows 10, versione 1809 o versione successiva<br>Azure Active Directory|
|**Modalità**|Piattaforma|Software|Hardware|
|**I sistemi e dispositivi**|PC con un predefinite della piattaforma modulo TPM (Trusted)<br>Riconoscimento PIN e la biometria |Riconoscimento PIN e dati biometrici sul telefono|Dispositivi di sicurezza FIDO2 sono compatibile di Microsoft|
|**Esperienza utente**|Accedere usando un PIN o il riconoscimento biometrico (facciale, iris o impronte digitali) con i dispositivi Windows.<br>L'autenticazione Windows Hello è legata al dispositivo; l'utente deve sia al dispositivo un componente di accesso, ad esempio un PIN o fattore biometrici per accedere alle risorse aziendali.|Accedere usando un telefono cellulare con il riconoscimento di analisi, facciale o iris impronte digitali, o aggiungere.<br>Gli utenti accedere account aziendale o personale dalla loro PC o telefono cellulare.|Accedere usando FIDO2 dispositivo di sicurezza (rilevamento biometrico, PIN e NFC)<br>Utente può accedere ai dispositivi basati su controlli di organizzazione ed eseguire l'autenticazione PIN base, biometria usando i dispositivi, ad esempio le chiavi di sicurezza USB e smartcard abilitato per NFC, chiavi o dispositivi indossabili.|
|**Scenari abilitati**| Esperienza senza password con il dispositivo Windows.<br>Applicabile per PC di lavoro dedicato con capacità per il single sign-on a dispositivi e applicazioni.|Senza password in un punto qualsiasi soluzione tramite telefono cellulare.<br>Applicabile per l'accesso alle applicazioni personali sul web o lavoro da qualsiasi dispositivo.|Esperienza senza password per i ruoli di lavoro usando la biometrica, PIN e NFC.<br>Applicabile per i PC condivisi e in cui un telefono cellulare non è un'opzione praticabile (quali personale dell'help desk, chiosco pubblico o ospedale team)|

Usare la tabella seguente per scegliere quale metodo supporteranno i requisiti e gli utenti.

|Utente tipo|Scenario|Environment|Tecnologia senza password|
|:-|:-|:-|:-|
|**Admin**|Proteggere l'accesso a un dispositivo per le attività di gestione|Dispositivo Windows 10 assegnato|Windows Hello for Business e/o FIDO2 chiave di sicurezza|
|**Admin**|Attività di gestione nei dispositivi non Windows| Dispositivo mobile o non windows|Accedi senza password con l'app Microsoft Authenticator|
|**Operatori dei sistemi informativi**|Lavoro produttività|Dispositivo Windows 10 assegnato|Windows Hello for Business e/o FIDO2 chiave di sicurezza|
|**Operatori dei sistemi informativi**|Lavoro produttività| Dispositivo mobile o non windows|Accedi senza password con l'app Microsoft Authenticator|
|**Ruolo di lavoro nel sistema dalla**|Chioschi multimediali in una voce di factory, stabilimento, delle vendite al dettaglio o dei dati|Dispositivi condivisi di Windows 10|Chiavi di sicurezza FIDO2|

## <a name="getting-started"></a>Introduzione

L'autenticazione senza password è la tendenza del futuro e il percorso di un ambiente più sicuro. È consigliabile che le organizzazioni iniziano a pianificare la modifica e riducendo le relative dipendenze nella password. Per iniziare, considerare gli obiettivi seguenti:

* Abilitare gli utenti per l'accesso condizionale e app Microsoft Authenticator + autenticazione a più fattori.

* Protezione di Password di Azure AD di implementazione + criteri di aggiornamento.

* Abilitare gli utenti per SSPR con la registrazione combinata.

* Distribuire l'App Microsoft Authenticator per la mobilità.

* Distribuzione di Windows Hello for Business (1903: passo coi tempi).

* Distribuire dispositivi FIDO2 per gli utenti che non è possibile utilizzare il telefono.

* Quando possibile, disabilitare l'autenticazione basata su password.

Per raggiungere questi obiettivi, è consigliabile l'approccio seguente:

1. Abilitare Azure Active Directory per sfruttare appieno le funzionalità, ad esempio Azure MFA e accesso condizionale.

2. Abilitare multi-factor authentication fornire protezione aggiuntiva.

3. Abilitare la reimpostazione della Password Self-Service nel caso in cui gli utenti devono eseguire il fallback usare una password.

4. Distribuire Microsoft Authenticator phone Accedi per la mobilità aggiuntiva.

5. Distribuire Windows Hello for Business a tutti i dispositivi Windows 10.

6. Preparazione per le chiavi di sicurezza FIDO2.

> [!NOTE]
> Fare riferimento ad Azure Active Directory [pagina relativa alla licenza](https://azure.microsoft.com/pricing/details/active-directory/) per informazioni dettagliate sui requisiti di licenza per i metodi senza password.

## <a name="conclusion"></a>Conclusione

Negli ultimi anni, Microsoft ha continuato l'impegno per l'abilitazione di un mondo senza password. Con Windows 10, Microsoft ha introdotto Windows Hello for Business, con un nome sicuro, hardware protetto credenziale a due fattori che abilita single sign-on di Azure Active Directory e Active Directory. Come per la tecnologia di Windows Hello for Business, l'app Microsoft Authenticator Usa l'autenticazione basata su chiave per consentire una credenziale utente che è associata a un dispositivo mobile e Usa un elemento biometrico o PIN. Ora le chiavi di sicurezza FIDO2 consentono contengono le credenziali con l'utente ed eseguire l'accesso ad Azure AD, scegliere la chiave di sicurezza come il provider di credenziali nella schermata di blocco di Windows 10. Tutte e tre queste soluzioni senza password ridurre il rischio di phishing, spray password e attacchi di tipo replay e fornire agli utenti un modo pratico e sicuro per accedere e accedere ai dati da qualsiasi posizione.

L'adozione delle tecnologie moderne multi-factor authentication come letture biometriche e crittografia a chiave pubblica in dispositivi accessibili ampiamente è uno dei passaggi che consentono di ridurre in modo significativo i rischi di identità dell'azienda con impatto più elevati. Senza password è un approccio a lungo termine per l'autenticazione sicura e è in continua evoluzione. Dato nuovi requisiti emergenti, le organizzazioni possono prepararsi, rendendo un piano per avviare lo spostamento alle tecnologie senza password.

## <a name="next-steps"></a>Passaggi successivi

* Cenni preliminari [What ' s senza password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Come abilitare senza password in Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
