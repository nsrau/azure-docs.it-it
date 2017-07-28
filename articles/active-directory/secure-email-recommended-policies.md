---
title: Criteri consigliati per proteggere la posta elettronica di Azure AD | Microsoft Docs
description: Descrive i criteri per le raccomandazioni di Microsoft sull&quot;applicazione di criteri e configurazioni di posta elettronica.
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 1747ba5ff55780322eeaae5396099ec373289c98
ms.contentlocale: it-it
ms.lasthandoff: 06/08/2017


---

# <a name="recommended-policies"></a>Criteri consigliati
 
Questo articolo descrive i criteri consigliati per aiutare i clienti a proteggere la posta elettronica aziendale oltre all'autenticazione moderna e all'accesso condizionale di supporto per i client di posta elettronica. Vengono anche descritte le configurazioni del client per la piattaforma predefinita consigliate per offrire la migliore esperienza SSO agli utenti, nonché i prerequisiti tecnici per l'accesso condizionale.

## <a name="prerequisites"></a>Prerequisiti

Prima di implementare i criteri descritti nel seguito di questo documento, l'organizzazione deve soddisfare diversi prerequisiti:
* [Configurare le reti denominate](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal). Azure AD Identity Protection raccoglie e analizza tutti i dati della sessione disponibili per generare un punteggio di rischio. È consigliabile specificare gli intervalli di IP pubblici dell'organizzazione per la rete nella configurazione di reti denominata in Azure AD. Al traffico proveniente da questi intervalli verrà assegnato un punteggio di rischio ridotto, pertanto il traffico esterno all'ambiente aziendale viene considerato avente un punteggio di rischio elevato.
* [Registrare tutti gli utenti con l'autenticazione a più fattori, ovvero MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices). Azure AD Identity Protection usa Azure MFA per eseguire una verifica di sicurezza aggiuntiva. È consigliabile richiedere a tutti gli utenti di registrarsi in anticipo per Azure MFA.
* [Abilitare la registrazione automatica dei dispositivi per computer Windows aggiunti a un dominio](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). L'accesso condizionale deve verificare che il dispositivo che si connette al servizio sia un dispositivo conforme o aggiunto al dominio. A tale scopo nei computer Windows, il dispositivo deve essere registrato con Azure AD.  In questo articolo viene illustrato come configurare la registrazione automatica del dispositivo.  Si noti che AD FS è un requisito.
* **Preparare il team di supporto**. Attuare un piano per gli utenti che non possono completare l'autenticazione a più fattori. Questo è possibile aggiungendoli a un gruppo di esclusione dei criteri, o registrando nuove informazioni di autenticazione a più fattori. Prima di apportare una di queste modifiche importanti per la sicurezza, è necessario verificare che l'utente effettivo esegua la richiesta. Un passaggio importante consiste nel richiedere che i responsabili degli utenti contribuiscano all'approvazione.
* [Configurare il writeback delle password in AD locale](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). Il writeback delle password consente ad Azure AD di richiedere che gli utenti modifichino le password locali quando viene rilevato un account compromesso ad alto rischio. È possibile abilitare questa funzionalità con Azure AD Connect in due modi. nella schermata delle funzionalità facoltative della configurazione guidata di Azure AD Connect oppure tramite Windows PowerShell.  
* [Abilitare l'autenticazione moderna](https://support.office.com/en-us/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662) e [proteggere gli endpoint legacy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-supported-apps).  L'accesso condizionale è ideale sia per le applicazioni desktop che per i dispositivi mobili che usano un'autenticazione moderna. Se l'applicazione usa i protocolli di autenticazione legacy, può ottenere l'accesso nonostante le condizioni applicate. È importante sapere quali applicazioni possono usare le regole di accesso condizionale. È inoltre necessario conoscere i passaggi che sono necessari per la protezione di altri punti di ingresso dell'applicazione.
* [Abilitare Azure Information Protection](https://docs.microsoft.com/en-us/information-protection/get-started/infoprotect-tutorial-step1) attivando Rights Management. Usare Azure Information Protection con messaggio di posta elettronica per avviare la classificazione dei messaggi di posta elettronica. Seguire l'esercitazione di avvio rapido per personalizzare e pubblicare i criteri.  

### <a name="recommended-email-clients"></a>Client di posta elettronica consigliati
I seguenti client di posta elettronica supportano l'autenticazione moderna e l'accesso condizionale. Azure Information Protection non è ancora disponibile per tutti i client.

|Piattaforma|Client|Versione/Note|Azure Information Protection|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2013, 2016 [Abilitare l'autenticazione moderna]((https://support.office.com/en-us/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910))|Sì|
|**iOS**|Outlook|[Più recente](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|No|
|**Android**|Outlook|[Più recente](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|No|
|**macOS**|Supporto presto disponibile||No|
|**Linux**|Non supportate||No|

#### <a name="additional-client-software"></a>Software client aggiuntivo
Per accedere ai documenti protetti di Azure Information Protection, potrebbe essere necessario un software aggiuntivo. Assicurarsi di usare i [formati di software e documenti supportati](https://docs.microsoft.com/information-protection/get-started/requirements-applications) per creare e visualizzare i documenti protetti con Azure Information Protection.

## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>Configurazione client consigliata per SSO e accesso condizionale
Questa sezione descrive le configurazioni client per la piattaforma predefinita consigliate per offrire la migliore esperienza SSO agli utenti, nonché i prerequisiti tecnici per l'accesso condizionale.

### <a name="windows-devices"></a>Dispositivi Windows
Si consiglia Windows 10, versione 1703 o successiva, poiché Azure è progettato per offrire la migliore esperienza SSO possibile sia a livello locale che per Azure AD. I dispositivi emessi dall'azienda o dall'istituto di istruzione devono essere configurati per poter essere aggiunti direttamente ad Azure AD, oppure se l'organizzazione usa dispositivi aggiunti a un dominio AD locale, questi devono essere [configurati per la registrazione automatica e automatica con Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). 

Per i dispositivi Windows BYOD, gli utenti possono usare "Add work or school account" (Aggiungi account aziendale o dell'istituto di istruzione). Per i dispositivi Windows BYOD, gli utenti possono usare "Add work or school account" (Aggiungi account aziendale o dell'istituto di istruzione). Si noti che gli utenti del browser Chrome in Windows 10 devono [installare un'estensione](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog) di modo che possano ottenere un'esperienza di accesso senza problemi con quella di accesso a Edge o Internet Explorer. Se l'organizzazione dispone di dispositivi Windows 7 aggiunti a un dominio, è possibile anche installare Microsoft Workplace Join per il [pacchetto](https://www.microsoft.com/en-us/download/details.aspx?id=53554) per i computer che non hanno Windows 10 per registrare i dispositivi con Azure AD.

### <a name="ios-devices"></a>Dispositivi iOS

Si consiglia di installare l'[app Microsoft Authenticator](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) sui dispositivi dell'utente prima di distribuire i criteri di autenticazione a più fattori o di accesso condizionale. Come minimo, l'app deve essere installata quando agli utenti viene [richiesto di registrare il dispositivo](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time) con Azure AD mediante l'aggiunta di un account aziendale o dell'istituto di istruzione o quando si installa l'app del portale aziendale Intune per registrare il dispositivo nella gestione. Ciò dipende dal criterio di accesso condizionale configurato.

### <a name="android-devices"></a>Dispositivi Android

Si consiglia agli utenti di installare l'[app del portale aziendale Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
) e l'[app Authenticator Microsoft](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) prima che i criteri di accesso condizionale vengano distribuiti o quando richiesto durante determinati tentativi di autenticazione. Dopo l'installazione dell'app, è possibile che agli utenti venga richiesto di registrarsi con Azure AD o di registrare il proprio dispositivo con Intune. Ciò dipende dal criterio di accesso condizionale configurato. 

È anche consigliabile che i dispositivi aziendali vengano normalizzati negli OEM e nelle versioni che supportano Android for Work o Samsung Knox per consentire agli account di posta di essere gestiti e protetti dai criteri MDM di Intune.

## <a name="tiers-of-security-and-protection"></a>Livelli di sicurezza e protezione

La maggior parte delle organizzazioni dispone di requisiti specifici relativi alla sicurezza e alla protezione dei dati. Questi requisiti variano in base al segmento del settore e alle funzioni del processo all'interno delle organizzazioni. Ad esempio, l'ufficio legale e gli amministratori di Office 365 potrebbero richiedere una sicurezza aggiuntiva e controlli di protezione delle informazioni sulla corrispondenza tramite posta elettronica che non sono richiesti per altri utenti della business unit. 

Ogni settore ha anche il proprio set di normative specializzate. Anziché indicare un elenco di tutte le possibili opzioni di protezione o una raccomandazione per ogni funzione del processo o segmento di settore, questo articolo contiene raccomandazioni per 3 diversi livelli di sicurezza e protezione della posta elettronica che può essere applicata in base al livello di granularità necessario: [di base, sensibile e altamente regolamentato](https://go.microsoft.com/fwlink/p/?linkid=841656).  

**Di base**. È consigliabile stabilire uno standard minimo per la protezione dei dati, nonché per le identità e i dispositivi che accedono ai dati. È possibile seguire le raccomandazioni di base per garantire una protezione avanzata predefinita che soddisfi le esigenze di molte organizzazioni. 

**Sensibile**. Alcuni clienti hanno un subset di dati che devono essere protetti a livelli superiori o che richiedono la protezione di tutti i dati a livelli superiori. È possibile applicare una maggiore protezione per tutti o per set di dati specifici nell'ambiente di Office 365. Si consiglia di proteggere le identità e i dispositivi che accedono ai dati sensibili con livelli di sicurezza analoghi. 

**Altamente regolamentati**. Alcune organizzazioni potrebbero disporre di una quantità molto piccola di dati ovvero dati altamente classificati, segreti commerciali o regolamentati. Microsoft offre funzionalità per consentire alle organizzazioni di soddisfare questi requisiti, inclusa la protezione aggiuntiva per le identità e i dispositivi. 

### <a name="default-protection-mechanism-recommendations"></a>Raccomandazioni per il meccanismo di protezione predefinito


La tabella seguente contiene le raccomandazioni relative al meccanismo di protezione predefinito per ciascuno dei livelli di protezione e sicurezza definito in precedenza:

|Meccanismo di protezione|Di base|Sensibili|Altamente regolamentato|
|:-------------------|:-------|:--------|:---------------|
|**Applicazione dell'autenticazione a più fattori**|Con un rischio di accesso medio o superiore|Con un rischio di accesso basso o superiore|In tutte le nuove sessioni|
|**Applicazione della modifica della password**|Per utenti ad alto rischio|Per utenti ad alto rischio|Per utenti ad alto rischio|
|**Applicazione della protezione dell'applicazione Intune**|Sì|Sì|Sì|
|**Applicazione della registrazione di Intune**|Richiede un dispositivo aggiunto al dominio o conforme|Richiede un dispositivo aggiunto al dominio o conforme|Richiede un dispositivo aggiunto al dominio o conforme|

### <a name="device-ownership"></a>Proprietà del dispositivo
La tabella sopra riportata riflette la tendenza per molte organizzazioni di supportare una combinazione di dispositivi aziendali nonché di dispositivi personali o Bring Your Own Device (BYOD) per attivare la produttività mobile tra i dipendenti dell'organizzazione. I criteri di protezione app di Intune garantiscono la sicurezza della posta elettronica e ne impediscono la divulgazione all'esterno delle app per dispositivi mobili quali Outlook e le app di Office, sia su COD che su BYOD.  

È necessario che i dispositivi di proprietà aziendale siano gestiti da Intune o che siano aggiunti a un dominio per applicare altri tipi di protezione e controllo.  A seconda della riservatezza dei dati, l'organizzazione potrebbe scegliere di non consentire BYOD per utenti o app specifici.

## <a name="baseline"></a>Di base 
Questa sezione descrive le raccomandazioni per la protezione della posta elettronica per il livello base di protezione dati, identità e dispositivo. Queste raccomandazioni devono soddisfare le esigenze di protezione predefinite di molte organizzazioni.
>[!NOTE]
>I criteri indicati di seguito sono aggiuntivi e si basano l'uno sull'altro. Ogni sezione descrive solo le aggiunte applicate a ogni livello.
>

### <a name="conditional-access-policy-settings"></a>Impostazioni per i criteri di accesso condizionale

#### <a name="identity-protection"></a>Identity Protection 
È possibile offrire agli utenti un'esperienza Single Sign-On (SSO) come quella descritta nelle sezioni precedenti. È necessario intervenire solo al momento opportuno in base agli [eventi di rischio](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events).  

* Richiedere un rischio di accesso medio o superiore in base all'autenticazione a più fattori
* Richiedere la modifica della password sicura per gli utenti ad alto rischio

>[!IMPORTANT]
>[La sincronizzazione delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) e [la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) sono necessari per questa raccomandazione per i criteri.
>

#### <a name="data-loss-prevention"></a>Prevenzione della perdita dei dati 
L'obiettivo per i criteri di gestione dei dispositivi e delle app è evitare la perdita di dati nel caso si perda un dispositivo o venga rubato. È possibile farlo garantendo che l'accesso ai dati sia protetto da un PIN, che i dati siano crittografati nel dispositivo e che il dispositivo non sia stato compromesso.

|Raccomandazione per i criteri|Descrizione|
|:--------------------|:----------|
|**Richiedere la gestione del PC da parte di un utente**|Richiedere agli utenti di unire i propri PC a un dominio di Active Directory o registrarli nella gestione con Intune o Gestione configurazione|
|**Applicare le impostazioni di sicurezza tramite oggetti Criteri di gruppo o criteri di Gestione configurazione per i PC aggiunti al dominio**|Distribuire i criteri che consentono di configurare i computer gestiti per attivare BitLocker, abilitare l'antivirus e il firewall|
|**Richiedere la gestione dei dispositivi mobili**|Richiedere che i dispositivi utente usati per accedere alla posta elettronica siano gestiti da Intune o che la posta elettronica aziendale sia accessibile solo mediante app di posta elettronica per dispositivi mobili protette dai criteri di protezione app di Intune, quali Outlook Mobile|
|**Applicare un criterio di conformità del dispositivo Intune ai dispositivi gestiti**|Applicare un criterio di conformità del dispositivo Intune ai dispositivi mobili aziendali gestiti e ai PC gestiti da Intune per cui è necessario: un PIN con una lunghezza minima di 6 caratteri, la crittografia del dispositivo, un dispositivo integro, non jailbroken o rooted, ma che superi l'attestazione d'integrità, e, se disponibile, richiedere dispositivi a basso rischio in base a un MTP di terze parti come Lookout o SkyCure|
|**Applicare un criterio di protezione app di Intune per le applicazioni gestite in esecuzione su dispositivi non gestiti**|Applicare un criterio di protezione app di Intune per le app gestite in esecuzione su dispositivi mobili personali non gestiti in modo da richiedere: un PIN con una lunghezza minima di 6 caratteri, la crittografia del dispositivo e l'integrità del dispositivo, che non sia jailbroken o rooted, ma che superi l'attestazione d'integrità|

### <a name="user-impact"></a>Impatto sugli utenti

Per la maggior parte delle organizzazioni, è importante poter definire le aspettative degli utenti sul momento e sulle condizioni previste per accedere a Office 365 e alla posta elettronica.  

Gli utenti in genere possono usufruire del Single Sign-On ad eccezione delle situazioni seguenti: 
* Per la richiesta dei token di autenticazione per Exchange Online:
  * Agli utenti potrebbe essere richiesto di eseguire l'autenticazione a più fattori ogni volta che viene rilevato un rischio di accesso medio o elevato e se gli utenti non hanno ancora eseguito l'autenticazione a più fattori nella sessione corrente.  
  * Agli utenti verrà richiesto di usare le app di posta elettronica che supportano l'SDK di protezione app di Intune o di accedere ai messaggi di posta elettronica da dispositivi conformi a Intune o aggiunti a un dominio AD. 
* Quando gli utenti a rischio accedono e completano correttamente l'autenticazione a più fattori, verrà richiesto loro di cambiare la password.

## <a name="sensitive"></a>Sensibili

Questa sezione descrive le raccomandazioni per la protezione della posta elettronica per il livello sensibile di protezione dati, identità e dispositivo. Queste raccomandazioni sono indirizzate a clienti che hanno un subset di dati che devono essere protetti a livelli superiori o che richiedono la protezione di tutti i dati a livelli superiori. 

È possibile applicare una maggiore protezione per tutti o per set di dati specifici nell'ambiente di Office 365. Ad esempio, è possibile applicare i criteri per garantire che i dati sensibili siano condivisi solo tra app protette per impedire la perdita di dati. Si consiglia di proteggere le identità e i dispositivi che accedono ai dati sensibili con livelli di sicurezza analoghi. 

### <a name="conditional-access-policy-settings"></a>Impostazioni per i criteri di accesso condizionale
#### <a name="identity-protection"></a>Identity Protection 

È possibile offrire agli utenti un'esperienza Single Sign-On (SSO) come quella descritta nelle sezioni precedenti. È necessario intervenire solo al momento opportuno in base agli [eventi di rischio](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events).   

* Richiedere l'autenticazione a più fattori in sessioni a rischio basso o elevato
 * Richiedere la modifica della password sicura per gli utenti ad alto rischio

>[!IMPORTANT]
>[La sincronizzazione delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) e [la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) sono necessari per questa raccomandazione per i criteri.
>

#### <a name="data-loss-prevention"></a>Prevenzione della perdita dei dati 

L'obiettivo per i criteri di gestione dei dispositivi e delle app è evitare la perdita di dati nel caso si perda un dispositivo o venga rubato. È possibile farlo garantendo che l'accesso ai dati sia protetto da un PIN, che i dati siano crittografati nel dispositivo e che il dispositivo non sia stato compromesso.

|Raccomandazione per i criteri|Descrizione|
|:--------------------|:----------|
|**Richiedere la gestione del PC da parte di un utente**|Richiedere agli utenti di unire i propri PC a un dominio di Active Directory o registrarli nella gestione con Intune o Gestione configurazione e assicurarsi che questi dispositivi siano conformi ai criteri prima di concede l'accesso alla posta elettronica|
|**Applicare le impostazioni di sicurezza tramite oggetti Criteri di gruppo o criteri di Gestione configurazione per i PC aggiunti al dominio**|Distribuire i criteri che consentono di configurare i computer gestiti per attivare BitLocker, abilitare l'antivirus e il firewall|
|**Richiedere la gestione dei dispositivi mobili**|Richiedere che i dispositivi utente usati per accedere alla posta elettronica siano gestiti da Intune o che la posta elettronica aziendale sia accessibile solo mediante app di posta elettronica per dispositivi mobili protette dai criteri di protezione app di Intune, quali Outlook Mobile|
|**Applicare un criterio di conformità del dispositivo Intune ai dispositivi gestiti**|Applicare un criterio di conformità del dispositivo Intune ai dispositivi mobili aziendali gestiti e ai PC gestiti da Intune per cui è necessario: un PIN con una lunghezza minima di 6 caratteri, la crittografia del dispositivo, un dispositivo integro, non jailbroken o rooted, ma che superi l'attestazione d'integrità, e, se disponibile, richiedere dispositivi a basso rischio in base a un MTP di terze parti come Lookout o SkyCure|
|**Applicare un criterio di protezione app di Intune per le applicazioni gestite in esecuzione su dispositivi non gestiti**|Applicare un criterio di protezione app di Intune per le app gestite in esecuzione su dispositivi mobili personali non gestiti in modo da richiedere: un PIN con una lunghezza minima di 6 caratteri, la crittografia del dispositivo e l'integrità del dispositivo, che non sia jailbroken o rooted, ma che superi l'attestazione d'integrità|

### <a name="user-impact"></a>Impatto sugli utenti

Per la maggior parte delle organizzazioni, è importante poter definire le aspettative degli utenti in particolare per quanto riguarda il momento e le condizioni previste per accedere alla posta elettronica di Office 365. 

Gli utenti in genere possono usufruire del Single Sign-On ad eccezione delle situazioni seguenti: 
* Per la richiesta dei token di autenticazione per Exchange Online:
  * Agli utenti verrà richiesto di eseguire l'autenticazione a più fattori ogni volta che viene rilevato un rischio di accesso medio o elevato e se gli utenti non hanno ancora eseguito l'autenticazione a più fattori nella sessione corrente.  
  * Agli utenti verrà richiesto di usare le app di posta elettronica che supportano l'SDK di protezione app di Intune o di accedere ai messaggi di posta elettronica da dispositivi conformi a Intune o aggiunti a un dominio AD. 
* Quando gli utenti a rischio accedono e completano correttamente l'autenticazione a più fattori, verrà richiesto loro di cambiare la password.

## <a name="highly-regulated"></a>Altamente regolamentato
Questa sezione descrive le raccomandazioni per la protezione della posta elettronica per il livello altamente regolamentato di protezione dati, identità e dispositivo. Queste raccomandazioni sono indirizzate a clienti che potrebbero disporre di una quantità molto piccola di dati ovvero dati altamente classificati, segreti commerciali o regolamentati. Microsoft offre funzionalità per consentire alle organizzazioni di soddisfare questi requisiti, inclusa la protezione aggiuntiva per le identità e i dispositivi. 

### <a name="conditional-access-policy-settings"></a>Impostazioni per i criteri di accesso condizionale
#### <a name="identity-protection"></a>Identity Protection 

Per il livello altamente regolamentato Microsoft consiglia di applicare l'autenticazione a più fattori per tutte le nuove sessioni.
* Richiedere l'autenticazione a più fattori per tutte le nuove sessioni
* Richiedere la modifica della password sicura per gli utenti ad alto rischio

>[!IMPORTANT]
>[La sincronizzazione delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) e [la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) sono necessari per questa raccomandazione per i criteri.
>

#### <a name="data-loss-prevention"></a>Prevenzione della perdita dei dati
L'obiettivo per i criteri di gestione dei dispositivi e delle app è evitare la perdita di dati nel caso si perda un dispositivo o venga rubato. È possibile farlo garantendo che l'accesso ai dati sia protetto da un PIN, che i dati siano crittografati nel dispositivo e che il dispositivo non sia stato compromesso.

Per il livello altamente regolamentato, è consigliabile richiedere app che supportano i criteri di protezione app di Intune App in esecuzione solo su dispositivi conformi a Intune o aggiunti al dominio.

|Raccomandazione per i criteri|Descrizione|
|:--------------------|:----------|
|**Richiedere la gestione del PC da parte di un utente**|Richiedere agli utenti di unire i propri PC a un dominio di Active Directory o registrarli nella gestione con Intune o Gestione configurazione e assicurarsi che questi dispositivi siano conformi ai criteri prima di concede l'accesso alla posta elettronica|
|**Applicare le impostazioni di sicurezza tramite oggetti Criteri di gruppo o criteri di Gestione configurazione per i PC aggiunti al dominio**|Distribuire i criteri che consentono di configurare i computer gestiti per attivare BitLocker, abilitare l'antivirus e il firewall|
|**Richiedere la gestione dei dispositivi mobili**|Richiedere che i dispositivi utente usati per accedere alla posta elettronica siano gestiti da Intune o che la posta elettronica aziendale sia accessibile solo mediante app di posta elettronica per dispositivi mobili protette dai criteri di protezione app di Intune, quali Outlook Mobile|
|**Applicare un criterio di conformità del dispositivo Intune ai dispositivi gestiti**|Applicare un criterio di conformità del dispositivo Intune ai dispositivi mobili aziendali gestiti e ai PC gestiti da Intune per cui è necessario: un PIN con una lunghezza minima di 6 caratteri, la crittografia del dispositivo, un dispositivo integro, non jailbroken o rooted, ma che superi l'attestazione d'integrità, e, se disponibile, richiedere dispositivi a basso rischio in base a un MTP di terze parti come Lookout o SkyCure|

### <a name="user-impact"></a>Impatto sugli utenti
Per la maggior parte delle organizzazioni, è importante poter definire le aspettative degli utenti in particolare per quanto riguarda il momento e le condizioni previste per accedere alla posta elettronica di Office 365. 

* La durata massima di una sessione Single Sign-On è 1 giorno. Agli utenti verrà richiesto di eseguire nuovamente l'autenticazione con l'autenticazione a più fattori quando le sessioni scadono.
* Quando gli utenti a rischio, dopo aver completato l'autenticazione a più fattori, verrà richiesto loro di cambiare la password.
* Per la richiesta dei token di autenticazione per Exchange Online:
  * Agli utenti verrà richiesto di eseguire l'autenticazione a più fattori ogni volta che si avvia una nuova sessione.  
  * Agli utenti verrà richiesto di usare le app di posta elettronica che supportano l'SDk per la protezione app di Intune
  * Agli utenti verrà richiesto di accedere ai messaggi di posta elettronica da dispositivi conformi a Intune o aggiunti a un dominio di AD. 
 
 ## <a name="next-steps"></a>Passaggi successivi
 [Distribuire i criteri consigliati](secure-email-deploy-recommended-policies.md)

