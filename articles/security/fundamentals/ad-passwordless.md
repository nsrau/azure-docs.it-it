---
title: Informazioni su un mondo senza password con Azure Active Directory | Microsoft Docs
description: Questa guida aiuta i CEO, cio, CISO, Chief Identity Architects, Enterprise Architects e Security and IT Decision Maker responsabili della scelta di un metodo di autenticazione con password per l'implementazione del Azure Active Directory.
services: active-directory
keywords: con password, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 09a3c14734d86738a521cf765fcfac2880eee177
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68616188"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Un mondo senza password con Azure Active Directory

È giunto il momento di suddividere la relazione con le password. Le password sono state utili in passato, ma nell'area di lavoro digitale odierna sono diventati un vettore di attacco relativamente semplice per i pirati informatici. Gli hacker amano le password e non è difficile capire perché, quando si considera che le password più diffuse in Azure Active Directory (Azure AD) includono termini come l'anno, il mese, la stagione o un team sportivo locale. Inoltre, la [ricerca ha dimostrato](https://aka.ms/passwordguidance) che i consigli tradizionali per la gestione delle password, ad esempio i requisiti di lunghezza, i requisiti di complessità e le frequenze di modifica, sono controproducenti per diversi motivi correlati alla natura umana.

Tre tipi di attacchi comunemente usati per compromettere gli account utente sono spray, phishing e violazione della password. Azure AD funzionalità come il [blocco intelligente](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), le [password escluse](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)e la [protezione con password](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) possono contribuire alla protezione da questi tipi di attacchi. Analogamente, [l'](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) implementazione dell'autenticazione a più fattori o la verifica in due passaggi garantisce una maggiore sicurezza richiedendo una seconda forma di autenticazione. Tuttavia, a lungo termine, una soluzione senza password è la soluzione migliore per garantire il metodo di autenticazione più sicuro.

Questo articolo è l'inizio del viaggio per facilitare la comprensione e l'implementazione delle soluzioni di Microsoft per la password e consente di scegliere tra una o più delle opzioni seguenti:

* **Windows Hello for business**. In Windows 10, Windows Hello for business sostituisce le password con l'autenticazione a due fattori avanzata nei PC e nei dispositivi mobili. Questo processo di autenticazione è costituito da un nuovo tipo di credenziale utente, che è associata a un dispositivo e usa un sensore biometrico o un PIN.

* **Accesso senza password con Microsoft Authenticator**. L'app Microsoft Authenticator può essere usata per accedere a un account Azure AD senza usare una password. Analogamente alla tecnologia di Windows Hello for business, il Microsoft Authenticator usa l'autenticazione basata su chiavi per abilitare le credenziali utente associate a un dispositivo e usa una metrica o un PIN.

* **Chiavi di sicurezza FIDO2**. FIDO2 fornisce credenziali di accesso crittografiche univoche in ogni sito Web e vengono archiviate in un dispositivo locale come Windows Hello o chiavi di sicurezza esterne. Queste chiavi di sicurezza sono resistenti ai rischi di phishing, furto di password e attacchi di riproduzione. Insieme alla verifica dell'utente tramite biometria o PIN, la soluzione è la verifica a due fattori che soddisfa le esigenze di sicurezza moderne.

## <a name="the-future-of-passwordless-authentication"></a>Il futuro dell'autenticazione con password

In questi giorni, banche, società di carte di credito e altre organizzazioni e Servizi online spesso proteggono l'account richiedendo la verifica dell'identità due volte: una volta usando la password, quindi di nuovo tramite telefono, testo o un'app. Mentre l'autenticazione a più fattori risolve il problema di sicurezza delle password condivise, rubate o indovinate, non risolve il fattore inconveniente di provare a ricordarli. Ciò che gli utenti e le organizzazioni desiderano nell'era del cloud odierno sono metodi di autenticazione senza password altamente sicuri *e* convenienti.

![Praticità rispetto alla sicurezza](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello for business, l'accesso senza password con Microsoft Authenticator e le chiavi di sicurezza FIDO2 condividono un'architettura semplice e comune che fornisce agli utenti un metodo di autenticazione altamente sicuro e comodo da usare. Tutte e tre sono basate sulla tecnologia a chiave pubblica/privata, richiedono un gesto locale, ad esempio una biometrica o un PIN, e le chiavi private associate a un singolo dispositivo e archiviate in modo sicuro e mai condivise.

## <a name="windows-hello-for-business"></a>Windows Hello for Business

In Windows 10, Windows Hello for business sostituisce le password con l'autenticazione a due fattori avanzata nei PC e nei dispositivi. L'autenticazione è costituita da un nuovo tipo di credenziale utente associato a un dispositivo e usa un movimento biometrico o un PIN che consente agli utenti di eseguire l'autenticazione a Azure AD nonché a una Active Directory locale. È semplice accedere a un dispositivo usando Windows Hello for business. È possibile usare un PIN o un movimento biometrico, ad esempio un'impronta digitale o un riconoscimento facciale.

### <a name="windows-hello-for-business-scenarios"></a>Scenari di Windows Hello for business

Windows Hello for business è ideale per gli Information Worker che dispongono di un proprio PC Windows designato. La biometrica e le credenziali sono direttamente collegate al PC dell'utente, che impedisce l'accesso a utenti diversi dal proprietario. Con l'integrazione dell'infrastruttura a chiave pubblica e il supporto integrato per Single Sign-on (SSO), Windows Hello for business offre un metodo semplice e pratico per accedere facilmente alle risorse aziendali in locale e nel cloud.

### <a name="windows-hello-for-business-deployment-considerations"></a>Considerazioni sulla distribuzione di Windows Hello for business

Windows Hello for business è un sistema distribuito che usa diversi componenti per eseguire la registrazione, il provisioning e l'autenticazione del dispositivo. Pertanto, la distribuzione richiede una pianificazione corretta tra più team all'interno dell'organizzazione. La [Guida alla pianificazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) di Windows Hello for business può essere usata per prendere decisioni sul tipo di distribuzione di Windows Hello for business e sulle opzioni che è necessario prendere in considerazione.

Sono disponibili molte opzioni tra cui è possibile scegliere quando si distribuisce Windows Hello for business. Fornire più opzioni garantisce che quasi tutte le organizzazioni possano distribuire Windows Hello for business. Considerare i seguenti tipi di distribuzioni supportate:

* [Distribuzione della chiave Azure AD ibrido unita in join](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Distribuzione del trust tra certificati Azure AD ibrido](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Guida alla distribuzione di Azure AD join Single Sign-on](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Distribuzione di attendibilità della chiave locale](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Distribuzione Trust certificati locale](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Fornire molte opzioni rende la distribuzione più complessa. Tuttavia, la maggior parte delle organizzazioni determinerà probabilmente che ha già implementato la maggior parte dell'infrastruttura da cui dipende la distribuzione di Windows Hello for business. Indipendentemente da, è importante comprendere che Windows Hello for business è un sistema distribuito e che è consigliabile pianificare una pianificazione adeguata.

Si consiglia di leggere la [pianificazione di una distribuzione di Windows Hello for business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) per scegliere il modello di distribuzione più adatto per la propria organizzazione. Quindi, in base alla pianificazione effettuata, fare riferimento alla [Guida alla distribuzione di Windows Hello for business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) per garantire una corretta distribuzione di Windows Hello for business nell'ambiente esistente.

### <a name="how-windows-hello-for-business-works"></a>Come funziona Windows Hello for Business

#### <a name="user-sets-up-windows-hello-for-business"></a>L'utente configura Windows Hello for business

Dopo una verifica iniziale in due passaggi dell'utente durante la registrazione, Windows Hello viene configurato sul dispositivo dell'utente e Windows chiede all'utente di impostare un movimento, che può essere una biometrica, ad esempio un'impronta digitale o un riconoscimento facciale, o un PIN. Una volta impostato, l'utente fornisce il gesto per verificarne l'identità. Windows usa quindi Windows Hello per autenticare gli utenti.

In base alle funzionalità del dispositivo Windows 10, sarà disponibile un'enclave protetta incorporata, nota come TPM (hardware Trusted Platform Module) o TPM Software. Il TPM archivia la chiave privata, che richiede la faccia, l'impronta digitale o il PIN per sbloccarlo. I dati biometrici non vengono spostati e non vengono mai inviati a dispositivi o server esterni. Non esiste un singolo punto di raccolta che un utente malintenzionato può compromettere per sottrarre i dati biometrici, perché Windows Hello archivia solo i dati di identificazione biometrici nel dispositivo.

> [!TIP]
> In apparenza, un PIN sembra una password, ma è effettivamente più sicuro. Una differenza importante tra una password e un PIN è che il PIN è collegato al dispositivo specifico in cui è stato configurato. Un utente che ruba la password può accedere all'account ovunque ci si trovi. Ma se rubano il PIN, è necessario che rubi anche il dispositivo fisico. Inoltre, poiché un PIN è locale rispetto al dispositivo, non viene trasmesso in nessun punto, quindi non può essere intercettato durante la trasmissione o il furto da un server.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Utente che usa Windows Hello for business per l'accesso

La biometria e i pin di Windows Hello for business utilizzano la crittografia asimmetrica (chiave pubblica/privata) per l'autenticazione. Durante l'autenticazione, la crittografia è associata alla chiave della sessione TLS, che protegge il processo di autenticazione in modo che un attacco man-in-the-Middle (MiTM) non possa rubare il token di sicurezza o l'artefatto risultante e riprodurlo da altre posizioni.

Windows Hello for business offre un'esperienza di accesso ottimale che autentica l'utente per Azure AD e Active Directory risorse. I dispositivi aggiunti a Azure AD eseguono l'autenticazione in Azure durante l'accesso e possono facoltativamente eseguire l'autenticazione a Active Directory. I dispositivi ibridi Azure Active Directory aggiunti si autenticano per Active Directory durante l'accesso ed eseguono l'autenticazione per Azure Active Directory in background.

![Vedere l'immagine di origine](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Nei passaggi seguenti viene illustrata l'autenticazione di accesso per Azure Active Directory.

![Schermata di blocco di Windows 10](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. L'utente accede a Windows usando biometrico o un movimento PIN. Il movimento Sblocca la chiave privata di Windows Hello for business e viene inviata all'Security Support Provider di autenticazione cloud, denominata provider di app cloud.

2. Il provider AP cloud richiede un parametro nonce da Azure Active Directory.

3. Azure AD restituisce un parametro nonce valido per 5 minuti.

4. Il provider AP cloud firma il parametro nonce usando la chiave privata dell'utente e restituisce il parametro nonce firmato al Azure Active Directory.

5. Azure Active Directory convalida il parametro nonce firmato usando la chiave pubblica registrata in modo sicuro dell'utente sulla firma nonce. Dopo la convalida della firma, Azure AD convalida il parametro nonce firmato restituito. Dopo aver convalidato il parametro nonce, Azure AD crea un PRT con la chiave della sessione crittografata con la chiave di trasporto del dispositivo e la restituisce al provider del punto di accesso cloud.

6. Il provider AP cloud riceve il PRT crittografato con la chiave della sessione. Usando la chiave di trasporto privata del dispositivo, il provider AP cloud decrittografa la chiave della sessione e protegge la chiave della sessione usando il TPM del dispositivo.

7. Il provider AP cloud restituisce una risposta di autenticazione riuscita a Windows, dopo la quale l'utente è in grado di accedere a Windows e alle applicazioni cloud e locali senza la necessità di eseguire di nuovo l'autenticazione (SSO).

Per approfondire il processo di autenticazione in altri scenari che coinvolgono Windows Hello for business, vedere [Windows Hello for business e autenticazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>L'utente gestisce le credenziali di Windows Hello for business

Il [servizio di reimpostazione PIN Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) è una funzionalità di Azure ad che consente agli utenti di reimpostare il PIN, se necessario. Utilizzando criteri di gruppo, Microsoft Intune o una soluzione MDM compatibile, un amministratore può configurare i dispositivi Windows 10 per utilizzare in modo sicuro il servizio di reimpostazione PIN Microsoft che consente agli utenti di reimpostare il PIN dimenticato tramite le impostazioni o sopra la schermata di blocco senza richiedere Ripetere la registrazione.

A volte gli utenti devono eseguire il fallback all'uso delle password. [Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) è un'altra Azure AD funzionalità che consente agli utenti di reimpostare le proprie password senza dover contattare il personale IT. Prima di usare il servizio, gli utenti devono registrarsi per la reimpostazione della password self-service o essere registrati. Durante la registrazione, l'utente sceglie uno o più metodi di autenticazione abilitati dall'organizzazione. SSPR consente agli utenti di essere sbloccati rapidamente e continuare a lavorare indipendentemente da dove si trovano o dall'ora del giorno. Consentendo agli utenti di sbloccare autonomamente, l'organizzazione può ridurre il tempo non produttivo e i costi di supporto elevati per la maggior parte dei problemi comuni relativi alle password.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Accesso senza password con Microsoft Authenticator

L'accesso senza password con Microsoft Authenticator è un'altra soluzione senza password che può essere usata per accedere agli account di Azure AD tramite l'accesso tramite telefono. È comunque necessario verificare la propria identità fornendo un elemento che si conosce e un altro utente, ma l'accesso tramite telefono consente di ignorare l'immissione della password ed esegue tutte le verifiche di identità sul dispositivo mobile usando impronta digitale, viso o PIN.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator scenari con password

L'app Microsoft Authenticator consente agli utenti di verificare la propria identità ed eseguire l'autenticazione con l'account aziendale o personale. Può anche essere usato per aumentare una password con un codice di accesso monouso o una notifica push oppure sostituire completamente la necessità di una password. Invece di usare una password, gli utenti confermano la propria identità usando il telefono cellulare tramite l'analisi delle impronte digitali, il riconoscimento facciale o Iris o il PIN. Basato su una tecnologia sicura simile a quella utilizzata da Windows Hello, questo strumento viene inserito in un pacchetto in una semplice app in un dispositivo mobile, rendendola un'opzione utile per gli utenti. L'app Microsoft Authenticator è disponibile per Android e iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Considerazioni sulla distribuzione di Microsoft Authenticator

I prerequisiti per l'uso dell'app Microsoft Authenticator per eseguire l'accesso senza password ai Azure AD includono quanto segue:

* Gli utenti finali sono abilitati per l'autenticazione a più fattori di Azure

* La possibilità per gli utenti di registrare i propri dispositivi usando Microsoft Intune o una soluzione di gestione di dispositivi mobili (MDM) di terze parti

Supponendo che questi requisiti siano soddisfatti, gli amministratori possono abilitare l'accesso tramite telefono senza password nel tenant usando [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Quando l'accesso tramite telefono è abilitato nel tenant, gli utenti finali possono scegliere di effettuare l'accesso usando il telefono selezionando il proprio account aziendale o dell'Istituto di istruzione nella schermata **account** dell'app, quindi selezionando **Abilita l'accesso tramite telefono**.

Supponendo che l'accesso senza password sia abilitato da un amministratore, gli utenti finali dovranno soddisfare i requisiti seguenti:

* Registrato in Azure Multifactor Authentication

* La versione più recente di Microsoft Authenticator installata nei dispositivi che eseguono iOS 8,0 o versione successiva o Android 6,0 o versione successiva

* Account aziendale o dell'Istituto di istruzione con notifiche push aggiunte all'app

Per evitare la possibilità di bloccare l'account o di ricreare gli account in un nuovo dispositivo, si consiglia di usare Microsoft Authenticator per eseguire il [backup delle credenziali dell'account](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) nel cloud. Al termine del backup, è anche possibile usare l'app per ripristinare le informazioni in un nuovo dispositivo, evitando potenzialmente di rimanere bloccati senza accesso al dispositivo o di dover ricreare gli account.

Poiché la maggior parte degli utenti è abituata a usare solo password per l'autenticazione, è importante che l'organizzazione Istruisca gli utenti in merito a questo processo. La consapevolezza può ridurre la probabilità che gli utenti chiamino il help desk per eventuali [problemi](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) correlati all'accesso con l'app Microsoft Authenticator.

> [!NOTE]
> Un potenziale punto di errore per questa soluzione è quando un utente comune si trova in una posizione in cui non è disponibile la connettività Internet. Le chiavi di sicurezza di FIDO2 e Windows Hello for business non sono soggette alle stesse limitazioni.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Funzionamento dell'accesso senza password con Microsoft Authenticator

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>L'utente configura l'accesso senza password con Microsoft Authenticator

Prima che l'app Microsoft Authenticator possa essere usata come soluzione senza password per accedere a un account Azure AD, i passaggi devono essere eseguiti sia da un amministratore che dagli utenti finali.

Per prima cosa, un amministratore deve [abilitare l'uso dell'app come credenziale](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) nel tenant usando Windows PowerShell. L'amministratore dovrà anche abilitare gli utenti finali per Azure multi-factor authentication (autenticazione a più fattori di Azure) e configurare l'app Microsoft Authenticator come uno dei [metodi di verifica](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Gli utenti finali dovranno [scaricare e installare](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) l'app Microsoft Authenticator e [configurare il proprio account](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) per usare l'app Microsoft Authenticator come uno dei metodi di verifica.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Utente che usa Microsoft Authenticator per l'accesso senza password

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Sebbene la schermata di blocco di Windows 10 non includa l'app Microsoft Authenticator come opzione di accesso, gli utenti possono ancora immettere il proprio nome utente e quindi ricevere una notifica push sul dispositivo mobile per verificare la presenza. Gli utenti confermano la loro presenza abbinando un numero nella schermata di accesso, quindi fornendo un'analisi dei volti, un'impronta digitale o un PIN per sbloccare la chiave privata e completare l'autenticazione. Questo metodo di verifica a più fattori è più sicuro di una password e più comodo rispetto all'immissione di una password e di un codice.

![Accesso dell'autenticatore](./media/ad-passwordless/azure-ad-pwdless-image4.png)

L'autenticazione senza password con Microsoft Authenticator segue lo stesso modello di base di Windows Hello for business, ma è un po' più complicato poiché l'utente deve essere identificato in modo che Azure AD possa trovare la versione dell'app Microsoft Authenticator utilizzato.

![Processo di autenticazione](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. L'utente immette il proprio nome utente.

2. Azure AD rileva che l'utente dispone di una credenziale complessa e avvia il flusso di credenziali complesse.

3. La notifica viene inviata all'app tramite Apple Push Notification Service (APNS) nei dispositivi iOS o tramite Firebase Cloud Messaging (FCM) su dispositivi Android.

4. L'utente riceve la notifica push e apre l'app.

5. L'app chiama Azure AD e riceve una richiesta di verifica della presenza e un parametro nonce.

6. L'utente completa la richiesta immettendo la relativa biometrica o il PIN per sbloccare la chiave privata.

7. Il parametro nonce viene firmato con la chiave privata e restituito a Azure AD.

8. Azure AD esegue la convalida della chiave pubblica/privata e restituisce un token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>L'utente gestisce l'accesso senza password con Microsoft Authenticator credenziali

Con la [registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), gli utenti possono registrarsi e sfruttare i vantaggi di Azure Multifactor Authentication e della reimpostazione della password self-service. Gli utenti registrano e gestiscono queste impostazioni passando alla [pagina del profilo personale](https://aka.ms/mysecurityinfo). Oltre ad abilitare SSPR, la registrazione combinata supporta più metodi di autenticazione e azioni.

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

FIDO2 è la versione più recente di FIDO Alliance standard ed è costituita da due componenti: W3C Web Authentication (webauthn) standard e il corrispondente protocollo FIDO Alliance client-to-Authenticator (CTAP2). Gli standard FIDO2 consentono agli utenti di sfruttare gli autenticatori basati su hardware, dispositivi mobili e biometria per eseguire facilmente l'autenticazione con molte app e siti Web in ambienti desktop e per dispositivi mobili.

Microsoft e i partner del settore collaborano con i dispositivi di sicurezza FIDO2 per Windows Hello per consentire l'autenticazione semplice e sicura sui dispositivi condivisi. Le chiavi di sicurezza di FIDO2 consentono di portare le proprie credenziali con l'utente e di eseguire l'autenticazione in un dispositivo Windows 10 aggiunto [Azure ad](https://aka.ms/azuread418)che fa parte dell'organizzazione.

Webauthn definisce un'API che consente lo sviluppo e l'implementazione di un'autenticazione avanzata con password da parte di app e servizi Web. Il protocollo CTAP Abilita i dispositivi esterni, ad esempio le chiavi di sicurezza conformi a FIDO, per lavorare con webauthn e fungere da autenticatori. Con l'autenticazione Web, gli utenti possono accedere a Servizi online con le chiavi di sicurezza FIDO2, con impronta digitale, PIN o portabile, sfruttando le credenziali di chiave pubblica complesse anziché le password. Attualmente webauthn è supportato in Microsoft Edge e il supporto per Chrome e Firefox è in fase di sviluppo.

I dispositivi e i token che rispettano i protocolli FIDO2, webauthn e CTAP introducono una soluzione multipiattaforma di autenticazione avanzata senza usare le password. I partner Microsoft stanno lavorando a diversi fattori di forma chiave di sicurezza, ad esempio chiavi di sicurezza USB e smart card abilitate per NFC.

### <a name="fido2-security-keys-scenarios"></a>Scenari di chiavi di sicurezza FIDO2

È possibile usare le chiavi di sicurezza di FIDO2 per accedere a Azure AD scegliendo la chiave di sicurezza come provider di credenziali nella schermata di blocco di Windows 10. Non è necessario specificare un nome utente o una password che lo rende una soluzione ideale per i ruoli di lavoro di prima linea che condividono PC tra più utenti. Sono inoltre un'ottima opzione di autenticazione quando i criteri aziendali stabiliscono che le credenziali di un utente devono essere fisicamente separate dal dispositivo. Gli utenti possono anche scegliere di accedere ai siti Web usando la chiave di sicurezza FIDO2 all'interno del browser Microsoft Edge in Windows 10 versione 1809 o successiva.

### <a name="fido2-security-keys-deployment-considerations"></a>Considerazioni sulla distribuzione delle chiavi di sicurezza FIDO2

Gli amministratori possono abilitare il supporto FIDO2 in Azure AD e assegnare la funzionalità a utenti o gruppi. È anche possibile creare criteri per il provisioning delle chiavi e configurare le restrizioni, ad esempio consentire o bloccare un set specifico di chiavi di sicurezza hardware. Le chiavi devono essere distribuite fisicamente agli utenti finali.

**I requisiti per l'abilitazione dell'accesso senza password per Azure AD e i siti Web con le chiavi di sicurezza FIDO2 includono quanto segue:**

* Azure AD

* Azure Multi-Factor Authentication

* Anteprima della registrazione combinata

* L'anteprima della chiave di sicurezza FIDO2 richiede una chiave di sicurezza FIDO2 compatibile

* Autenticazione Web (webauthn) richiede Microsoft Edge in Windows 10 versione 1809 o successiva

* Per l'accesso a Windows basato su FIDO2 è necessario che Azure AD aggiunto Windows 10 versione 1809 o successiva (l'esperienza migliore è in Windows 10 versione 1903 o successiva)

I fattori di forma conformi a FIDO2 includono dispositivi USB, NFC e Bluetooth. Si consiglia di scegliere il fattore di forma che soddisfa le esigenze specifiche, perché alcune piattaforme e browser non sono ancora conformi a FIDO2.

Si consiglia inoltre di fare in modo che ogni organizzazione crei un protocollo per gli utenti e gli amministratori da seguire in caso di smarrimento o furto di una chiave di sicurezza. Gli utenti devono segnalare la chiave smarrita o rubata, in modo che gli amministratori o l'utente possano eliminare le chiavi di sicurezza dal profilo dell'utente ed effettuare il provisioning di un nuovo.

### <a name="how-fido2-security-keys-works"></a>Funzionamento delle chiavi di sicurezza FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>L'utente configura la chiave di sicurezza FIDO2

Sebbene gli amministratori possano eseguire manualmente il provisioning delle [chiavi](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) e distribuirle agli utenti finali, il provisioning e l'abilitazione del provider di credenziali FIDO2 nella schermata di blocco di Windows 10 saranno supportati tramite [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Gli amministratori dovranno anche usare il [portale di Azure](https://portal.azure.com/) per abilitare i dispositivi token hardware come metodo di autenticazione senza password.

Per la distribuzione di chiavi di sicurezza FIDO2 è inoltre necessario che gli utenti registrino le proprie chiavi utilizzando la [registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) Con la registrazione combinata, gli utenti registrano una sola volta e ottengono i vantaggi di Azure Multifactor Authentication e della reimpostazione della password Single Sign-on (SSPR).

Oltre a selezionare il token hardware come metodo di autenticazione a più fattori predefinito, è consigliabile selezionare anche un'opzione di verifica aggiuntiva.

* Microsoft Authenticator-notifica

* App Authenticator o token hardware--code

* Telefonata

* SMS

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Utente che usa la chiave di sicurezza FIDO2 per l'accesso

FIDO2 fornisce un livello di astrazione tra il fattore di forma usato come autenticatore e la crittografia a chiave pubblica/privata per abilitare gli autenticatori della piattaforma incorporati, ad esempio Windows Hello e le chiavi di sicurezza, per la risoluzione in una chiave privata e il recapito di una chiave pubblica che può essere usato come identificatore per accedere alle risorse esterne. Le chiavi di sicurezza di FIDO2 sono dotate di una propria enclave protetta incorporata in cui viene archiviata la chiave privata e che richiede la biometrica o il PIN per sbloccarlo. Le credenziali non possono essere riutilizzate, riprodotte o condivise tra i servizi e non sono soggette ad attacchi di phishing e MiTM o a violazioni del server.

![Accesso a FIDO2](./media/ad-passwordless/azure-ad-pwdless-image6.png)

Le chiavi di sicurezza di FIDO2 forniscono l'autenticazione sicura, indipendentemente dal fattore di forma. La chiave di sicurezza include le credenziali e deve essere protetta con un secondo fattore aggiuntivo, ad esempio un'impronta digitale (integrata nella chiave di sicurezza) o un PIN da immettere all'accesso di Windows. I partner Microsoft stanno lavorando a diversi fattori di forma chiave di sicurezza. Alcuni esempi includono chiavi di sicurezza USB e smart card abilitate per NFC.

> [!NOTE]
> Una chiave di sicurezza deve implementare determinate funzionalità ed estensioni dal protocollo CTAP di FIDO2 in modo che siano [compatibili con Microsoft](https://aka.ms/fido2securitykeys). Microsoft ha testato queste soluzioni per la compatibilità con Windows 10 e Azure Active Directory.

![Processo di accesso FIDO2](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. L'utente inserisce il dispositivo FIDO2 nel computer.

2. Windows rileva la chiave di sicurezza FIDO2.

3. Windows invia una richiesta di autenticazione.

4. Azure AD restituisce un parametro nonce.

5. L'utente completa il proprio movimento per sbloccare la chiave privata archiviata nell'enclave protetta della chiave di sicurezza FIDO2.

6. La chiave di sicurezza FIDO2 firma il parametro nonce con la chiave privata.

7. La richiesta di token PRT con parametro nonce firmato viene inviata a Azure AD.

8. Azure AD verifica il nonce firmato usando la chiave pubblica FIDO2.

9. Azure AD restituisce PRT per consentire l'accesso alle risorse locali.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>L'utente gestisce le credenziali della chiave di sicurezza FIDO2

Analogamente all'app Microsoft Authenticator, la gestione delle credenziali per le chiavi di sicurezza di FIDO2 si basa sull'esperienza di registrazione combinata per gli utenti finali.

## <a name="deciding-a-passwordless-method"></a>Decisione di un metodo non con password

La scelta tra queste tre opzioni con password dipende dai requisiti di sicurezza, piattaforma e app dell'azienda.

Di seguito sono riportati alcuni fattori da considerare quando si sceglie la tecnologia senza password Microsoft:

||**Windows Hello for Business** (Configurare Windows Hello for Business)|**Accesso senza password con l'app Microsoft Authenticator**|**Chiavi di sicurezza FIDO2**|
|:-|:-|:-|:-|
|**Prerequisiti**| Windows 10, versione 1809 o successiva<br>Azure Active Directory| App Microsoft Authenticator<br>Telefono (dispositivi iOS e Android che eseguono Android 6,0 o versione successiva).|Windows 10, versione 1809 o successiva<br>Azure Active Directory|
|**Modalità**|Piattaforma|Software|Hardware|
|**Sistemi e dispositivi**|PC con un Trusted Platform Module incorporato (TPM)<br>Riconoscimento del PIN e della biometria |Riconoscimento del PIN e della biometria sul telefono|Dispositivi di sicurezza FIDO2 compatibili con Microsoft|
|**Esperienza utente**|Accedere con un PIN o un riconoscimento biometrico (facciale, Iris o impronta digitale) con i dispositivi Windows.<br>L'autenticazione di Windows Hello è associata al dispositivo. per accedere alle risorse aziendali, l'utente deve disporre sia del dispositivo sia di un componente di accesso, ad esempio un PIN o un fattore biometrico.|Eseguire l'accesso con un telefono cellulare con impronta digitale, riconoscimento facciale o Iris oppure PIN.<br>Gli utenti possono accedere al proprio account di lavoro o personale dal PC o dal telefono cellulare.|Accedere con il dispositivo di sicurezza FIDO2 (biometria, PIN e NFC)<br>L'utente può accedere al dispositivo in base ai controlli dell'organizzazione ed eseguire l'autenticazione in base al PIN, alla biometria usando dispositivi quali chiavi di sicurezza USB e smart card, chiavi o indossabili abilitati per NFC.|
|**Scenari abilitati**| Esperienza senza password con il dispositivo Windows.<br>Applicabile per PC di lavoro dedicati con funzionalità per l'accesso Single Sign-on a dispositivi e applicazioni.|Soluzione senza password con il telefono cellulare.<br>Applicabile per accedere alle applicazioni aziendali o personali sul Web da qualsiasi dispositivo.|Esperienza senza password per i dipendenti che usano biometria, PIN e NFC.<br>Applicabile per i PC condivisi e in cui un telefono cellulare non è un'opzione valida, ad esempio per il personale help desk, il chiosco pubblico o il team ospedaliero|

Usare la tabella seguente per scegliere il metodo che supporterà i requisiti e gli utenti.

|Utente tipo|Scenario|Ambiente|Tecnologia con password|
|:-|:-|:-|:-|
|**Admin**|Proteggere l'accesso a un dispositivo per le attività di gestione|Dispositivo Windows 10 assegnato|Chiave di sicurezza di Windows Hello for business e/o FIDO2|
|**Admin**|Attività di gestione su dispositivi non Windows| Dispositivo mobile o non Windows|Accesso senza password con l'app Microsoft Authenticator|
|**Information Worker**|Lavoro di produttività|Dispositivo Windows 10 assegnato|Chiave di sicurezza di Windows Hello for business e/o FIDO2|
|**Information Worker**|Lavoro di produttività| Dispositivo mobile o non Windows|Accesso senza password con l'app Microsoft Authenticator|
|**Ruolo di lavoro Frontline**|Chioschi in una fabbrica, impianto, vendita al dettaglio o immissione di dati|Dispositivi Windows 10 condivisi|Chiavi di sicurezza FIDO2|

## <a name="getting-started"></a>Introduzione

L'autenticazione senza password è l'onda del futuro e il percorso di un ambiente più sicuro. È consigliabile che le organizzazioni inizino a pianificare questa modifica e ridurne le dipendenze sulle password. Per iniziare, considerare gli obiettivi seguenti:

* Abilitare gli utenti per l'app multi-factor authentication + Microsoft Authenticator e l'accesso condizionale.

* Implementazione Azure AD la protezione con password e criteri di aggiornamento.

* Abilitare gli utenti per SSPR con registrazione combinata.

* Distribuire Microsoft Authenticator app per la mobilità.

* Distribuire Windows Hello for business (1903: rimanere aggiornati).

* Distribuire i dispositivi FIDO2 per gli utenti che non possono usare telefoni.

* Quando possibile, disabilitare l'autenticazione basata su password.

Per raggiungere questi obiettivi, è consigliabile usare l'approccio seguente:

1. Abilitare Azure Active Directory per sfruttare appieno le funzionalità, ad esempio l'autenticazione a più fattori di Azure e l'accesso condizionale.

2. Abilitare la funzionalità di autenticazione a più fattori per fornire protezione aggiuntiva.

3. Abilitare la reimpostazione della password self-service nel caso in cui gli utenti debbano eseguire il fallback all'uso di una password.

4. Distribuire Microsoft Authenticator l'accesso tramite telefono per la mobilità aggiuntiva.

5. Distribuire Windows Hello for business in tutti i dispositivi Windows 10.

6. Preparare le chiavi di sicurezza FIDO2.

> [!NOTE]
> Per informazioni dettagliate sui requisiti di licenza per i metodi con password, vedere la pagina relativa alle [licenze](https://azure.microsoft.com/pricing/details/active-directory/) Azure Active Directory.

## <a name="conclusion"></a>Conclusione

Negli ultimi anni, Microsoft ha continuato a impegnarsi per l'abilitazione di un mondo senza password. Con Windows 10, Microsoft ha introdotto Windows Hello for business, una potente credenziale a due fattori protetta dall'hardware che consente l'accesso Single Sign-on per Azure Active Directory e Active Directory. Analogamente alla tecnologia di Windows Hello for business, l'app Microsoft Authenticator usa l'autenticazione basata su chiave per abilitare le credenziali utente associate a un dispositivo mobile e usa una metrica o un PIN. Ora le chiavi di sicurezza di FIDO2 consentono di portare le credenziali con l'utente e accedere a Azure AD scegliendo la chiave di sicurezza come provider di credenziali nella schermata di blocco di Windows 10. Tutte e tre le soluzioni senza password riducono il rischio di phishing, attacchi di tipo spray e riproduzione delle password e offrono agli utenti un modo estremamente sicuro e comodo per accedere ai dati ovunque ci si trovi.

L'adozione di tecnologie di autenticazione a più fattori moderne, ad esempio la biometria e la crittografia a chiave pubblica in dispositivi ampiamente accessibili, è uno dei passaggi più interessati che possono ridurre significativamente il rischio di identità di un'azienda. L'accesso senza password è un approccio a lungo termine per l'autenticazione sicura ed è in continua evoluzione. Considerati i requisiti emergenti, le organizzazioni possono prepararsi facendo un piano per iniziare a trasferire le tecnologie con password.

## <a name="next-steps"></a>Passaggi successivi

* Una panoramica delle informazioni sulle [password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Come abilitare la password in Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
