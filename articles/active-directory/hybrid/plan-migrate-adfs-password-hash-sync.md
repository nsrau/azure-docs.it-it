---
title: 'Azure AD Connect: Eseguire la migrazione dalla federazione alla sincronizzazione degli hash delle password per Azure AD | Microsoft Docs'
description: Informazioni sulla migrazione di un ambiente ibrido di gestione delle identità dalla federazione alla sincronizzazione degli hash delle password.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a14e630c23af3e0228bf4806851f29cfab199215
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103979"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Eseguire la migrazione dalla federazione alla sincronizzazione degli hash delle password per Azure AD
Il documento seguente presenta le linee guida relative alla migrazione da AD FS alla sincronizzazione degli hash delle password.

>[!NOTE]
>Fare clic [qui](https://aka.ms/ADFSTOPHSDPDownload) per scaricare una copia di questo documento.


## <a name="prerequisites-for-the-migration"></a>Prerequisiti per la migrazione 
Prima di eseguire la migrazione, verificare che siano soddisfatti i prerequisiti seguenti.
### <a name="update-azure-ad-connect"></a>Aggiornare Azure AD Connect

Come requisito minimo, per eseguire i passaggi per la migrazione all'autenticazione pass-through, è necessario disporre di [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Questa versione contiene modifiche significative alla modalità di conversione delle informazioni di accesso e riduce a minuti il tempo complessivo per la migrazione dalla federazione all'autenticazione cloud, che in precedenza poteva richiedere anche ore.

> [!IMPORTANT]
> La documentazione, gli strumenti e i blog meno recenti indicano che la conversione degli utenti è un passaggio obbligatorio per la conversione dei domini da federati a gestiti. Si noti che la conversione degli utenti non è più necessaria e Microsoft sta aggiornando la documentazione e gli strumenti in base a questa modifica.

Per aggiornare Azure AD Connect alla versione più recente, seguire queste [istruzioni per l'aggiornamento](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Autorizzazioni necessarie per la sincronizzazione degli hash delle password

Azure AD Connect può essere configurato tramite Impostazioni rapide o Installazione personalizzata. Se si è usata l'opzione Installazione personalizzata, è possibile che le [autorizzazioni necessarie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) per la sincronizzazione degli hash delle password non siano configurate.

Per poter sincronizzare gli hash delle password, l'account di Active Directory Domain Services di Azure AD Connect richiede le autorizzazioni seguenti.

* Replica modifiche directory

* Replica modifiche directory - Tutto

È pertanto consigliabile verificare che queste autorizzazioni siano configurate per tutti i domini nella foresta.

### <a name="plan-migration-method"></a>Pianificare il metodo di migrazione

Per eseguire la migrazione dall'autenticazione federata alla sincronizzazione degli hash delle password e all'accesso Single Sign-On facile sono disponibili due metodi. La scelta del metodo dipende dal modo in cui AD FS è stato originariamente configurato. 



- **Opzione A: Uso di Azure AD Connect**. Se AD FS è stato originariamente configurato con Azure AD Connect, il passaggio alla sincronizzazione degli hash delle password come metodo di accesso utente deve essere eseguito tramite la procedura guidata di Azure AD Connect.   
Quando si usa Azure AD Connect, al momento della modifica del metodo di accesso utente viene eseguito automaticamente il cmdlet Set-MsolDomainAuthentication e pertanto non si ha alcun controllo sul processo di annullamento della federazione per tutti i domini federati verificati nel tenant di Azure AD.

> [!NOTE]
> Al momento, non è possibile evitare l'annullamento della federazione di tutti i domini nel tenant quando si imposta la sincronizzazione degli hash delle password come metodo di accesso utente e AAD Connect è stato originariamente usato per la configurazione automatica di AD FS.  



- **Opzione B: Uso di Azure AD Connect con PowerShell**. Questo metodo può essere usato solo quando AD FS non è stato originariamente configurato con Azure AD Connect. È sempre necessario modificare il metodo di accesso utente tramite la procedura guidata di Azure AD Connect, ma la differenza principale sta nel fatto che il cmdlet Set-MsolDomainAuthentication non viene eseguito automaticamente perché non è in grado di riconoscere la farm AD FS e pertanto si ha pieno controllo nella scelta dei domini da convertire e dell'ordine in cui convertirli.

Per comprendere quale metodo è preferibile usare, eseguire i passaggi nella sezione seguente.

#### <a name="verify-current-user-sign-in-settings"></a>Verificare le impostazioni di accesso utente correnti

Verificare le impostazioni di accesso utente correnti eseguendo l'accesso al portale di Azure AD [https://aad.portal.azure.com](https://aad.portal.azure.com/) con un account di amministratore globale.

Nella sezione Accesso utente verificare che lo stato di Federazione sia Abilitato e quello di Accesso Single Sign-On facile e Autenticazione pass-through sia Disabilitato. Verificare anche lo stato di sincronizzazione delle password, che dovrebbe risultare disabilitato, a meno che non sia stato abilitato in precedenza.

![Figura 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Verificare la configurazione di Azure AD Connect

   1. Passare al server di Azure AD Connect, avviare Azure AD Connect e quindi selezionare Configura. 
   2. Nella schermata Attività aggiuntive selezionare Visualizza configurazione corrente e quindi fare clic su Avanti.</br>
   ![Figura 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. Nella schermata Verifica della soluzione prendere nota dello stato di sincronizzazione delle password.</br> 

   Se lo stato di Sincronizzazione hash password è attualmente Disabilitato, è necessario seguire la procedura illustrata in questa guida per abilitare la sincronizzazione. Se invece è Abilitato, si può tranquillamente ignorare la sezione [Passaggio 1: Abilitare la sincronizzazione degli hash delle password](#step-1--enable-password-hash-synchronization) in questa guida.
   4. Nella schermata Verifica della soluzione scorrere verso il basso fino a visualizzare Active Directory Federation Services (AD FS).</br>
 
   Se la configurazione di AD FS è presente in questa sezione, si può tranquillamente presupporre che AD FS sia stato originariamente configurato con Azure AD Connect e quindi la conversione di uno o più domini da federati a gestiti può essere controllata tramite l'opzione "Cambia l'accesso utente" di Azure AD Connect. Questa procedura è descritta nella sezione **Opzione A: Passare dalla federazione alla sincronizzazione degli hash delle password con Azure AD Connect**.
   5. Se Active Directory Federation Services non è presente nelle impostazioni correnti, è necessario convertire manualmente i domini da federati a gestiti tramite PowerShell, come descritto nella sezione **Opzione B: Passare dalla federazione alla sincronizzazione degli hash delle password con Azure AD Connect e PowerShell**.

### <a name="document-current-federation-settings"></a>Documentare le impostazioni di federazione correnti

È possibile trovare le impostazioni di federazione correnti usando il cmdlet Get-MsolDomainFederationSettings.

Il comando da eseguire è il seguente:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Ad esempio: 

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```
Verificare le impostazioni che potrebbero essere state personalizzate nella documentazione relativa alla progettazione e alla distribuzione della federazione, in particolare PreferredAuthenticationProtocol, SupportsMfa e PromptLoginBehavior.

Di seguito sono riportate altre informazioni sulle finalità d'uso di queste impostazioni.

[Supporto del parametro prompt=login di Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se il valore di SupportsMfa è attualmente "True", ciò significa che si sta usando una soluzione MFA (Multi-Factor Authentication) locale per inserire una richiesta di verifica come secondo fattore nel flusso di autenticazione utente. Questa impostazione non sarà più applicabile agli scenari di autenticazione di Azure AD. Per eseguire la stessa funzione, si dovrà usare in alternativa il servizio Azure MFA (basato sul cloud). Valutare con attenzione i requisiti di MFA prima di proseguire e assicurarsi di aver compreso come usare Azure MFA, le implicazioni relative alla gestione delle licenze e il processo di registrazione degli utenti finali prima di procedere alla conversione dei domini. La guida dettagliata relativa alla distribuzione di Azure MFA è disponibile all'indirizzo [https://aka.ms/deploymentplans](https://aka.ms/deploymentplans).

#### <a name="backup-federation-settings"></a>Eseguire il backup delle impostazioni di federazione

Anche se non sarà possibile apportare modifiche ad altre relying party nella farm AD FS durante questo processo, è consigliabile assicurarsi di avere una copia di backup valida corrente della farm AD FS per un eventuale ripristino. Per eseguire questa operazione è possibile usare lo [strumento di ripristino rapido di AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) fornito gratuitamente da Microsoft. Questo strumento può essere usato per eseguire il backup e il ripristino di AD FS, in una farm esistente o in una nuova farm.

Se si sceglie di non usare lo strumento di ripristino rapido di AD FS, è necessario almeno esportare il trust della relying party "Piattaforma delle identità di Microsoft Office 365" e le eventuali regole personalizzate per le attestazioni che sono state aggiunte. A questo scopo è possibile usare l'esempio di PowerShell seguente

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Considerazioni relative alla distribuzione e all'utilizzo di AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Verificare l'utilizzo corrente di AD FS

Prima di procedere alla conversione dei domini da federati a gestiti, è consigliabile esaminare con attenzione il modo in cui si usa attualmente AD FS per Azure AD/Office 365 e altre applicazioni (trust di relying party). In particolare, è consigliabile prendere in considerazione le informazioni riportate nella tabella seguente:

| Se| Allora |
|-|-|
| Si intende continuare a usare AD FS per queste altre applicazioni.| Si useranno sia AD FS che Azure AD e sarà pertanto necessario prendere in considerazione l'esperienza degli utenti finali. È possibile che in alcuni scenari gli utenti debbano eseguire due volte l'autenticazione, una per accedere ad Azure AD (dove otterranno l'accesso Single Sign-On per altre applicazioni come Office 365) e l'altra per le applicazioni ancora associate ad AD FS come trust della relying party. |
| AD FS è un componente altamente personalizzabile e si basa su specifiche impostazioni di personalizzazione definite nel file onload.js, che non è duplicabile in Azure AD. Si può ad esempio aver modificato l'esperienza di accesso per consentire agli utenti di immettere il proprio nome solo nel formato SamAccountName, anziché specificare l'UPN, o di visualizzare informazioni personalizzate distintive dell'azienda.| Prima di procedere, è necessario verificare che gli attuali requisiti di personalizzazione possano essere soddisfatti da Azure AD. Per altre informazioni e indicazioni, vedere le sezioni relative alla personalizzazione di AD FS e all'uso di informazioni personalizzate distintive dell'azienda in AD FS.|
| Si stanno bloccando client di autenticazione legacy tramite AD FS.| Valutare l'opportunità di sostituire i controlli per i client di autenticazione legacy attualmente presenti in AD FS con una combinazione di [controlli di accesso condizionale per l'autenticazione legacy](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) e [regole di accesso client di Exchange Online](https://aka.ms/EXOCAR).|
| Per l'autenticazione degli utenti ad AD FS si richiede l'autenticazione a più fattori con una soluzione MFA basata su server locale.| Non sarà possibile inserire una richiesta di verifica MFA tramite la soluzione MFA locale nel flusso di autenticazione per un dominio gestito, ma a tale scopo sarà possibile usare il servizio Azure MFA non appena il dominio sarà convertito. Se attualmente gli utenti non usano Azure MFA, sarà necessario preparare un singolo passaggio di registrazione per gli utenti finali e comunicare loro le informazioni di autenticazione.|
| Per controllare l'accesso a Office 365, si usano attualmente i criteri di controllo di accesso (regole AuthZ) in AD FS.| Valutare l'opportunità di sostituire questi criteri con i [criteri di accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) e le [regole di accesso client di Exchange Online](https://aka.ms/EXOCAR) equivalenti di Azure AD.|

### <a name="considerations-for-common-ad-fs-customizations"></a>Considerazioni relative alle personalizzazioni comuni di AD FS

#### <a name="inside-corporate-network-claim"></a>Attestazione di presenza all'interno della rete aziendale

L'attestazione InsideCorporateNetwork viene rilasciata da AD FS se l'utente che esegue l'autenticazione si trova all'interno della rete aziendale. Questa attestazione può essere passata ad Azure AD e consente di ignorare l'autenticazione a più fattori in base alla posizione di rete degli utenti. Vedere [Indirizzi IP attendibili per gli utenti federati](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) per informazioni su come determinare se questa funzionalità è attualmente abilitata in AD FS.

L'attestazione InsideCorporateNetwork non sarà più disponibile dopo che i domini saranno convertiti alla sincronizzazione degli hash delle password. In alternativa a questa funzionalità è possibile usare [Posizioni specifiche in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations).

Dopo aver configurato Posizioni specifiche, tutti i criteri di accesso condizionale configurati per includere o escludere le posizioni di rete, "Tutte le posizioni attendibili" o "Indirizzi IP attendibili MFA", devono essere aggiornati in modo da riflettere le nuove posizioni specifiche definite.

Per altre informazioni sulla condizione relativa alla posizione nell'accesso condizionale, vedere [Posizioni di accesso condizionale di Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD ibrido

L'aggiunta di un dispositivo ad Azure AD consente all'amministratore di creare regole di accesso condizionale che impongono l'uso di dispositivi che soddisfano gli standard di accesso in termini di sicurezza e conformità e agli utenti di accedere a un dispositivo usando un account aziendale o dell'istituto di istruzione anziché un account personale. La funzionalità Aggiunta ad Azure AD ibrido consente di aggiungere ad Azure AD i dispositivi aggiunti a un dominio di AD. L'ambiente federato potrebbe essere stato configurato con questa funzionalità.

Per assicurarsi che la funzionalità di aggiunta a un ambiente ibrido continui a funzionare per eventuali nuovi dispositivi aggiunti al dominio dopo che i domini sono stati convertiti alla sincronizzazione degli hash delle password, Azure AD Connect deve essere configurato in modo da sincronizzare con Azure AD gli account computer di Active Directory per i client Windows 10. Per gli account computer di Windows 7 e Windows 8, la funzionalità di aggiunta a un ambiente ibrido userà l'accesso Single Sign-On facile per registrare il computer in Azure AD e non sarà necessario sincronizzare gli account come per i dispositivi Windows 10. Sarà tuttavia necessario distribuire un file workplacejoin.exe aggiornato (tramite un file con estensione msi) a questi client di livello inferiore in modo che possano registrarsi usando l'accesso Single Sign-On facile. [Scaricare il file con estensione msi](https://www.microsoft.com/download/details.aspx?id=53554). 

Per altre informazioni, vedere [Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Personalizzazione

Un'organizzazione potrebbe aver definito [pagine di accesso di AD FS personalizzate](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) in modo da mostrare informazioni più pertinenti relative alle proprie attività. In tal caso, è consigliabile applicare [personalizzazioni simili alla pagina di accesso di AD Azure](https://docs.microsoft.com/azure/active-directory/customize-branding).

Anche se sono disponibili opzioni di personalizzazione simili, molto probabilmente si riscontreranno differenze dal punto di vista grafico. Può quindi essere opportuno segnalare le variazioni previste agli utenti finali.

> [!NOTE]
> L'aggiunta di informazioni personalizzate distintive dell'azienda è possibile solo se si è acquistata la licenza Premium o Basic di Azure AD o se si possiede una licenza di Office 365.

## <a name="planning-deployment-and-support"></a>Pianificazione della distribuzione e del supporto

### <a name="plan-the-maintenance-window"></a>Pianificare l'intervallo di manutenzione

Mentre il processo di conversione dei domini è di per sé relativamente rapido, Azure AD potrebbe inviare alcune richieste di autenticazione ai server AD FS per un periodo massimo di quattro ore dopo il completamento della conversione. Durante questo intervallo di quattro ore, e a seconda delle varie cache sul lato del servizio, è possibile che queste autenticazioni non vengano accettate da Azure AD. Gli utenti riceveranno così un errore perché non potranno eseguire l'autenticazione ad AD FS, ma Azure AD non accetterà più token generati dagli utenti poiché il trust federativo è stato rimosso.

> [!NOTE]
> Questa situazione interessa solo gli utenti che accedono ai servizi tramite un browser durante questo intervallo successivo alla conversione finché non viene cancellata la cache sul lato del servizio. I client legacy (Exchange ActiveSync, Outlook 2010 o 2013) non dovrebbero essere interessati da questa situazione poiché Exchange Online mantiene una cache delle proprie credenziali per un determinato periodo di tempo che viene usata per autenticare nuovamente l'utente in modo invisibile senza dover tornare al servizio AD FS. Non appena viene cancellata la cache, per ripetere l'autenticazione in modo invisibile per questi client vengono usate le credenziali archiviate nel dispositivo e pertanto gli utenti non dovrebbero ricevere richieste di password per effetto del processo di conversione dei domini. Diversamente dai client legacy, i client con autenticazione moderna (Office 2013 o 2016, iOS e Android) usano un token di aggiornamento valido per ottenere nuovi token per l'accesso continuo alle risorse anziché tornare al servizio AD FS. Non sono pertanto soggetti a richieste di password per effetto del processo di conversione dei domini e continueranno a funzionare senza richiedere operazioni di configurazione aggiuntive.

> [!IMPORTANT]
> Non arrestare l'ambiente di AD FS né rimuovere il trust della relying party Office 365 finché non si è verificato che tutti gli utenti si sono autenticati tramite il processo di autenticazione cloud.

### <a name="plan-for-rollback"></a>Pianificare per il rollback

Se si riscontra un problema importante che non può essere risolto rapidamente, è possibile decidere di eseguire il rollback della soluzione alla federazione. È importante pianificare le operazioni da eseguire se la distribuzione non viene completata come previsto. Se la conversione del dominio o degli utenti non riesce durante la distribuzione oppure si deve eseguire il rollback alla federazione, è necessario comprendere in che modo attenuare le eventuali interruzioni del servizio e ridurre l'impatto sugli utenti.

#### <a name="rolling-back"></a>Esecuzione del rollback

Consultare la documentazione relativa alla progettazione e alla distribuzione della federazione per i dettagli della specifica distribuzione. Il processo dovrebbe comprendere le attività seguenti:

* Conversione dei domini gestiti in domini federati tramite Convert-MSOLDomainToFederated 

* Se necessario, configurazione di altre regole per le attestazioni.

### <a name="plan-change-communications"></a>Pianificare le comunicazioni relative alle modifiche

Una parte importante del processo di pianificazione della distribuzione e del supporto consiste nell'assicurarsi che gli utenti finali vengano informati in modo proattivo riguardo alle modifiche introdotte, a ciò che potrebbe accedere o alle eventuali operazioni da eseguire. 

Dopo la distribuzione della sincronizzazione degli hash delle password e dell'accesso Single Sign-On facile, gli utenti finali avranno una diversa esperienza di accesso ad Office 365 e ad altre risorse associate autenticate tramite Azure AD. Gli utenti esterni alla rete vedranno ora solo la pagina di accesso ad Azure AD invece di essere reindirizzati alla pagina basata su form presentata dai server proxy applicazione Web con accesso all'esterno.

La pianificazione della strategia di comunicazione comprende diverse attività, incluse le seguenti:

* Notifica del rilascio imminente o dell'avvenuto rilascio di funzionalità tramite
  * Posta elettronica e altri canali di comunicazione interna
  * Supporti visivi, ad esempio poster
  * Comunicazioni live dei dirigenti o di altro tipo
* Designazione di chi personalizzerà e invierà le comunicazioni e di quando questo dovrà avvenire.

## <a name="implementing-your-solution"></a>Implementazione della soluzione

Ora che la soluzione è stata pianificata, si è pronti per l'implementazione. L'implementazione include i componenti seguenti:

1. Abilitazione della sincronizzazione dell'hash delle password

2. Preparazione dell'accesso Single Sign-On facile

3. Conversione del metodo di accesso alla sincronizzazione degli hash delle password e abilitazione dell'accesso Single Sign-On facile

### <a name="step-1--enable-password-hash-synchronization"></a>Passaggio 1: Abilitare la sincronizzazione degli hash delle password

Il primo passaggio per implementare questa soluzione consiste nell'abilitare la sincronizzazione degli hash delle password tramite la procedura guidata di Azure AD Connect. Sincronizzazione hash password è una funzionalità facoltativa che può essere abilitata negli ambienti che usano la federazione senza alcuna conseguenza sul flusso di autenticazione. In questo caso, Azure AD Connect inizierà a sincronizzare gli hash delle password senza influire sull'esperienza di accesso degli utenti che usano la federazione.

Per questo motivo, è consigliabile eseguire questo passaggio come un'attività preparatoria, ben prima di modificare il metodo di accesso ai domini. Ciò consentirà di avere tempo a sufficienza per verificare che la sincronizzazione degli hash delle password funzioni correttamente.

Per abilitare la sincronizzazione degli hash delle password:

   1. Nel server di Azure AD Connect avviare la procedura guidata e selezionare Configura.
   2. Selezionare Personalizzazione delle opzioni di sincronizzazione e quindi fare clic su Avanti.
   3. Nella schermata Connessione ad Azure AD specificare il nome utente e la password di un amministratore globale.
   4. Nella schermata Connessione delle directory fare clic su Avanti.
   5. Nella schermata Filtro di domini e unità organizzative fare clic su Avanti.
   6. Nella schermata Funzionalità facoltative selezionare Sincronizzazione hash password e fare clic su Avanti.
   ![Figura 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Fare clic su Avanti in tutte le schermate successive e quindi su Configura nell'ultima schermata.
   8. Azure AD Connect inizierà a sincronizzare gli hash delle password alla successiva sincronizzazione.

Dopo che la sincronizzazione degli hash delle password è stata abilitata, gli hash delle password per tutti gli utenti nell'ambito della sincronizzazione di Azure AD Connect verranno ridefiniti e scritti in Azure AD. A seconda del numero di utenti, questa operazione può richiedere da pochi minuti ad alcune ore.

Ai fini della pianificazione, è necessario stimare che in un'ora possono essere elaborati circa 20.000 utenti.

Per verificare che la sincronizzazione degli hash delle password funzioni correttamente, usare l'attività Risoluzione dei problemi nella procedura guidata di Azure AD Connect.

   1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione Esegui come amministratore.
   2. Eseguire Set-ExecutionPolicy RemoteSigned o Set-ExecutionPolicy Unrestricted.
   3. Avviare la procedura guidata di Azure AD Connect.
   4. Passare alla pagina Attività aggiuntive, selezionare Risoluzione dei problemi e fare clic su Avanti.
   5. Nella pagina Risoluzione dei problemi fare clic su Avvia per avviare il menu per la risoluzione dei problemi in PowerShell.
   6. Nel menu principale scegliere Troubleshoot password hash synchronization (Risolvere i problemi di sincronizzazione degli hash delle password).
   7. Nel sottomenu scegliere Password hash synchronization does not work at all (La sincronizzazione degli hash delle password non funziona).

Se si riscontrano problemi, usare le informazioni riportate in [questo articolo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) per risolverli.

### <a name="step-2--prepare-for-seamless-sso"></a>Passaggio 2: Eseguire le attività preliminari per l'accesso Single Sign-On facile

Per consentire agli utenti di usufruire dell'accesso Single Sign-On facile, è necessario aggiungere un URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory.

Per impostazione predefinita, il browser calcola automaticamente l'area corretta, Internet o Intranet, in base a un URL specifico. Ad esempio, "http://contoso/" esegue il mapping all'area Intranet, mentre "http://intranet.contoso.com/" esegue il mapping all'area Internet perché l'URL contiene un punto. I browser non invieranno ticket Kerberos a un endpoint del cloud, come l'URL di Azure AD, a meno che l'URL in questione non venga esplicitamente aggiunto all'area Intranet del browser.

Seguire questi [passaggi per implementare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) le modifiche necessarie nei dispositivi.

> [!IMPORTANT]
> L'introduzione di questa modifica non cambierà il modo in cui gli utenti accedono ad Azure AD. È tuttavia importante applicare questa configurazione a tutti i dispositivi prima di proseguire con il passaggio 3. Si noti inoltre che per accedere ai dispositivi a cui non è stata applicata questa configurazione gli utenti dovranno semplicemente immettere nome utente e password per l'accesso ad Azure AD.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>Passaggio 3: Convertire il metodo di accesso alla sincronizzazione degli hash delle password e abilitare l'accesso Single Sign-On facile

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>Opzione A: Passare dalla federazione alla sincronizzazione degli hash delle password con Azure AD Connect

Usare questo metodo quando AD FS è stato inizialmente configurato tramite Azure AD Connect. Non è possibile usare questo metodo se AD FS non è stato originariamente configurato con Azure AD Connect. Prima di tutto, **modificare il metodo di accesso utente**.

   1. Nel server di Azure AD Connect avviare la procedura guidata.
   2. Selezionare Cambia l'accesso utente e quindi fare clic su Avanti.
   ![Figura 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. Nella schermata **Connessione ad Azure AD** specificare il nome utente e la password di un **amministratore globale**.
   4. Nella schermata **Accesso utente** modificare l'impostazione dei pulsanti di opzione deselezionando Federazione tramite AD FS e selezionando Sincronizzazione hash password. Assicurarsi inoltre di selezionare la casella Non convertire gli account utente perché si tratta di un passaggio deprecato e verrà rimossa da una versione futura di AAD Connect. Selezionare anche Abilita Single Sign-On e quindi fare clic su **Avanti**.
   ![Figura 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > A partire da Azure AD Connect versione 1.1.880.0, la casella di controllo Accesso Single Sign-On facile è abilitata per impostazione predefinita.
   
   > [!IMPORTANT]
   > È possibile ignorare tranquillamente gli avvisi in cui viene segnalato che la conversione degli utenti e la sincronizzazione completa degli hash delle password sono passaggi obbligatori per la conversione dalla federazione all'autenticazione cloud. Si noti che questi passaggi non sono più obbligatori. Le versioni future di Azure AD Connect non includeranno più un'opzione per convertire gli utenti. Se questi avvisi continuano a essere visualizzati, verificare di eseguire la versione più recente di Azure AD Connect e di usare la versione più recente di questa guida. Per altre informazioni, vedere la sezione [Aggiornare Azure AD Connect](#_Update_Azure_AD).
   
   5. Nella schermata Abilita Single Sign-on immettere le credenziali dell'account di amministratore di dominio e quindi fare clic su Avanti.
   ![Figura 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Per abilitare l'accesso Single Sign-On facile è necessario usare le credenziali dell'amministratore di dominio perché il processo esegue le azioni seguenti che richiedono queste autorizzazioni elevate. Le credenziali dell'amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per abilitare la funzionalità e quindi eliminate al termine dell'operazione.
   >  * Un account computer denominato AZUREADSSOACC, che rappresenta Azure AD, viene creato in Active Directory (AD) locale.
   >  * La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
   >  * Vengono anche creati due nomi dell'entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.
   >  * Le credenziali dell'amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per abilitare la funzionalità e quindi eliminate al termine dell'operazione.
   
   6. Nella schermata Pronto per la configurazione verificare che la casella di controllo "Avvia il processo di sincronizzazione al termine della configurazione" sia selezionata. Selezionare quindi Configura.
   ![Figura 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > A questo punto, tutti i domini federati saranno passati all'autenticazione gestita che userà la sincronizzazione degli hash delle password come metodo di autenticazione.
       
   7. Aprire il portale di Azure AD, selezionare Azure Active Directory e quindi Azure AD Connect.
   8. Verificare che lo stato di Federazione sia disabilitato e che invece quelli di accesso Single Sign-on facile e di Sincronizzazione password siano abilitati.  
  ![Figura 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Proseguire con la sezione [Test e passaggi successivi](#testing-and-next-steps).
   
   > [!IMPORTANT]
   > Ignorare la sezione Opzione B: Passare dalla federazione alla sincronizzazione degli hash delle password con Azure AD Connect e PowerShell perché i passaggi descritti in questa sezione non sono applicabili al caso specifico.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>Opzione B: Passare dalla federazione alla sincronizzazione degli hash delle password con Azure AD Connect e PowerShell

Usare questa opzione quando la federazione non è stata inizialmente configurata tramite Azure AD Connect.

Nel corso di questo processo, si abiliterà l'accesso Single Sign-On facile e si convertiranno i domini da federati a gestiti.

   1. Nel server di Azure AD Connect avviare la procedura guidata.
   2. Selezionare Cambia l'accesso utente e quindi fare clic su Avanti. 
   3. Nella schermata Connessione ad Azure AD specificare il nome utente e la password di un amministratore globale.
   4. Nella schermata Accesso utente modificare l'impostazione dei pulsanti di opzione deselezionando Non configurare e selezionando Sincronizzazione hash password, quindi selezionare Abilita Single Sign-One e fare clic su Avanti.
   
   Prima della modifica: ![Figura 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Dopo la modifica:  
   ![Figura 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > A partire da Azure AD Connect versione 1.1.880.0, la casella di controllo Accesso Single Sign-On facile è abilitata per impostazione predefinita.
   
   5. Nella schermata Abilita Single Sign-on immettere le credenziali dell'account di amministratore di dominio e quindi fare clic su Avanti.
   
   > [!NOTE]
   > Per abilitare l'accesso Single Sign-On facile è necessario usare le credenziali dell'amministratore di dominio perché il processo esegue le azioni seguenti che richiedono queste autorizzazioni elevate. Le credenziali dell'amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per abilitare la funzionalità e quindi eliminate al termine dell'operazione.
   > * Un account computer denominato AZUREADSSOACC, che rappresenta Azure AD, viene creato in Active Directory (AD) locale.
   > * La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
   > * Vengono anche creati due nomi dell'entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.
   
   6. Nella schermata Pronto per la configurazione verificare che la casella di controllo "Avvia il processo di sincronizzazione al termine della configurazione" sia selezionata. Selezionare quindi Configura.
   ![Figura 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   Quando si seleziona Configura, l'accesso Single Sign-On facile verrà configurato in base al passaggio precedente. La configurazione della sincronizzazione degli hash delle password non verrà modificata perché è stata abilitata in precedenza.
   
   > [!IMPORTANT]
   > A questo punto non verranno apportate modifiche al modo in cui gli utenti eseguono l'accesso.  
   
   7. Nel portale di Azure AD verificare che lo stato di Federazione sia rimasto abilitato e che ora lo stato di Accesso Single Sign-On facile sia abilitato.
   ![Figura 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Convertire i domini da federati a gestiti

A questo punto, la federazione è ancora abilitata e operativa per i domini. Per proseguire la distribuzione, ogni dominio deve essere convertito da federato a gestito in modo da forzare l'autenticazione utente tramite la sincronizzazione degli hash delle password.

> [!IMPORTANT]
> Non è necessario convertire contemporaneamente tutti i domini. È possibile scegliere di iniziare con un dominio di test nel tenant di produzione o con il dominio che include il minor numero di utenti.

La conversione viene eseguita tramite il modulo Azure AD PowerShell.

   1. Aprire PowerShell e accedere ad Azure AD usando un account di amministratore globale.  
   2. Per convertire il primo dominio, usare il comando seguente:  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. Aprire il portale di Azure AD, selezionare Azure Active Directory e quindi Azure AD Connect.
   4. Verificare che il dominio sia stato convertito in dominio gestito con il comando seguente:
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Test e passaggi successivi

### <a name="test-authentication-with-phs"></a>Testare l'autenticazione con la sincronizzazione degli hash delle password

Quando il tenant usava la federazione, gli utenti venivano reindirizzati dalla pagina di accesso di Azure AD all'ambiente di AD FS. Ora che il tenant è configurato per l'uso della sincronizzazione degli hash delle password anziché della federazione, gli utenti non verranno reindirizzati ad AD FS ma eseguiranno l'accesso direttamente tramite la pagina di accesso di Azure AD.

Aprire Internet Explorer in modalità InPrivate per evitare l'attivazione automatica dell'accesso Single Sign-On facile e passare alla pagina di accesso di Office 365 ([https://portal.office.com](https://portal.office.com/)). Digitare l'UPN dell'utente e fare clic su Avanti. Assicurarsi di digitare l'UPN di un utente ibrido che è stato sincronizzato da Active Directory in locale e che in precedenza era federato. L'utente vedrà la schermata in cui digitare nome utente e password.

![Figura 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Figura 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

Dopo aver digitato la password, si verrà reindirizzati al portale di Office 365.

![Figura 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Testare l'accesso Single Sign-On facile

Accedere a un computer aggiunto al dominio che è connesso alla rete aziendale. Aprire Internet Explorer e passare a uno degli URL seguenti:  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (sostituire Contoso con il nome del proprio dominio).

L'utente verrà rapidamente reindirizzato alla pagina di accesso di Azure AD e vedrà il messaggio "Tentativo di accesso per conto dell'utente". Non dovrà quindi ricevere la richiesta di specificare un nome utente o una password.

![Figura 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

L'utente verrà quindi reindirizzato al pannello di accesso e l'accesso verrà eseguito automaticamente:

> [!NOTE]
> L'accesso Single Sign-On facile funziona nei servizi di Office 365 che supportano l'hint di dominio, ad esempio myapps.microsoft.com/contoso.com. Il portale di Office 365 (portal.office.com) attualmente non supporta l'hint di dominio e pertanto gli utenti dovranno digitare il proprio UPN. Dopo che si è specificato un UPN, la funzionalità di accesso Single Sign-On facile può recuperare il ticket Kerberos per conto dell'utente e consentirgli di accedere senza digitare una password. 

> [!TIP]
> Per un'esperienza di accesso Single Sign-On migliorata, valutare l'opportunità di distribuire [Aggiunta ad Azure AD ibrido in Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction).

### <a name="removal-of-the-relying-party-trust"></a>Rimozione del trust della relying party

Dopo aver verificato che tutti gli utenti e i client eseguano correttamente l'autenticazione tramite Azure AD, si può rimuovere tranquillamente il trust della relying party Office 365.

Se AD FS non viene usato per altri scopi (altri trust di relying party configurati), è ora possibile rimuovere tranquillamente le autorizzazioni di AD FS.

### <a name="rollback"></a>Rollback

Se si riscontra un problema importante che non può essere risolto rapidamente, è possibile decidere di eseguire il rollback della soluzione alla federazione.

Consultare la documentazione relativa alla progettazione e alla distribuzione della federazione per i dettagli della specifica distribuzione. Il processo dovrebbe comprendere le attività seguenti:

* Conversione dei domini gestiti in domini federati tramite Convert-MSOLDomainToFederated

* Se necessario, configurazione di altre regole per le attestazioni.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>Abilitare la sincronizzazione degli aggiornamenti di userPrincipalName

In genere, gli aggiornamenti dell'attributo UserPrincipalName usando il servizio di sincronizzazione locale vengono bloccati, a meno che siano rispettate entrambe le condizioni seguenti:

* L'utente è gestito (non federato).

* All'utente non è stata assegnata una licenza.

Per istruzioni su come verificare o abilitare questa funzionalità, vedere l'articolo seguente:

[Sincronizzare gli aggiornamenti di userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>risoluzione dei problemi

Il team di supporto della propria organizzazione dovrebbe essere in grado di comprendere come risolvere i problemi di autenticazione che si verificano durante o dopo la conversione dei domini da federati a gestiti. Usare la documentazione seguente per consentire al team di supporto di acquisire familiarità con i passaggi comuni per la risoluzione dei problemi e le azioni appropriate che possono essere utili per isolare e risolvere un problema.

[Risolvere i problemi relativi alla sincronizzazione degli hash delle password di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Risolvere i problemi relativi all'accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Rinnovare la chiave di decrittografia di Kerberos per l'accesso Single Sign-On facile

È importante rinnovare spesso la chiave di decrittografia di Kerberos dell'account computer AZUREADSSOACC, che rappresenta Azure AD, creato nella foresta di AD locale. La chiave di decrittografia di Kerberos dovrebbe essere rinnovata almeno ogni 30 giorni in modo da allinearsi alla frequenza con cui i membri del dominio di Active Directory inviano gli aggiornamenti delle password. Poiché nessun dispositivo associato è collegato all'oggetto account computer AZUREADSSOACC, il rinnovo deve essere eseguito manualmente.

Per rinnovare la chiave di decrittografia di Kerberos, seguire questi passaggi nel server locale in cui si esegue Azure AD Connect.

[Come è possibile rinnovare la chiave di decrittografia di Kerberos dell'account computer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>Passaggi successivi

- [Concetti relativi alla progettazione per Azure AD Connect](plan-connect-design-concepts.md)
- [Scegliere l'autenticazione appropriata](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologie supportate](plan-connect-design-concepts.md)
