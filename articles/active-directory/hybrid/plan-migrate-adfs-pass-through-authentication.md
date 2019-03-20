---
title: "Azure AD Connect: Eseguire la migrazione dalla federazione all'autenticazione pass-through per Azure Active Directory | Microsoft Docs"
description: Questo articolo include informazioni sulla migrazione di un ambiente ibrido di gestione delle identità dalla federazione all'autenticazione pass-through.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf0bb51470272099ed2824d0450082f93fe65f14
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076463"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Eseguire la migrazione dalla federazione all'autenticazione pass-through per Azure Active Directory

Questo articolo descrive come spostare i domini dell'organizzazione da Active Directory Federation Services (AD FS) all'autenticazione pass-through.

È possibile [scaricare questo articolo](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Prerequisiti per la migrazione all'autenticazione pass-through

Per passare dall'uso di AD FS a quello dell'autenticazione pass-through è necessario che i prerequisiti seguenti siano soddisfatti.

### <a name="update-azure-ad-connect"></a>Aggiornare Azure AD Connect

Per completare i passaggi necessari per eseguire la migrazione all'uso dell'autenticazione pass-through, è necessario [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 o versione successiva. Azure AD Connect 1.1.819.0 contiene modifiche significative alla modalità di conversione delle informazioni di accesso. Il tempo complessivo per la migrazione da AD FS all'autenticazione cloud, che in precedenza poteva richiedere anche ore, è ridotto ad alcuni minuti.

> [!IMPORTANT]
> In documenti, strumenti e blog non aggiornati potrebbe essere indicato che la conversione degli utenti è obbligatoria quando si convertono i domini dall'identità federata all'identità gestita. La *conversione degli utenti* non è più obbligatoria. Microsoft sta aggiornando la documentazione e gli strumenti in base a questa modifica.

Per aggiornare Azure AD Connect, completare i passaggi elencati in [Azure AD Connect: Eseguire l'aggiornamento alla versione più recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Pianificare il numero e il posizionamento degli agenti di autenticazione

L'autenticazione pass-through richiede la distribuzione di agenti leggeri nel server di Azure AD Connect e nel computer locale che esegue Windows Server. Per ridurre la latenza, installare gli agenti il più vicino possibile ai controller di dominio di Active Directory.

Per la maggior parte dei clienti, due o tre agenti di autenticazione sono sufficienti per fornire disponibilità elevata e la capacità necessaria. Un tenant può avere un massimo di 12 agenti registrati. Il primo agente viene installato sempre nel server di Azure AD Connect. Per informazioni sulle limitazioni e sulle opzioni di distribuzione degli agenti, vedere [Autenticazione pass-through di Azure AD: Limitazioni correnti](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations).

### <a name="plan-the-migration-method"></a>Pianificare il metodo di migrazione

Per eseguire la migrazione dalla gestione delle identità federate all'autenticazione pass-through e all'accesso Single Sign-On facile, è possibile scegliere tra due metodi. La scelta del metodo dipende dal modo in cui l'istanza di AD FS è stata originariamente configurata.

* **Azure AD Connect**. Se originariamente AD FS è stato configurato tramite Azure AD Connect, *è necessario* passare all'autenticazione pass-through usando la procedura guidata di Azure AD Connect.

   ‎Quando si modifica il metodo di accesso dell'utente, Azure AD Connect esegue automaticamente il cmdlet **Set-MsolDomainAuthentication**. Azure AD Connect annulla automaticamente la federazione di tutti i domini federati verificati nel tenant di Azure AD.

   > [!NOTE]
   > Al momento, se Azure AD Connect è stato originariamente usato per configurare AD FS, non è possibile evitare l'annullamento della federazione di tutti i domini nel tenant quando si imposta l'autenticazione pass-through come metodo di accesso utente.
‎
* **Azure AD Connect con PowerShell**. È possibile usare questo metodo solo se AD FS non è stato originariamente configurato tramite Azure AD Connect. Per questa opzione, è comunque necessario modificare il metodo di accesso utente tramite la procedura guidata di Azure AD Connect. La differenza principale di questa opzione è che la procedura guidata non esegue automaticamente il cmdlet **Set-MsolDomainAuthentication**. Con questa opzione, si ha pieno controllo su quali domini vengono convertiti e in quale ordine.

Per comprendere quale metodo è preferibile usare, completare i passaggi nelle sezioni seguenti.

#### <a name="verify-current-user-sign-in-settings"></a>Verificare le impostazioni di accesso utente correnti

1. Accedere al [portale di Azure AD](https://aad.portal.azure.com/) usando un account amministratore globale.
2. Nella sezione **Accesso utente** verificare le impostazioni di questi campi:
   * **Federazione** deve essere impostato su **Abilitata**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Disabilitato**.
   * **Autenticazione pass-through** deve essere impostato su **Disabilitato**.

   ![Screenshot delle impostazioni nella sezione Accesso utente di Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Verificare come è stata configurata la federazione

1. Nel server di Azure AD Connect aprire Azure AD Connect. Selezionare **Configura**.
2. Nella pagina **Attività aggiuntive** selezionare **Visualizza la configurazione corrente** e quindi fare clic su **Avanti**.<br />
 
   ![Screenshot dell'opzione Visualizza la configurazione corrente selezionata nella pagina Attività aggiuntive](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Nella pagina **Verifica della soluzione** scorrere fino a visualizzare **Active Directory Federation Services (AD FS)**.<br />

   * Se in questa sezione viene visualizzata la configurazione di AD FS, si può presupporre con sicurezza che AD FS sia stato originariamente configurato usando Azure AD Connect. È possibile convertire i domini dall'identità federata all'identità gestita usando l'opzione **Cambia l'accesso utente** di Azure AD Connect. Per altre informazioni su questo processo, vedere la sezione **Opzione A: Configurare l'autenticazione pass-through con Azure AD Connect**.
   * Se AD FS non è elencato nelle impostazioni correnti, è necessario convertire manualmente i domini dall'identità federata all'identità gestita usando PowerShell. Per altre informazioni su questo processo, vedere la sezione **Opzione B: Passare dalla federazione all'autenticazione pass-through con Azure AD Connect e PowerShell**.

### <a name="document-current-federation-settings"></a>Documentare le impostazioni di federazione correnti

Per trovare le impostazioni di federazione correnti, eseguire il cmdlet **Get-MsolDomainFederationSettings**:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Esempio:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verificare le impostazioni che potrebbero essere state personalizzate per la documentazione relativa alla progettazione e alla distribuzione della federazione. Cercare in particolare le personalizzazioni in **PreferredAuthenticationProtocol**, **SupportsMfa** e **PromptLoginBehavior**.

Per altre informazioni, vedere questi articoli:

* [Supporto del parametro prompt=login di AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se **SupportsMfa** è impostato su **True**, si sta usando una soluzione di autenticazione a più fattori locale per inserire una richiesta di verifica come secondo fattore nel flusso di autenticazione utente. Questa configurazione non funziona più per gli scenari di autenticazione di Azure AD. 
>
> In alternativa, usare il servizio Azure Multi-Factor Authentication basato sul cloud per eseguire la stessa funzione. Valutare attentamente i requisiti di autenticazione a più fattori prima di continuare. Prima di convertire i domini, assicurarsi di comprendere come usare Azure Multi-Factor Authentication, le implicazioni relative alla gestione delle licenze e il processo di registrazione utente.

#### <a name="back-up-federation-settings"></a>Eseguire il backup delle impostazioni di federazione

Anche se non è possibile apportare modifiche ad altre relying party nella farm AD FS durante i processi descritti in questo articolo, è consigliabile avere una copia di backup valida corrente della farm AD FS per un eventuale ripristino. È possibile creare un backup valido corrente usando lo [strumento di ripristino rapido di AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) fornito gratuitamente da Microsoft. Questo strumento può essere usato per eseguire il backup di AD FS e per ripristinare una farm esistente o crearne una nuova.

Se si sceglie di non usare lo strumento di ripristino rapido di AD FS, è necessario almeno esportare il trust della relying party Piattaforma delle identità di Microsoft Office 365 e le eventuali regole personalizzate per le attestazioni aggiunte. È possibile esportare il trust della relying party e le regole associate per le attestazioni usando l'esempio di PowerShell seguente:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerazioni relative alla distribuzione e all'uso di AD FS

Questa sezione include considerazioni sulla distribuzione e dettagli sull'uso di AD FS.

### <a name="current-ad-fs-use"></a>Uso di AD FS corrente

Prima di poter procedere alla conversione dell'identità da federata a gestita, è consigliabile esaminare con attenzione il modo in cui si usa attualmente AD FS per Azure AD, Office 365 e altre applicazioni (trust di relying party). In particolare, considerare gli scenari descritti nella tabella seguente:

| Se | Allora |
|-|-|
| Si prevede di continuare a usare AD FS con altre applicazioni (diverse da Azure AD e Office 365). | Dopo la conversione dei domini, si useranno sia AD FS che Azure AD. Considerare l'esperienza utente. È possibile che in alcuni scenari gli utenti debbano eseguire due volte l'autenticazione: una per accedere ad Azure AD (dove un utente ottiene l'accesso Single Sign-On ad altre applicazioni, come Office 365) e l'altra per le applicazioni ancora associate ad AD FS come trust della relying party. |
| L'istanza di AD FS è un componente altamente personalizzabile e si basa su specifiche impostazioni di personalizzazione definite nel file onload.js, ad esempio se si è modificata l'esperienza di accesso per consentire agli utenti di usare il proprio nome solo nel formato **SamAccountName**, invece di un nome di entità utente (UPN), o se l'organizzazione visualizza informazioni personalizzate distintive dell'azienda. Il file onload.js non può essere duplicato in Azure AD. | Prima di continuare, è necessario verificare che Azure AD possa soddisfare i requisiti di personalizzazione corrente. Per altre informazioni e indicazioni, vedere le sezioni relative alla personalizzazione di AD FS e all'uso di informazioni personalizzate distintive dell'azienda in AD FS.|
| Si usa AD FS per bloccare le versioni precedenti dei client di autenticazione.| Considerare la possibilità di sostituire i controlli di AD FS che bloccano le versioni precedenti dei client di autenticazione usando una combinazione di [controlli di accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) e [regole di accesso client di Exchange Online](https://aka.ms/EXOCAR). |
| Si richiede agli utenti di eseguire l'autenticazione a più fattori in una soluzione server di autenticazione a più fattori locale quando gli utenti eseguono l'autenticazione ad AD FS.| In un dominio con identità gestite, non è possibile inserire una richiesta di autenticazione a più fattori tramite la soluzione di autenticazione a più fattori locale nel flusso di autenticazione. È tuttavia possibile usare il servizio Azure Multi-Factor Authentication per l'autenticazione a più fattori dopo aver convertito il dominio.<br /><br /> Se gli utenti attualmente non usano Azure Multi-Factor Authentication, è necessario un unico passaggio di registrazione utente. È necessario eseguire le opportune operazioni preliminari e comunicare agli utenti la registrazione pianificata. |
| Per controllare l'accesso a Office 365, si usano attualmente i criteri di controllo di accesso (regole AuthZ) in AD FS.| Valutare l'opportunità di sostituire i criteri con i [criteri di accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) e le [regole di accesso client di Exchange Online](https://aka.ms/EXOCAR) equivalenti di Azure AD.|

### <a name="common-ad-fs-customizations"></a>Personalizzazioni di AD FS comuni

Questa sezione descrive le personalizzazioni di AD FS comuni.

#### <a name="insidecorporatenetwork-claim"></a>Attestazione InsideCorporateNetwork

AD FS rilascia l'attestazione **InsideCorporateNetwork** se l'utente che esegue l'autenticazione si trova all'interno della rete aziendale. Questa attestazione può quindi essere passata ad Azure AD. L'attestazione viene usata per ignorare l'autenticazione a più fattori basata sul percorso di rete dell'utente. Per informazioni su come determinare se questa funzionalità è attualmente disponibile in AD FS, vedere [Indirizzi IP attendibili per utenti federati](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

L'attestazione **InsideCorporateNetwork** non è disponibile dopo che i domini sono stati convertiti all'autenticazione pass-through. In alternativa a questa funzionalità è possibile usare [Posizioni specifiche in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations).

Dopo aver configurato le posizioni specifiche, è necessario aggiornare tutti i criteri di accesso condizionale configurati per includere o escludere i valori **Tutte le posizioni attendibili** o **Indirizzi IP attendibili MFA** di rete in modo da riflettere le nuove posizioni specifiche.

Per altre informazioni sulla condizione relativa alla **posizione** nell'accesso condizionale, vedere [Posizioni di accesso condizionale di Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD ibrido

Quando si aggiunge un dispositivo ad Azure AD, è possibile creare regole di accesso condizionale che obbligano i dispositivi a rispettare gli standard di accesso relativi a sicurezza e conformità. Gli utenti possono anche accedere a un dispositivo usando un account aziendale o dell'istituto di istruzione invece di un account personale. Quando si usano dispositivi aggiunti ad Azure AD ibrido, è possibile aggiungere ad Azure AD i dispositivi aggiunti a un dominio di Active Directory. L'ambiente federato potrebbe essere stato configurato per usare questa funzionalità.

Per assicurarsi che la funzionalità di aggiunta a un ambiente ibrido continui a funzionare per eventuali dispositivi aggiunti al dominio dopo che i domini sono stati convertiti all'autenticazione pass-through, per i client Windows 10 è necessario usare Azure AD Connect per sincronizzare con Azure AD gli account computer di Active Directory.

Per gli account computer di Windows 8 e Windows 7, la funzionalità di aggiunta a un ambiente ibrido usa l'accesso Single Sign-On facile per registrare il computer in Azure AD. Non è necessario sincronizzare gli account computer di Windows 8 e Windows 7 come si fa per i dispositivi Windows 10. È tuttavia necessario distribuire un file workplacejoin.exe aggiornato (tramite un file con estensione msi) nei client Windows 8 e Windows 7 in modo che possano registrarsi usando l'accesso Single Sign-On facile. [Scaricare il file con estensione msi](https://www.microsoft.com/download/details.aspx?id=53554).

Per altre informazioni, vedere [Configurare i dispositivi aggiunti ad Azure AD ibrido](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Personalizzazione

Se l'organizzazione [ha personalizzato le pagine di accesso di AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) per visualizzare informazioni più pertinenti all'organizzazione, valutare l'opportunità di eseguire [personalizzazioni simili nella pagina di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Anche se sono disponibili opzioni di personalizzazione simili, molto probabilmente dopo la conversione si riscontreranno nelle pagine di accesso differenze dal punto di vista grafico. Potrebbe essere necessario fornire informazioni sulle modifiche previste nelle comunicazioni agli utenti.

> [!NOTE]
> L'aggiunta di informazioni personalizzate distintive dell'organizzazione è possibile solo se si acquista la licenza Premium o Basic di Azure Active Directory o se si possiede una licenza di Office 365.

## <a name="plan-for-smart-lockout"></a>Pianificazione per il blocco intelligente

La funzionalità di blocco intelligente di Azure AD protegge dagli attacchi di forza bruta. Il blocco intelligente impedisce che un account di Active Directory locale venga bloccato quando si usa l'autenticazione pass-through e sono impostati criteri di gruppo per il blocco degli account in Active Directory.

Per altre informazioni, vedere [Blocco intelligente di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="plan-deployment-and-support"></a>Pianificare la distribuzione e il supporto

Completare le attività descritte in questa sezione per pianificare la distribuzione e il supporto.

### <a name="plan-the-maintenance-window"></a>Pianificare l'intervallo di manutenzione

Anche se il processo di conversione dei domini è relativamente rapido, Azure AD potrebbe continuare a inviare alcune richieste di autenticazione ai server di AD FS per un periodo massimo di quattro ore dopo il completamento della conversione. Durante questo periodo di quattro ore, e a seconda delle varie cache sul lato del servizio, è possibile che Azure AD non accetti queste autenticazioni e che venga restituito un errore. L'utente può ancora eseguire l'autenticazione con AD FS, ma Azure AD non accetta più il token rilasciato dell'utente perché tale trust di federazione è stato rimosso.

Questo problema interessa solo gli utenti che accedono ai servizi tramite un Web browser durante l'intervallo successivo alla conversione prima che venga cancellata la cache sul lato del servizio. I client legacy (Exchange ActiveSync, Outlook 2010 o 2013) non dovrebbero essere interessati perché Exchange Online conserva una cache delle credenziali per un determinato periodo di tempo. La cache viene usata per autenticare di nuovo l'utente senza avvisare. L'utente non deve tornare ad AD FS. Le credenziali archiviate nel dispositivo per questi client vengono usate per autenticarli di nuovo senza avvisare dopo che il contenuto di questa cache viene cancellato. Non è previsto che gli utenti ricevano richieste di password come risultato del processo di conversione dei domini.

I client di autenticazione moderni (Office 2016 e Office 2013, iOS e app Android) usano un token di aggiornamento valido per ottenere nuovi token per l'accesso continuo alle risorse invece di tornare ad AD FS. Questi client non sono soggetti a richieste di password risultanti dal processo di conversione dei domini. I client continueranno a funzionare senza alcuna configurazione aggiuntiva.

> [!IMPORTANT]
> Non arrestare l'ambiente di AD FS né rimuovere il trust della relying party Office 365 finché non si è verificato che tutti gli utenti possano essere autenticati usando il processo di autenticazione cloud.

### <a name="plan-for-rollback"></a>Pianificare per il rollback

Se si riscontra un problema importante che non può essere risolto rapidamente, è possibile decidere di eseguire il ripristino della soluzione alla federazione. È importante pianificare le operazioni da eseguire se la distribuzione non viene implementata come previsto. Se la conversione del dominio o degli utenti non riesce durante la distribuzione oppure se si deve eseguire il ripristino alla federazione, è necessario comprendere in che modo attenuare le eventuali interruzioni del servizio e ridurre l'effetto sugli utenti.

#### <a name="to-roll-back"></a>Per eseguire il ripristino dello stato precedente

Per pianificare il ripristino dello stato precedente, vedere la documentazione relativa alla progettazione e alla distribuzione della federazione per i dettagli della specifica distribuzione. Il processo deve includere queste attività:

* Conversione dei domini gestiti in domini federati tramite il cmdlet **Convert-MSOLDomainToFederated**.
* Se necessario, configurazione di altre regole per le attestazioni.

### <a name="plan-communications"></a>Pianificare le comunicazioni

Una parte importante del processo di pianificazione della distribuzione e del supporto consiste nell'assicurarsi che gli utenti vengano informati in modo proattivo riguardo alle modifiche imminenti. Gli utenti devono sapere in anticipo che cosa potrebbe verificarsi e che cosa devono fare.

Dopo la distribuzione dell'autenticazione pass-through e dell'accesso Single Sign-On facile, gli utenti hanno una diversa esperienza di accesso a Office 365 e ad altre risorse autenticate tramite Azure AD. Gli utenti che sono all'esterno della rete vedono solo la pagina di accesso di Azure AD. Questi utenti non vengono reindirizzati alla pagina basata su form presentata dai server proxy applicazione Web con accesso all'esterno.

Includere gli elementi seguenti nella strategia di comunicazione:

* Notificare agli utenti il rilascio imminente o l'avvenuto rilascio di funzionalità usando:
   * Posta elettronica e altri canali di comunicazione interna.
   * Oggetti visivi, ad esempio poster.
   * Comunicazioni live, dei dirigenti o di altro tipo.
* Designare chi personalizzerà e invierà le comunicazioni e quando questo dovrà avvenire.

## <a name="implement-your-solution"></a>Implementare la soluzione

Ora che la soluzione è stata pianificata, è possibile implementarla. L'implementazione consiste nelle operazioni seguenti:

* Esecuzione delle attività preliminari per l'accesso Single Sign-On facile.
* Conversione del metodo di accesso all'autenticazione pass-through e abilitazione dell'accesso Single Sign-On facile.

### <a name="step-1-prepare-for-seamless-sso"></a>Passaggio 1: Eseguire le attività preliminari per l'accesso Single Sign-On facile

Per consentire agli utenti di usufruire dell'accesso Single Sign-On facile, è necessario aggiungere un URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory.

Per impostazione predefinita, i Web browser calcolano automaticamente l'area corretta, Internet o Intranet, in base a un URL. Ad esempio, **http:\/\/contoso/** viene associato all'area Intranet e **http:\/\/intranet.contoso.com** viene associato all'area Internet (perché l'URL contiene un punto). I browser inviano ticket Kerberos a un endpoint del cloud, come l'URL di Azure AD, solo se l'URL in questione viene esplicitamente aggiunto all'area Intranet del browser.

Completare questi passaggi per [implementare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) le modifiche necessarie nei dispositivi.

> [!IMPORTANT]
> L'introduzione di questa modifica non cambia il modo in cui gli utenti accedono ad Azure AD. È tuttavia importante applicare questa configurazione a tutti i dispositivi prima di proseguire. Per accedere ai dispositivi a cui non è stata applicata questa configurazione, gli utenti devono immettere un nome utente e una password per l'accesso ad Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Passaggio 2: Convertire il metodo di accesso all'autenticazione pass-through e abilitare l'accesso Single Sign-On facile

Per convertire il metodo di accesso all'autenticazione pass-through e abilitare l'accesso Single Sign-On facile sono disponibili due opzioni.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Opzione A: Configurare l'autenticazione pass-through con Azure AD Connect

Usare questo metodo se si è inizialmente configurato l'ambiente di AD FS tramite Azure AD Connect. In *caso contrario*, questo metodo non può essere usato.

> [!IMPORTANT]
> Al termine della procedura seguente, tutti i domini vengono convertiti dall'identità federata all'identità gestita. Per altre informazioni, vedere [Pianificare il metodo di migrazione](#plan-the-migration-method).

Prima di tutto, cambiare il metodo di accesso:

1. Nel server di Azure AD Connect avviare la procedura guidata di Azure AD Connect.
2. Selezionare **Cambia l'accesso utente** e quindi fare clic su **Avanti**. 
3. Nella pagina **Connessione ad Azure AD** specificare il nome utente e la password di un account amministratore globale.
4. Nella pagina **Accesso utente** selezionare il pulsante **Autenticazione pass-through**, quindi selezionare **Abilita Single Sign-On**  e infine fare clic su **Avanti**.
5. Nella pagina **Abilita Single Sign-on** immettere le credenziali dell'account amministratore di dominio e quindi fare clic su **Avanti**.

   > [!NOTE]
   > Le credenziali dell'account amministratore di dominio sono necessarie per abilitare l'accesso Single Sign-on facile. Il processo esegue le operazioni descritte di seguito, che richiedono queste autorizzazioni elevate. Le credenziali dell'account amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per attivare la funzionalità. Al termine del processo, le credenziali vengono rimosse.
   >
   > 1. Un account computer denominato AZUREADSSOACC, che rappresenta Azure AD, viene creato nell'istanza di Active Directory locale.
   > 2. La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
   > 3. Vengono creati due nomi di entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.

6. Nella pagina **Pronto per la configurazione** verificare che la casella di controllo **Avvia il processo di sincronizzazione al termine della configurazione** sia selezionata. Selezionare quindi **Configura**.<br />

   ![Screenshot della pagina Pronto per la configurazione](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Nel portale di Azure AD selezionare **Azure Active Directory** e quindi **Azure AD Connect**.
8. Verificare le impostazioni di questi campi:
   * **Federazione** deve essere impostato su **Disabilitato**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Abilitata**.
   * **Autenticazione pass-through** deve essere impostato su **Abilitata**.<br />

   ![Screenshot che mostra le impostazioni nella sezione Accesso utente](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Distribuire quindi i metodi di autenticazione aggiuntivi:

1. Nel portale di Azure scegliere **Azure Active Directory** > **Azure AD Connect** e quindi selezionare **Autenticazione pass-through**.
2. Nella pagina **Autenticazione pass-through** selezionare il pulsante **Scarica**.
3. Nella pagina **Scaricare l'agente** selezionare **Accetta le condizioni e scarica**.

   Viene avviato il download degli agenti di autenticazione aggiuntivi. Installare l'agente di autenticazione secondario in un server aggiunto al dominio. 

   > [!NOTE]
   > Il primo agente viene sempre installato nel server di Azure AD Connect come parte delle modifiche di configurazione apportate nella sezione **Accesso utente** dello strumento Azure AD Connect. Installare eventuali altri agenti di autenticazione in un server separato. È consigliabile avere due o tre agenti di autenticazione aggiuntivi disponibili. 

4. Eseguire l'installazione degli agenti di autenticazione. Durante l'installazione è necessario specificare le credenziali di un account amministratore globale.

   ![Screenshot che mostra il pulsante di installazione nella pagina Microsoft Azure AD Connect Authentication Agent Package](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Screenshot che mostra la pagina di accesso](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Dopo aver installato l'agente di autenticazione, è possibile tornare alla pagina relativa all'integrità dell'agente di autenticazione pass-through per controllare lo stato degli agenti aggiuntivi.

Proseguire con la sezione [Test e passaggi successivi](#testing-and-next-steps).

> [!IMPORTANT]
> Ignorare la sezione **Opzione B: Passare dalla federazione all'autenticazione pass-through con Azure AD Connect e PowerShell**. I passaggi in questa sezione non sono applicabili se si è scelta l'opzione A per convertire il metodo di accesso all'autenticazione pass-through e abilitare l'accesso Single Sign-On facile. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Opzione B: Passare dalla federazione all'autenticazione pass-through con Azure AD Connect e PowerShell

Usare questa opzione se non si sono inizialmente configurati i domini federati tramite Azure AD Connect.

Prima di tutto, abilitare l'autenticazione pass-through:

1. Nel server di Azure AD Connect avviare la procedura guidata di Azure AD Connect.
2. Selezionare **Cambia l'accesso utente** e quindi fare clic su **Avanti**.
3. Nella pagina **Connessione ad Azure AD** specificare il nome utente e la password di un account amministratore globale.
4. Nella pagina **Accesso utente** selezionare il pulsante **Autenticazione pass-through**. Selezionare **Abilita Single Sign-On** e quindi fare clic su **Avanti**.
5. Nella pagina **Abilita Single Sign-on** immettere le credenziali dell'account amministratore di dominio e quindi fare clic su **Avanti**.

   > [!NOTE]
   > Le credenziali dell'account amministratore di dominio sono necessarie per abilitare l'accesso Single Sign-on facile. Il processo esegue le operazioni descritte di seguito, che richiedono queste autorizzazioni elevate. Le credenziali dell'account amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per attivare la funzionalità. Al termine del processo, le credenziali vengono rimosse.
   > 
   > 1. Un account computer denominato AZUREADSSOACC, che rappresenta Azure AD, viene creato nell'istanza di Active Directory locale.
   > 2. La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
   > 3. Vengono creati due nomi di entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.

6. Nella pagina **Pronto per la configurazione** verificare che la casella di controllo **Avvia il processo di sincronizzazione al termine della configurazione** sia selezionata. Selezionare quindi **Configura**.<br />

   ‎![Screenshot che mostra la pagina Pronto per la configurazione e il pulsante Configura](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Quando si seleziona **Configura**, si verifica quanto segue:

   1. Viene installato il primo agente di autenticazione pass-through.
   2. Viene abilitata la funzionalità di autenticazione pass-through.
   3. Viene abilitato l'accesso Single Sign-On facile.

7. Verificare le impostazioni di questi campi:
   * **Federazione** deve essere impostato su **Abilitata**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Abilitata**.
   * **Autenticazione pass-through** deve essere impostato su **Abilitata**.
   
   ![Screenshot che mostra le impostazioni nella sezione Accesso utente](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Selezionare **Autenticazione pass-through** e verificare che lo stato sia **Attivo**.<br />
   
   Se l'agente di autenticazione non è attivo, completare alcuni [passaggi per la risoluzione dei problemi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) prima di continuare con il processo di conversione dei domini nel passaggio successivo. Se si convertono i domini prima di verificare che gli agenti di autenticazione pass-through siano installati correttamente e che il relativo stato risulti **Attivo** nel portale di Azure, si rischia di causare un'interruzione del servizio di autenticazione.

Distribuire quindi gli agenti di autenticazione aggiuntivi:

1. Nel portale di Azure scegliere **Azure Active Directory** > **Azure AD Connect** e quindi selezionare **Autenticazione pass-through**.
2. Nella pagina **Autenticazione pass-through** selezionare il pulsante **Scarica**. 
3. Nella pagina **Scaricare l'agente** selezionare **Accetta le condizioni e scarica**.
 
   Viene avviato il download dell'agente di autenticazione. Installare l'agente di autenticazione secondario in un server aggiunto al dominio.

   > [!NOTE]
   > Il primo agente viene sempre installato nel server di Azure AD Connect come parte delle modifiche di configurazione apportate nella sezione **Accesso utente** dello strumento Azure AD Connect. Installare eventuali altri agenti di autenticazione in un server separato. È consigliabile avere due o tre agenti di autenticazione aggiuntivi disponibili.
 
4. Eseguire l'installazione degli agenti di autenticazione. Durante l'installazione è necessario specificare le credenziali di un account amministratore globale.<br />

   ![Screenshot che mostra il pulsante di installazione nella pagina Microsoft Azure AD Connect Authentication Agent Package](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Screenshot che mostra la pagina di accesso](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Dopo aver installato l'agente di autenticazione, è possibile tornare alla pagina relativa all'integrità dell'agente di autenticazione pass-through per controllare lo stato degli agenti aggiuntivi.

A questo punto, l'autenticazione federata è ancora attiva e operativa per i domini. Per continuare con la distribuzione, è necessario convertire ogni dominio dall'identità federata all'identità gestita per consentire alla funzionalità di autenticazione pass-through di iniziare a elaborare le richieste di autenticazione per il dominio.

Non è necessario convertire tutti i domini nello stesso momento. È possibile scegliere di iniziare con un dominio di test nel tenant di produzione oppure con il dominio che ha il minor numero di utenti.

Completare la conversione usando il modulo Azure AD PowerShell:

1. In PowerShell accedere ad Azure AD usando un account amministratore globale.
2. Per convertire il primo dominio, usare il comando seguente:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Nel portale di Azure scegliere **Azure Active Directory** > **Azure AD Connect**.
4. Dopo aver convertito tutti i domini federati, verificare le impostazioni di questi campi:
   * **Federazione** deve essere impostato su **Disabilitato**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Abilitata**.
   * **Autenticazione pass-through** deve essere impostato su **Abilitata**.<br />

   ![Screenshot che mostra le impostazioni nella sezione Accesso utente](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Test e passaggi successivi

Eseguire le attività seguenti per verificare l'autenticazione pass-through e completare il processo di conversione.

### <a name="test-pass-through-authentication"></a>Testare l'autenticazione pass-through 

Quando il tenant usava l'identità federata, gli utenti venivano reindirizzati dalla pagina di accesso di Azure AD all'ambiente di AD FS. Ora che il tenant è configurato per l'uso dell'autenticazione pass-through anziché dell'autenticazione federata, gli utenti non vengono reindirizzati all'ambiente AD FS, ma passano direttamente alla pagina di accesso di Azure AD.

Per testare l'autenticazione pass-through:

1. Aprire Internet Explorer in modalità InPrivate per evitare che la funzionalità di accesso Single Sign-On facile esegua automaticamente l'accesso.
2. Passare alla pagina di accesso di Office 365 ([https://portal.office.com](https://portal.office.com/)).
3. Immettere l'UPN di un utente e quindi fare clic su **Avanti**. Assicurarsi di immettere l'UPN di un utente ibrido che è stato sincronizzato dall'istanza di Active Directory locale e che in precedenza ha usato l'autenticazione federata. Verrà visualizzata una pagina in cui immettere il nome utente e la password:

   ![Screenshot che mostra la pagina di accesso in cui immettere un nome utente](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Screenshot che mostra la pagina di accesso in cui immettere una password](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Dopo che si è immessa la password e si è selezionato **Accedi**, si verrà reindirizzati al portale di Office 365.

   ![Screenshot che mostra il portale di Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Testare l'accesso Single Sign-On facile

Per testare l'accesso Single Sign-On facile:

1. Accedere a un computer aggiunto al dominio che è connesso alla rete aziendale.
2. In Internet Explorer o Chrome passare a uno degli URL seguenti (sostituire "contoso" con il nome del proprio dominio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   L'utente verrà rapidamente reindirizzato alla pagina di accesso di Azure AD e vedrà il messaggio "Tentativo di accesso per conto dell'utente". All'utente non verrà chiesto di specificare un nome utente o una password.<br />

   ![Screenshot che mostra la pagina di accesso di Azure AD con il messaggio](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. L'utente verrà reindirizzato e l'accesso verrà eseguito automaticamente:

   > [!NOTE]
   > L'accesso Single Sign-On facile funziona nei servizi di Office 365 che supportano l'hint di dominio, ad esempio myapps.microsoft.com/contoso.com. Attualmente, il portale di Office 365 (portal.office.com) non supporta gli hint di dominio. Gli utenti devono immettere un UPN. Dopo che è stato immesso un UPN, la funzionalità di accesso Single Sign-On facile recupera il ticket Kerberos per conto dell'utente. L'utente esegue l'accesso senza immettere una password.

   > [!TIP]
   > Per un'esperienza di accesso Single Sign-On migliorata, valutare l'opportunità di distribuire [Aggiunta ad Azure AD ibrido in Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction).

### <a name="remove-the-relying-party-trust"></a>Rimuovere il trust della relying party

Dopo aver verificato che tutti gli utenti e i client eseguano correttamente l'autenticazione tramite Azure AD, si può rimuovere tranquillamente il trust della relying party Office 365.

Se non si usa AD FS per altri scopi, ovvero per altri trust di relying party, a questo punto si possono rimuovere senza problemi le autorizzazioni di AD FS.

### <a name="rollback"></a>Rollback

Se si riscontra un problema importante che non può essere risolto rapidamente, può essere opportuno eseguire il ripristino della soluzione alla federazione.

Vedere la documentazione relativa alla progettazione e alla distribuzione della federazione per i dettagli della specifica distribuzione. Il processo deve includere queste attività:

* Convertire i domini gestiti all'autenticazione federata usando il cmdlet **Convert-MSOLDomainToFederated**.
* Se necessario, configurare altre regole per le attestazioni.

### <a name="sync-userprincipalname-updates"></a>Sincronizzare gli aggiornamenti di userPrincipalName

In genere, gli aggiornamenti dell'attributo **UserPrincipalName**, che usa il servizio di sincronizzazione dall'ambiente locale, vengono bloccati a meno che non siano vere entrambe le condizioni seguenti:

* L'utente è in un dominio con identità gestite (non federate).
* All'utente non è stata assegnata una licenza.

Per informazioni su come verificare o attivare questa funzionalità, vedere [Sincronizzare gli aggiornamenti di userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rinnovare la chiave di decrittografia di Kerberos per l'accesso Single Sign-On facile

È importante rinnovare spesso la chiave di decrittografia di Kerberos dell'account computer AZUREADSSOACC, che rappresenta Azure AD. L'account computer AZUREADSSOACC viene creato nella foresta locale di Active Directory. È consigliabile rinnovare la chiave di decrittografia di Kerberos almeno ogni 30 giorni in modo che la frequenza di rinnovo sia allineata a quella con cui i membri del dominio di Active Directory inviano gli aggiornamenti delle password. Nessun dispositivo associato è collegato all'oggetto account computer AZUREADSSOACC ed è quindi necessario eseguire il rinnovo manualmente.

Avviare il rinnovo della chiave di decrittografia di Kerberos per l'accesso Single Sign-On facile nel server locale che esegue Azure AD Connect.

Per altre informazioni, vedere [Come è possibile rinnovare la chiave di decrittografia di Kerberos dell'account computer AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)

## <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

Monitorare i server che eseguono gli agenti di autenticazione per garantire la disponibilità della soluzione. Oltre ai contatori delle prestazioni generali del server, gli agenti di autenticazione espongono oggetti prestazioni che possono essere utili per riconoscere gli errori e le statistiche di autenticazione.

Gli agenti di autenticazione registrano le operazioni nei registri eventi di Windows che si trovano in Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

È anche possibile attivare la registrazione per la risoluzione dei problemi.

Per altre informazioni, vedere [Risolvere i problemi di autenticazione pass-through di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni, vedere [Concetti relativi alla progettazione per Azure AD Connect](plan-connect-design-concepts.md).
* Scegliere l'[autenticazione appropriata](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).
* Vedere le informazioni sulle [topologie supportate](plan-connect-design-concepts.md).
