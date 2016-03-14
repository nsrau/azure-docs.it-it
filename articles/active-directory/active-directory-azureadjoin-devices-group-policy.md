<properties
	pageTitle="Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10 | Microsoft Azure"
	description="Illustra agli amministratori come configurare Criteri di gruppo per abilitare i dispositivi per l'aggiunta a un dominio nella rete dell'organizzazione."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="02/26/2016"

	ms.author="femila"/>

# Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10

Negli ultimi 15 anni, per connettere i dispositivi sui cui lavorare le aziende hanno fatto essenzialmente ricorso alla funzione di aggiunta a un dominio. Questo ha permesso agli utenti di accedere ai dispositivi con i relativi account aziendali o dell'istituto di istruzione di Windows Server Active Directory (Active Directory) e agli amministratori IT di gestire interamente tali dispositivi. In genere, le aziende si basano su metodi di creazione dell'immagine per effettuare il provisioning dei dispositivi agli utenti e, per gestirli, usano System Center Configuration Manager (SCCM) o Criteri di gruppo.

Dopo aver connesso i dispositivi ad Azure Active Directory (Azure AD), l'aggiunta a un dominio di Windows 10 offre i vantaggi seguenti:

- Accesso Single Sign-On (SSO) alle risorse di Azure AD da qualsiasi posizione.
- Accesso alla sezione aziendale di Windows Store usando account aziendali o dell'istituto di istruzione, non è necessario un account Microsoft.
- Roaming delle impostazioni utente tra dispositivi conforme ai criteri dell'organizzazione usando account aziendali o dell'istituto di istruzione, non è necessario un account Microsoft.
- Autenticazione avanzata e pratico accesso all'account aziendale o dell'istituto di istruzione con Microsoft Passport e Windows Hello.
- Possibilità di limitare l'accesso solo ai dispositivi conformi alle impostazioni di Criteri di gruppo dei dispositivi aziendali.

## Prerequisiti

Aggiunta a un dominio continua a essere utile. Tuttavia, per poter sfruttare i vantaggi offerti da Azure AD per l'accesso SSO, il roaming delle impostazioni e l'accesso a Windows Store con account aziendali o dell'istituto di istruzione, è necessario quanto segue:

- Sottoscrizione di Azure AD.
- Azure AD Connect per estendere la directory locale ad Azure AD.
- Criteri impostati per connettere dispositivi aggiunti a un dominio ad Azure AD.
- Build di Windows 10 (build 10551 o successiva) per i dispositivi.

Per abilitare Microsoft Passport for Work e Windows Hello, è anche necessario quanto segue:

- Infrastruttura a chiave pubblica (PKI) per il rilascio di certificati utente.
- System Center Configuration Manager versione 1509 per la Technical Preview. Per altre informazioni, vedere l'articolo relativo alla [Technical Preview di Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e il [blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Queste informazioni sono necessarie per distribuire i certificati utente in base alle chiavi di Microsoft Passport.

In alternativa alla distribuzione di un'infrastruttura PKI, è possibile soddisfare il requisito seguente:

- Disporre di alcuni controller di dominio con Servizi di dominio Active Directory di Windows Server 2016.

Per abilitare l'accesso condizionale, è possibile creare impostazioni di Criteri di gruppo che consentano l'accesso a dispositivi aggiunti al dominio senza distribuzioni aggiuntive. Per gestire il controllo di accesso in base alla conformità dei dispositivi, è necessario quanto segue:

- System Center Configuration Manager versione 1509 per la Technical Preview relativa agli scenari di Passport.

## Istruzioni per la distribuzione



### Passaggio 1: Distribuzione di Azure Active Directory Connect

Azure AD Connect consente di effettuare il provisioning dei computer locali come oggetti dispositivo nel cloud. Per distribuire Azure AD Connect, vedere la sezione "Installare Azure AD Connect" nell'articolo [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect/#install-azure-ad-connect).

 - Se è stata eseguita un'[installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md), non l'installazione rapida, seguire la procedura **Creare un punto di connessione del servizio nell'istanza di Active Directory locale**, descritta di seguito.
 - Se è presente una configurazione federata con Azure AD prima dell'installazione di Azure AD Connect, ad esempio se in precedenza è stato distribuito Active Directory Federation Services (AD FS), seguire la procedura **Configurare regole attestazioni per AD FS** descritta di seguito.

#### Creare un punto di connessione del servizio nell'istanza di Active Directory locale

I dispositivi aggiunti a un dominio usano il punto di connessione del servizio per trovare informazioni sul tenant di Azure AD al momento della registrazione automatica con il servizio Registrazione dispositivo Azure.

Nel server Azure AD Connect, eseguire questi comandi di PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Quando si esegue il cmdlet $aadAdminCred = Get-Credential, usare il formato **user@example.com* per il nome utente delle credenziali immesse quando viene visualizzato il popup Get-Credential.

Quando si esegue il cmdlet Initialize-ADSyncDomainJoinedComputerSync..., sostituire [*connector account name*] con l'account di dominio usato come account connettore di Active Directory.

#### Configurare regole attestazioni per AD FS
La configurazione delle regole attestazioni per AD FS permette la registrazione immediata di un computer con il servizio Registrazione dispositivo Azure consentendo l'autenticazione dei computer con Kerberos/NTLM tramite AD FS. Senza questo passaggio, i computer accedono ad Azure AD con un certo ritardo, a seconda dei tempi richiesti dal Servizio di sincronizzazione Azure AD Connect.

>[AZURE.NOTE]
Se AD FS non è disponibile come server federativo locale, creare le regole attestazioni seguendo le istruzioni del fornitore.

Nel server AD FS o in una sessione connessa al server AD FS eseguire questi comandi di PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
I computer Windows 10 usano l'autenticazione integrata di Windows per eseguire l'autenticazione a un endpoint WS-Trust attivo ospitato da AD FS. Assicurarsi che l'endpoint sia abilitato. Se si sta usando il proxy per l'autenticazione Web, assicurarsi anche che l'endpoint venga pubblicato tramite il proxy. A tale scopo, verificare adfs/services/trust/13/windowstransport. Dovrebbe risultare abilitato nella console di gestione di AD FS in **Servizio** > **Endpoint**.


### Passaggio 2: Configurare la registrazione automatica dei dispositivi tramite Criteri di gruppo in Active Directory

È possibile usare Criteri di gruppo di Active Directory per configurare i dispositivi Windows 10 aggiunti a un dominio per la registrazione automatica in Azure AD. A questo scopo, seguire queste istruzioni dettagliate:

1. 	Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2.	Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui si desidera abilitare la funzionalità di aggiunta ad Azure AD.
3.	Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e quindi scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio Aggiunta automatica ad Azure AD. Fare clic su **OK**.
4.	Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5.	Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Aggiunta all'area di lavoro**.
6.	Fare clic con il pulsante destro del mouse su **Eseguire automaticamente l'unione dell'area di lavoro dei computer client** e scegliere **Modifica**.
7.	Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. Fare clic su **OK**.
8.	Collegare l'oggetto Criteri di gruppo a una posizione scelta. Per abilitare questi criteri per tutti i dispositivi Windows 10 aggiunti a un dominio nell'organizzazione, collegare l'oggetto Criteri di gruppo al dominio. Ad esempio:
 - Una specifica unità organizzativa in Active Directory in cui saranno posizionati i computer Windows 10 aggiunti a un dominio.
 - Uno specifico gruppo di sicurezza contenente i computer Windows 10 aggiunti a un dominio che verranno registrati automaticamente in Azure AD.

>[AZURE.NOTE]
In Windows 10 questo modello di Criteri di gruppo è stato rinominato. Se si esegue lo strumento Criteri di gruppo da un computer Windows 10, i criteri verranno visualizzati come: <br> **Registra i computer aggiunti a un dominio come dispositivi**<br> I criteri verranno memorizzati nel percorso seguente: <br>***Configurazione computer/Criteri/Modelli amministrativi/Componenti di Windows/Registrazione dispositivi***


## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->