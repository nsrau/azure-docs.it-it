<properties 
	pageTitle="Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10 | Microsoft Azure" 
	description="Argomento che illustra come gli amministratori possono configurare criteri di gruppo per abilitare i dispositivi per poter essere aggiunti a un dominio della rete aziendale." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="11/19/2015" 

	ms.author="femila"/>

# Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10

Negli ultimi 15 anni, per connettere i dispositivi con cui lavorare le aziende fanno spesso ricorso alla funzione di aggiunta a un dominio. In questo modo, infatti, gli utenti possono accedere ai dispositivi tramite i propri account aziendali di Active Directory e il personale IT può assumere il totale controllo dei dispositivi. In genere, inoltre, le aziende si avvalgono di metodi di imaging per effettuare il provisioning dei dispositivi per gli utenti e, per gestirli, usano System Center Configuration Manager (SCCM) o Criteri di gruppo.

In Windows 10, dopo essersi connessi ad Azure AD, la funzione di aggiunta a un dominio fornisce i vantaggi seguenti:

- Accesso Single Sign-On (SSO) alle risorse di Azure AD da qualsiasi posizione.
- Accesso alla sezione aziendale di Windows Store usando account aziendali (non è necessario un account Microsoft).
- Roaming conforme ai criteri aziendali delle impostazioni utente tra i dispositivi usando account aziendali (non è necessario un account Microsoft)
- Autenticazione avanzata e pratico accesso all'account aziendale con Microsoft Passport e Windows Hello.
- Possibilità di limitare l'accesso ai dispositivi conformi ai criteri aziendali in materia di accesso ai dispositivi.

##Prerequisiti

Sebbene l'aggiunta a un dominio continui ad essere la strada da seguire, per usufruire dei vantaggi offerti da Azure AD relativamente all'accesso SSO, nonché al roaming delle impostazioni e all'accesso a Windows Store con l'account aziendale, è necessario quanto segue:

- Sottoscrizione di Azure AD.
- Azure AD Connect per estendere la directory locale ad Azure AD.
- Impostazione dei criteri per connettere dispositivi aggiunti a un dominio ad Azure AD.
- Build di Windows 10 (build 10551 o successiva) per i dispositivi.

Per abilitare Microsoft Passport for Work e Windows Hello, è inoltre necessario quanto segue:

- Infrastruttura PKI per il rilascio di certificati utente.
- System Center Configuration Manager versione 1509 per la Technical Preview. Per altre informazioni, vedere l'articolo sulla [Technical Preview di Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e il [blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Queste informazioni sono necessarie per distribuire i certificati utente in base alle chiavi di Microsoft Passport.

In alternativa alla distribuzione di un'infrastruttura PKI, è possibile soddisfare il requisito seguente:

- Disporre di alcuni controller di dominio con Servizi di dominio Active Directory di Windows Server 2016.

Per abilitare l'accesso condizionale, è possibile creare dei criteri che consentano l'accesso ai dispositivi aggiunti al dominio senza distribuzioni aggiuntive. Per gestire il controllo di accesso in base alla conformità dei dispositivi, è necessario quanto segue:

- System Center Configuration Manager versione 1509 per la Technical Preview relativa agli scenari di Passport.

## Istruzioni per la distribuzione


## Passaggio 1: Distribuzione di Azure Active Directory Connect

Azure AD Connect consentirà il provisioning dei computer locali come oggetti dispositivo nel cloud. Per abilitare Azure AD Connect, fare riferimento all'articolo "Abilitazione della directory per la gestione ibrida con Azure AD Connect".

 - Se è stata seguita un'[installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (non l'installazione rapida), è necessario seguire la procedura **Creare un punto di connessione del servizio (SCP) in Active Directory locale**, descritta di seguito.
 - Se invece si dispone di una configurazione federata con Azure AD (ad esempio, se in precedenza è stato distribuito Active Directory Federation Services), prima di installare Azure AD Connect è necessario seguire la procedura **Configurare regole attestazioni per AD FS** descritta di seguito.

### Creare un punto di connessione del servizio (SCP) in Active Directory locale

I dispositivi aggiunti al dominio useranno questo oggetto per trovare informazioni sul tenant di Azure AD al momento della registrazione automatica mediante il servizio Registrazione dispositivo di Azure. Nel server di Azure AD Connect, eseguire i comandi di PowerShell seguenti:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

>[AZURE.NOTE]Sostituire [*connector account name*] con l'account di dominio usato come account connettore AD.

>[AZURE.NOTE]Il nome utente della credenziale da specificare quando viene visualizzato il popup Get-Credential deve essere nel formato *user@example.com*

### Configurare regole attestazioni per AD FS
Questa procedura consente la registrazione immediata di un computer con Azure DRS, in modo da poterne eseguire l'autenticazione usando Kerberos/NTLM tramite AD FS. Senza questo passaggio, i computer dovranno accedere ad Azure AD con un certo ritardo (in base ai tempi di sincronizzazione di Azure AD Connect).

>[AZURE.NOTE]Se non si dispone di AD FS come server federativo locale, creare le regole attestazioni seguendo le istruzioni del fornitore.

Nel server AD FS (o in una sessione connessa al server AD FS) eseguire i comandi di PowerShell seguenti:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
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

>[AZURE.NOTE]I computer Windows 10 useranno l'autenticazione integrata di Windows per eseguire l'autenticazione a un endpoint WS-Trust attivo ospitato da AD FS. È quindi necessario verificare che l'endpoint sia abilitato. Se si sta usando il proxy per l'autenticazione Web, inoltre, è necessario verificare che l'endpoint sia stato pubblicato tramite il proxy. A tale scopo, controllare che nella console di gestione di AD FS la voce "adfs/services/trust/13/windowstransport" risulti abilitata nell'area Servizio > Endpoint.


## Passaggio 2: Configurare la registrazione automatica dei dispositivi tramite Criteri di gruppo in Active Directory

È possibile usare Criteri di gruppo di Active Directory per configurare i dispositivi aggiunti a un dominio di Windows 10 per la registrazione automatica in Azure AD. A questo scopo, seguire le istruzioni dettagliate seguenti:

1. 	Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2.	Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui si desidera abilitare la funzionalità di aggiunta ad Azure AD.
3.	Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio Aggiunta automatica ad Azure AD. Fare clic su **OK**.
4.	Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5.	Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Aggiunta all'area di lavoro**.
6.	Fare clic con il pulsante destro del mouse su **Aggiungi automaticamente i computer client all'area di lavoro** e scegliere **Modifica**.
7.	Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. Fare clic su **OK**.
8.	È ora possibile collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Per abilitare questo oggetto per tutti i dispositivi Windows 10 aziendali aggiunti al dominio, collegarlo al dominio. ad esempio:
 - Una specifica unità organizzativa (OU) in AD in cui verranno posizionati i computer aggiunti al dominio di Windows 10.
 - Uno specifico gruppo di sicurezza contenente i computer aggiunti al dominio di Windows 10 che verranno automaticamente registrati in Azure AD.
 
>[AZURE.NOTE]In Windows 10 questo modello di Criteri di gruppo è stato rinominato. Se si esegue lo strumento Criteri di gruppo da un computer Windows 10, i criteri verranno visualizzati come: <br> **Registra i computer aggiunti a un dominio come dispositivi** e i criteri verranno memorizzati nel percorso seguente:<br> ***Configurazione computer/Criteri/Modelli amministrativi/Componenti di Windows/Registrazione dispositivo***

 
## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!----HONumber=AcomDC_1203_2015-->