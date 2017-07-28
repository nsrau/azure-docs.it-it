---
title: Distribuzione dei criteri consigliati per proteggere la posta elettronica di Azure AD | Microsoft Docs
description: Descrive le raccomandazioni relative alla distribuzione di Microsoft e all&quot;applicazione di criteri e configurazioni di posta elettronica.
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 08f72f1ba219946486a0fc62ea74cb338f5a85b6
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---

# <a name="deploy-recommended-policies"></a>Distribuire i criteri consigliati

In questa sezione viene illustrato come distribuire i criteri consigliati in un ambiente appena sottoposto a provisioning. La configurazione di questi criteri in un ambiente di laboratorio separato consente di comprendere e valutare i criteri consigliati prima di avviare la gestione temporanea dell'implementazione per gli ambienti di pre-produzione e di produzione. L'ambiente appena sottoposto a provisioning può essere cloud o ibrido.  

Per distribuire correttamente i criteri consigliati, nel portale di Azure è necessario soddisfare i prerequisiti indicati in precedenza. In particolare, è necessario:
* configurare le reti denominate, per garantire che Azure Identity Protection possa generare correttamente un punteggio di rischio
* richiedere la registrazione di tutti gli utenti per l'autenticazione a più fattori (MFA)
* configurare la sincronizzazione delle password e la reimpostazione della password self-service per consentire agli utenti di poter reimpostare le password da soli

È possibile considerare sia i criteri di Azure AD che quelli di Intune per gruppi di utenti specifici. È consigliabile implementare i criteri definiti in precedenza in più passaggi. In questo modo è possibile convalidare le prestazioni dei criteri e i team di supporto relativi ai criteri in modo incrementale.

## <a name="baseline-ca-policy"></a>Criteri CA di base

Per creare un nuovo criterio di accesso condizionale, accedere al portale di Microsoft Azure con le credenziali di amministratore. Passare quindi a **Azure Active Directory > Sicurezza > Accesso condizionale**. 

È possibile aggiungere un nuovo criterio (+Aggiungi) come illustrato nella schermata seguente:

![Criteri CA di base](./media/secure-email/baseline-ca-policy.png)

Le tabelle seguenti descrivono le impostazioni appropriate necessarie per esprimere i criteri obbligatori per ogni livello di protezione.

### <a name="medium-and-above-risk-requires-mfa"></a>Per il rischio medio ed elevato è necessaria l'autenticazione a più fattori

La tabella seguente descrive le impostazioni dei criteri di accesso condizionali da implementare per questo criterio.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Assegnazioni**|Utenti e gruppi|Includi|Selezionare utenti e gruppi: selezionare un gruppo di sicurezza specifico contenente gli utenti di destinazione|Iniziare con il gruppo di sicurezza che include gli utenti del progetto pilota.|
|||Escludi|Gruppo di sicurezza di eccezione; account del servizio (identità delle app)|Appartenenza modificato in base alle necessità temporanee|
||App cloud|Includi|Selezionare le app: selezionare Office 365 Exchange Online||
||Condizioni|Configurato|Sì|Configurazione specifica in base all'ambiente e alle esigenze|
||Rischio di accesso|Livello di rischio|Alto, medio|Controllare entrambi|
|**Controlli di accesso**|Concessione|Concedere l'accesso|True |Selezionato|
|||Richiedere l'autenticazione MFA|True |Controllo|
|||Richiede dispositivi conformi|False||
|||Richiede dispositivi aggiunti a un dominio|False||
|||Richiedi tutti i controlli selezionati|True |Selezionato|
|**Abilitare i criteri**|||Attivato|Distribuisce i criteri di accesso condizionale|

### <a name="require-a-compliant-or-domain-joined-device"></a>Richiede un dispositivo aggiunto al dominio o conforme

Per creare un nuovo criterio di accesso condizionale Intune per Exchange Online, accedere al [portale di gestione di Microsoft (http://manage.microsoft.com)](http://manage.microsoft.com/) con le credenziali di amministratore e quindi passare al **Criteri > Accesso condizionale > Criteri di Exchange Online**.

![Criteri di Exchange Online](./media/secure-email/exchange-online-policy.png)

È necessario impostare un criterio di accesso condizionale specifico per Exchange Online nel portale di gestione di Intune per richiedere un conforme o aggiunto a un dominio.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Accesso a un'applicazione**|Outlook e altre app che usano l'autenticazione moderna|Tutte le piattaforme|True |Selezionato|
|||Windows deve soddisfare i seguenti requisiti|Il dispositivo deve essere aggiunto al dominio o conforme|Selezionato (elenco)|
|||Piattaforma selezionata|False||
||Outlook Web Access (OWA)|Bloccare i dispositivi non conformi nella stessa piattaforma, come Outlook|True |Controllo|
||Le app di Exchange ActiveSync che usano l'autenticazione di base|Bloccare i dispositivi non conformi sulle piattaforme supportate da Microsoft Intune|True |Controllo|
|||Bloccare tutti gli altri dispositivi sulle piattaforme non supportati da Microsoft Intune|True |Controllo|
|**Distribuzione dei criteri**|Gruppi di destinazione|Selezionare i gruppi di Active Directory di destinazione per questo criterio|||
|||Tutti gli utenti|False||
|||Gruppi di sicurezza selezionati|True |Selezionato|
|||Modifica|Selezionare un gruppo di sicurezza specifico contenente gli utenti di destinazione||
||Gruppi esenti|Selezionare i gruppi di Active Directory esenti da questi criteri, esegue l'override dei membri dell'elenco di gruppi di destinazione|||
|||Nessun utente esente|True |Selezionato|
|||Gruppi di sicurezza selezionati|False|||

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Accesso condizionale per la gestione delle applicazioni per dispositivi mobili per Exchange Online

È necessario impostare un criterio di accesso condizionale specifico per Exchange Online nel portale di gestione di Intune per gestire le app per dispositivi mobili.

Per gestire le app per dispositivi mobili, accedere al portale di Microsoft Azure con le credenziali di amministratore e quindi passare a **Protezione app di Intune > Impostazioni > Accesso condizionale > Exchange Online**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Accesso all'app**|App consentite|Abilitare l'accesso all'app|Consentire le App che supportano i criteri dell'app Intune|Selezionato (elenco): risulta in un elenco delle combinazioni di app/piattaforma supportata dai criteri dell'app Intune|
|**Accesso utente**|App consentite|Gruppi di utenti limitati|Aggiungere gruppi di utenti: selezionare un gruppo di sicurezza specifico contenente gli utenti di destinazione|Iniziare con il gruppo di sicurezza che include gli utenti del progetto pilota|
|||Gruppi di utenti esenti|Eccezioni per i gruppi di sicurezza|||

#### <a name="apply-to"></a>Apply to

Dopo aver completato il progetto pilota, questi criteri si applicano a tutti gli utenti dell'organizzazione.

## <a name="sensitive-ca-policy"></a>Criteri CA riservati

### <a name="low-and-above-risk-requires-mfa"></a>Per il rischio basso ed elevato è necessaria l'autenticazione a più fattori
La tabella seguente descrive le impostazioni dei criteri di accesso condizionali da implementare per i criteri con rischio basso ed elevato.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Assegnazioni**|Utenti e gruppi|Includi|Selezionare utenti e gruppi: selezionare un gruppo di sicurezza specifico contenente gli utenti di destinazione|Iniziare con il gruppo di sicurezza che include gli utenti del progetto pilota|
|||Escludi|Gruppo di sicurezza di eccezione; account del servizio (identità delle app)|Appartenenza modificato in base alle necessità temporanee|
||App cloud|Includi|Selezionare le app: selezionare Office 365 Exchange Online||
||Condizioni|Configurato|Sì|Configurazione specifica in base all'ambiente e alle esigenze|
||Rischio di accesso|Configurato|Sì|Configurazione specifica in base all'ambiente e alle esigenze|
|||Livello di rischio|Basso, medio, elevato|Controllare tutti e tre|
|**Controlli di accesso**|Concessione|Concedere l'accesso|True |Selezionato|
|||Richiedere l'autenticazione MFA|True |Controllo|
|||Richiede dispositivi conformi|False||
|||Richiede un dispositivo aggiunto a un dominio|False||
|||Richiedi tutti i controlli selezionati|True |Selezionato|
|**Abilitare i criteri**|||Attivato|Distribuisce i criteri di accesso condizionale|

### <a name="require-a-compliant-or-domain-joined-device"></a>Richiede un dispositivo aggiunto al dominio o conforme
(Vedere le istruzioni di base)

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Accesso condizionale per la gestione delle applicazioni per dispositivi mobili per Exchange Online

(Vedere le istruzioni di base)

#### <a name="apply-to"></a>Apply to

Dopo aver completato il progetto pilota, questi criteri si applicano agli utenti dell'organizzazione che richiedono l'accesso alla posta elettronica considerata riservata.

## <a name="highly-regulated-ca-policy"></a>Criteri CA con regolamentazione elevata
### <a name="mfa-required"></a>Autenticazione a più fattori obbligatoria

La tabella seguente descrive le impostazioni dei criteri di accesso condizionali da implementare per i criteri con regolamentazione elevata.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Assegnazioni**|Utenti e gruppi|Includi|Selezionare utenti e gruppi: selezionare un gruppo di sicurezza specifico contenente gli utenti di destinazione|Iniziare con il gruppo di sicurezza che include gli utenti del progetto pilota|
|||Escludi|Gruppo di sicurezza di eccezione; account del servizio (identità delle app)|Appartenenza modificato in base alle necessità temporanee|
||App cloud|Includi|Selezionare le app: selezionare Office 365 Exchange Online||
|**Controlli di accesso**|Concessione|Concedere l'accesso|True |Selezionato|
|||Richiedere l'autenticazione MFA|True |Controllo|
|||Richiede dispositivi conformi|False|Controllo|
|||Richiede un dispositivo aggiunto a un dominio|False||
|||Richiedi tutti i controlli selezionati|True |Selezionato|
|**Abilitare i criteri**|||Attivato|Distribuisce i criteri di accesso condizionale|

### <a name="require-a-compliant-or-domain-joined-device"></a>Richiede un dispositivo aggiunto al dominio o conforme
(Vedere le istruzioni di base)
### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Accesso condizionale per la gestione delle applicazioni per dispositivi mobili per Exchange Online
(Vedere le istruzioni di base)
#### <a name="apply-to"></a>Apply to
Dopo aver completato il progetto pilota, questi criteri si applicano agli utenti dell'organizzazione che richiedono l'accesso alla posta elettronica considerata con un alto livello di regolamentazione.

## <a name="user-risk-policy"></a>Criteri di rischio utente
### <a name="high-risk-users-must-change-password"></a>Gli utenti ad alto rischio devono modificare la password
Per assicurarsi che tutti gli account compromessi di utenti ad alto rischio modifichino la password all'accesso, è necessario applicare i criteri seguenti. 

Accedere al [portale di Microsoft Azure (http://portal.azure.com)](http://portal.azure.com/) con le credenziali di amministratore, quindi passare al **Azure AD Identity Protection > Criteri di rischio utente**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Assegnazioni**|Utenti|Includi|Tutti gli utenti|Selezionato|
|||Escludi|Nessuna||
||Condizioni|Rischio utente|Alto|Selezionato|
|**Controlli**|Accesso|Consentire l'accesso|True |Selezionato|
||Accesso|Richiedere la modifica della password|True |Controllo|
|**Revisione**|N/D|N/D|N/D|N/D|
|**Applicare i criteri**|||Attivato|Avvia l'applicazione dei criteri|

## <a name="additional-configurations"></a>Configurazioni aggiuntive
Oltre ai criteri descritti in precedenza, è necessario configurare le impostazioni per le applicazione per dispositivi mobili e la gestione dei dispositivi illustrate in questa sezione. 

### <a name="intune-mobile-application-management"></a>Gestione di applicazioni per dispositivi mobili Intune 

Per garantire la protezione della posta elettronica con i suggerimenti per i criteri indicati in precedenza per ogni livello di sicurezza e protezione dei dati, è necessario creare criteri di protezione dell'app Intune all'interno del portale di Azure.

Per creare un nuovo criterio di protezione delle app, accedere al portale di Microsoft Azure con le credenziali di amministratore e quindi passare a **Protezione app di Intune > Impostazioni > Criteri per le app**.

Aggiungere un nuovo criterio (+Aggiungi) come illustrato nella schermata seguente:

![Gestione di applicazioni per dispositivi mobili Intune](./media/secure-email/intune-mobile-app-mgmt.png)

>[!NOTE]
>Le opzioni dei criteri per la protezione delle app di iOS e Android presentano delle piccole differenze. Di seguito vengono indicati i criteri specifici per Android.
>

Le tabelle seguenti descrivono in dettaglio le impostazioni appropriate necessarie per esprimere i criteri obbligatori per ogni livello di protezione.
| La tabella seguente descrive le impostazioni dei criteri per la protezione dell'app Intune consigliate.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Generale**|Email|Nome|Criteri di protezione della posta elettronica per Android|Immettere il nome del criterio|
|||Descrizione||Immettere il testo che descrive il criterio|
|||Piattaforma|Android|Le opzioni dei criteri per la protezione delle app di iOS e Android presentano delle piccole differenze: questo criterio è specifico per Android|
|**App**|Applicazioni|App|Outlook|Selezionato (elenco)|
|**Impostazioni**|Rilocazione dei dati|Impedire il backup di Android|Sì|In iOS questo effettua una chiamata specifica per iTunes e iCloud|
||||Consentire all'app di trasferire i dati ad altre app|App gestite da criteri||
|||Consentire all'app di ricevere i dati da altre app|App gestite da criteri||
|||Evitare "Save As" (Salva con nome)|Sì||
|||Limitare le operazioni taglia, copia e incolla con le altre app|App gestite da criteri||
|||Limitare il contenuto Web per la visualizzazione in browser gestito|No||
|||Crittografa dati app|Sì|In iOS selezionare l'opzione: When device is locked (Quando il dispositivo è bloccato)|
|||Disabilitare la sincronizzazione contatti|No||
||Accesso|Richiedere il PIN per l'accesso|Sì||
|||Numero di tentativi prima della reimpostazione del PIN|3||
|||Consentire il PIN semplice|No||
|||Lunghezza del PIN|6||
|||Consentire impronta digitale anziché PIN|Sì||
|||Richiedere credenziali aziendali per l'accesso|No||
|||Bloccare l'esecuzione delle app gestite nei dispositivi jailbroken o rooted|Sì||
|||Controllare di nuovo i requisiti di accesso dopo (minuti)|30||
|||Periodo di prova offline|720||
|||Intervallo offline (giorni) prima della cancellazione dei dati dell'app|90||
|||Bloccare l'acquisizione schermo e l'assistente per Android|No|In iOS questa opzione non è disponibile|

Al termine, ricordarsi di fare clic su "Crea". Ripetere i passaggi precedenti e sostituire la piattaforma selezionata, nell'elenco a discesa, con iOS. In questo modo si creano due criteri per l'app, pertanto dopo aver creato il criterio, assegnare i gruppi al criterio e distribuirlo.

### <a name="intune-mobile-device-management"></a>Gestione dei dispositivi mobili di Intune
Creare i criteri di configurazione e conformità seguenti accedendo al [portale di Microsoft Management (http://manage.microsoft.com)](https://manage.microsoft.com/) con le credenziali di amministratore.

#### <a name="ios-email-profile"></a>Profilo di posta elettronica iOS
Nel [portale di gestione di Intune (https://manage.microsoft.com)](https://manage.microsoft.com/) creare i criteri di configurazione seguenti in **Criteri > Criteri di configurazione > Aggiungi > iOS > Profilo di posta elettronica (iOS 8 e versioni successive)**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Profilo di posta elettronica**|Exchange Active Sync|Host (#)|Outlook.office365.com||
|||Nome account (#)|SecureEmailAccount|Scelta dell'amministratore|
|||Username|Nome dell'entità utente|Selezionato – Elenco a discesa|
|||Indirizzo di posta elettronica|Indirizzo SMTP primario|Selezionato – Elenco a discesa|
|||Metodo di autenticazione|Nome utente e password|Selezionato – Elenco a discesa|
|||Usare S/MIME|False||
||Impostazioni di sincronizzazione|Numero di giorni per la sincronizzazione della posta elettronica|Due settimane|Selezionato – Elenco a discesa|
|||Usare SSL|True |Controllo|
|||Consentire lo spostamento dei messaggi ad altri account di posta elettronica|False||
|||Consentire l'invio della posta da applicazioni di terze parti|True ||
|||Sincronizzare gli indirizzi di posta elettronica utilizzati di recente|True |Controllo|

#### <a name="ios-app-sharing-profile"></a>Profilo di condivisione dell'app iOS
Nel [portale di gestione di Intune (https://manage.microsoft.com)](https://manage.microsoft.com/) creare i criteri di configurazione seguenti in **Criteri > Criteri di configurazione > Aggiungi > iOS > Configurazione generale (iOS 8.0 e versioni successive)**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Sicurezza**|Tutti|Tutti|Non configurato||
|**Cloud**|Tutti|Tutti|Non configurato||
|**Applicazioni**|Browser|Tutti|Non configurato||
||App|Consentire l'installazione di app|Non configurato||
|||Richiedere una password per accedere all'archivio dell'applicazione|Non configurato||
|||Tutti gli acquisti in-app|Non configurato||
|||Consentire i documenti gestiti in altre app gestite (iOS 8.0 e versioni successive)|No|Selezionato – Elenco a discesa|
|||Consentire i documenti non gestiti in altre app gestite|Non configurato||
|||Consentire videoconferenza|Non configurato||
|||Consentire all'utente di considerare attendibili gli autori di nuove app aziendali|Non configurato||
||Giochi|Tutti|Non configurato||
||Contenuti multimediali|Tutti|Non configurato|||

#### <a name="android-email-profile"></a>Profilo di posta elettronica Android
Nel [portale di gestione di Intune (https://manage.microsoft.com)](https://manage.microsoft.com/) creare i criteri di configurazione seguenti in **Criteri > Criteri di configurazione > Aggiungi > iOS > Profilo di posta elettronica (Samsung KNOX Standard 4.0 e versioni successive)**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Profilo di posta elettronica**|Exchange Active Sync|Host (#)| Outlook.office365.com|
|||Nome account (#)|SecureEmailAccount|Scelta dell'amministratore|
|||Username|Nome dell'entità utente|Selezionato – Elenco a discesa|
|||Indirizzo di posta elettronica|Indirizzo SMTP primario|Selezionato – Elenco a discesa|
|||Metodo di autenticazione|Nome utente e password|Selezionato – Elenco a discesa|
|||Usare S/MIME|False||
||Impostazioni di sincronizzazione|Numero di giorni per la sincronizzazione della posta elettronica|Due settimane|Selezionato – Elenco a discesa|
|||Pianificazione sincronizzazione|Non configurato|Selezionato – Elenco a discesa|
|||Usare SSL|True |Controllo|
|||Tipo di contenuto da sincronizzare|||
|||Email|True |Controllo (bloccato)|
|||Contatti|True |Controllo|
|||Calendario|True |Controllo|
|||Attività|True |Controllo|
|||Note|True |Controllo|

#### <a name="android-for-work-email-profile"></a>Profilo di posta elettronica Android For Work
Nel [portale di gestione di Intune (https://manage.microsoft.com)](https://manage.microsoft.com/) creare i criteri di configurazione seguenti in **Criteri > Criteri di configurazione > Aggiungi > iOS > Profilo di posta elettronica (Android for Work - Gmail)**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Profilo di posta elettronica**|Exchange Active Sync|Host(#)| Outlook.office365.com|
|||Nome account (#)|SecureEmailAccount|Scelta dell'amministratore|
|||Username|Nome dell'entità utente|Selezionato – Elenco a discesa|
|||Indirizzo di posta elettronica|Indirizzo SMTP primario|Selezionato – Elenco a discesa|
|||Metodo di autenticazione|Nome utente e password|Selezionato – Elenco a discesa|
||Impostazioni di sincronizzazione|Numero di giorni per la sincronizzazione della posta elettronica|Due settimane|Selezionato – Elenco a discesa|
|||Usare SSL|True |Controllo|

#### <a name="android-for-work-app-sharing-profile"></a>Profilo di condivisione dell'app Android for Work
Nel [portale di gestione di Intune (https://manage.microsoft.com)](https://manage.microsoft.com/) creare i criteri di configurazione seguenti in **Criteri > Criteri di configurazione > Aggiungi > iOS > Configurazione generale (Android for Work)**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Sicurezza**|Password|Lunghezza minima password|Non configurato||
|||Numero accessi non riusciti ripetuti prima di rimuovere il profilo del lavoro|Non configurato||
|||Minuti di inattività prima di bloccare il dispositivo|Non configurato||
|||Scadenza della password (giorni)|Non configurato||
|||Ricordare la cronologia della password|Non configurato||
|||Richiedere una password per sbloccare il dispositivo mobile|Non configurato||
|||Consentire lo sblocco con impronta digitale (Android 6.0+)|Non configurato||
|||Consentire Smart Lock e altri agenti di attendibilità (Android 6.0+)|Non configurato||
||Impostazioni del profilo di lavoro|Consentire la condivisione di dati tra i profili personali e di lavoro|Le app nel profilo di lavoro possono gestire la richiesta di condivisione dal profilo personale|Selezionato – Elenco a discesa|
|||Nascondere le notifiche del profilo di lavoro quando il dispositivo è bloccato (Android 6.0+)|Non configurato||
|||Impostare i criteri di autorizzazione predefiniti dell'app (Android 6.0+)|Non configurato|||

#### <a name="device-compliance-policy"></a>Criteri di conformità del dispositivo
Nel [portale di gestione di Intune (https://manage.microsoft.com)](https://manage.microsoft.com/) creare i criteri di configurazione seguenti in **Criteri > Criteri di conformità > Aggiungi**.

|Categorie|Tipo|Proprietà|Valori|Note|
|:---------|:---|:---------|:-----|:----|
|**Sicurezza del sistema**|Password|Richiedere una password per sbloccare il dispositivo mobile (...)|Sì|Selezionato – Elenco a discesa|
|||Consentire password semplici (...)|No|Selezionato – Elenco a discesa|
|||Lunghezza minima password (...)|6|Selezionato - elenco|
||Impostazioni avanzate della password|Tutti|Non configurato||
||Crittografia|Richiedere la crittografia sul dispositivo mobile (...)|Sì|Selezionato – Elenco a discesa|
||Profili di posta elettronica|L'account di posta elettronica deve essere gestito da Intune (iOS 8.0+)|Sì| Selezionato – Elenco a discesa|
|||Selezionare (#)||Selezionare i criteri di configurazione di posta elettronica per iOS: criteri di posta elettronica per iOS (vedere i criteri di configurazione indicati in precedenza)|
|**Integrità dei dispositivi**|Attestazione di integrità del dispositivo Windows|Richiedere che i dispositivi siano segnalati come integri (Windows 10 Desktop e Mobile e versioni successive)|Sì||
||Impostazioni di sicurezza del dispositivo|Tutti|Non configurato||
||Protezione dalle minacce del dispositivo|Tutti|Non configurato||
||Jailbreak|Il dispositivo non deve essere jailbroken o rooted (iOS 8.0+, Android 4.0+)|Sì||
|**Proprietà del dispositivo**|Versione del sistema operativo|Tutti|Non configurato|||

Affinché tutti i criteri sopra indicati siano considerati distribuiti, è necessario definire gruppi di utenti come destinatari. A tale scopo è possibile creare criteri, al salvataggio o in seguito, selezionando Gestisci distribuzione nella sezione Criteri, nello stesso livello di Aggiungi.

## <a name="remediating-events-that-have-results-in-medium-or-high-risk-access"></a>Correzione degli eventi con accesso con rischio medio o alto
Se un utente segnala che si prevede di eseguire l'autenticazione a più fattori anche se questa operazione prima non era necessaria, l'assistenza può controllarne lo stato dal punto di vista del rischio.  

Gli utenti all'interno dell'organizzazione con un ruolo di amministratore globale o amministratore della sicurezza possono usare Azure AD Identity Protection per esaminare gli eventi rischiosi che hanno contribuito al punteggio del rischio calcolato. Se si identificano degli eventi che sono stati contrassegnati come sospetti, ma vengono confermati come validi, ad esempio un accesso da un percorso sconosciuto quando un dipendente è in ferie, l'amministratore può risolvere l'evento in modo che non venga più calcolato nel punteggio di rischio.

## <a name="next-steps"></a>Passaggi successivi
[Descrizioni dei servizi EMS e Office 365](secure-email-ems-office365-service-descriptions.md)

