---
title: Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio | Microsoft Docs
description: Registrare con Azure Active Directory i dispositivi Windows aggiunti a un dominio in modo automatico e invisibile all&quot;utente.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 70448a88fb9fc338132c5199aafaac02bd4aeb5a
ms.openlocfilehash: ac58f3f5498edbd6f7b19db647a9513a485d6dbf


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio

Per usare l'[accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access.md), i computer devono essere registrati con Azure Active Directory (Azure AD). Questo articolo illustra la procedura per configurare la registrazione automatica dei dispositivi Windows con Azure AD nell'organizzazione.

> [!NOTE]
> L'aggiornamento di Windows 10 di novembre offre solo alcune delle esperienze utente migliorate di Azure AD, ma supporta interamente l'accesso condizionale basato sul dispositivo. Per ulteriori informazioni sull'accesso condizionale, vedere l'articolo sull'[accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access.md). Per altre informazioni sui dispositivi Windows 10 nell'area di lavoro e su come registrare un dispositivo Windows 10 in Azure AD, vedere [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md).
> 
> 

Sui dispositivi che seguono Windows è possibile registrare alcune versioni precedenti di Windows, tra cui:

- Windows 8.1
- Windows 7

Sui dispositivi che eseguono Windows Server, è possibile registrare le piattaforme seguenti:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>Prerequisiti

Il requisito principale per la registrazione automatica dei dispositivi con Azure AD aggiunti a un dominio è una versione aggiornata di Azure Active Directory Connect (Azure AD Connect).

A seconda di come è stato distribuito Azure AD Connect, ovvero con l'installazione rapida o personalizzata oppure con un aggiornamento sul posto, potrebbero essere stati configurati automaticamente i seguenti prerequisiti:

- **Il punto di connessione del servizio Active Directory locale** per l'individuazione delle informazioni del tenant Azure AD da parte dei computer che si registrano ad Azure AD.
 
- **Le regole di trasformazione del rilascio di Active Directory Federation Services (AD FS)** per l'autenticazione dei computer al momento della registrazione (per le configurazioni federate).

Se i dispositivi dell'organizzazione non sono aggiunti a un dominio di Windows 10, seguire questa procedura:

* Impostare un criterio di Azure AD che consenta agli utenti di registrare i dispositivi
* Impostare l'Autenticazione integrata di Windows come possibile alternativa all'autenticazione a più fattori in AD FS

## <a name="step-1-configure-service-connection-point"></a>Passaggio 1: Configurare il punto di connessione del servizio 

La partizione del contesto di denominazione della configurazione deve contenere un oggetto del punto di connessione del servizio (SCP) nel dominio del computer. Il punto di connessione del servizio contiene informazioni di rilevamento sul tenant di Azure AD in cui si registrano i computer. In una configurazione di Active Directory a più foreste, il punto di connessione del servizio deve essere presente in tutte le foreste con computer aggiunti al dominio.

Il SCP si trova in:  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]**

Per una foresta con il nome del dominio di Active Directory *example.com*, il contesto di denominazione della configurazione è:  

**CN=Configuration,DC=example,DC=com**

Con il seguente script di Windows PowerShell, è possibile verificare l'esistenza dell'oggetto e recuperare i valori di individuazione: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

L'output di **$scp.Keywords** mostra le informazioni sul tenant di Azure AD, per esempio:

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se il punto di connessione del servizio non esiste, è possibile crearne uno eseguendo lo script PowerShell seguente nel server di Azure AD Connect:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**Osservazioni:**

- Quando si esegue **$aadAdminCred = Get-Credential**, è necessario digitare un nome utente. Il nome deve essere nel formato seguente: **user@example.com** 


- Quando si esegue il cmdlet **Initialize-ADSyncDomainJoinedComputerSync**, sostituire [*nome account connettore*] con l'account di dominio usato come account connettore di Active Directory.
  
- Questo cmdlet usa il modulo di Active Directory PowerShell basato su Servizi Web Active Directory in un controller di dominio. I Servizi Web Active Directory sono supportati nei controller di dominio in Windows Server 2008 R2 e versioni successive. Per i controller di dominio in Windows Server 2008 o versioni precedenti, usare l'API System.DirectoryServices tramite PowerShell per creare il punto di connessione del servizio e quindi assegnare i valori delle parole chiave.
 
 



##<a name="step-2-register-your-devices"></a>Passaggio 2: Registrare il dispositivo

La procedura consigliata per la registrazione del dispositivo varia se l'organizzazione è federata o meno. 


### <a name="device-registration-in-non-federated-organizations"></a>Registrazione del dispositivo in organizzazioni non federate

La registrazione del dispositivo in un'organizzazione non federata è supportata solo se si verifica quanto segue:

- Sul dispositivo si esegue Windows 10 e Windows Server 2016
- I dispositivi vengono aggiunti al dominio
- È abilitata la sincronizzazione delle password con Azure AD Connect

Se sono soddisfatti tutti questi requisiti, non è necessario eseguire alcuna operazione per registrare i dispositivi.  


### <a name="device-registration-in-federated-organizations"></a>Registrazione del dispositivo in organizzazioni federate

In una configurazione federata di Azure AD, i dispositivi si basano su AD FS (o sul server federativo locale) per eseguire l'autenticazione in Azure AD. Consentono di registrare sul servizio Registrazione dispositivo di Azure Active Directory.

Per i computer Windows 10 e Windows Server 2016, Azure AD Connect associa l'oggetto dispositivo in Azure AD all'oggetto dell'account computer locale. Per completare la registrazione e creare l'oggetto dispositivo, durante l'autenticazione al servizio Registrazione dispositivo Azure sono necessarie le informazioni seguenti:

- **http://schemas.microsoft.com/ws/2012/01/accounttype** contiene il valore DJ, che identifica lo strumento di autenticazione dell'entità di sicurezza come computer aggiunto al dominio.

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** contiene il valore dell'attributo **objectGUID** dell'account del computer locale.
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** contiene l'ID di sicurezza (SID) principale del computer, che corrisponde al valore dell'attributo **objectSid** dell'account del computer locale.

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** contiene il valore usato da Azure AD per considerare attendibile il token emesso da AD FS o dal servizio token di sicurezza (STS) locale. È importante se si dispone di più domini verificati in Azure AD. Nel caso di AD FS usare **http://\<*nome-dominio*\>/adfs/services/trust/** dove **\<nome-dominio\>** è il nome del dominio convalidato in Azure AD.

Per altre informazioni sui nomi dominio verificati, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](active-directory-add-domain.md).  
Per ottenere un elenco dei domini aziendali verificati, è possibile usare il cmdlet [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


I computer Windows 10 e Windows Server 2016 aggiunti al dominio eseguono l'autenticazione integrata di Windows a un endpoint WS-Trust attivo ospitato da AD FS. Assicurarsi che l'endpoint sia abilitato. Se si sta usando il proxy per l'autenticazione Web, assicurarsi anche che l'endpoint venga pubblicato tramite il proxy. L'endpoint è **adfs/services/trust/13/windowstransport**. 

A questo punto, dovrebbe essere abilitato nella console di gestione di AD FS in **Servizio > Endpoint**. Se AD FS non è disponibile come server federativo locale, attenersi alle istruzioni del fornitore per controllare che l'endpoint corrispondente sia abilitato. 



> [!NOTE]
> Se non si usa AD FS come server federativo locale, attenersi alle istruzioni del fornitore per creare le regole per l'emissione delle attestazioni.
> 
> 




**Creare le regole manualmente, in AD FS:**

- Selezionare uno degli script seguenti di Windows PowerShell 
- Eseguire lo script di Windows PowerShell in una sessione connessa al server. 
- Sostituire la prima riga con il nome di dominio convalidato dell'organizzazione in Azure AD.




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>Impostazione delle regole AD FS in un ambiente di dominio singolo

Usare lo script seguente per aggiungere le regole AD FS, se si dispone di **un solo dominio verificato**:


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

    $rule1 = '@RuleName = "Issue object GUID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(claim = c2);' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>Impostazione delle regole AD FS in un ambiente di dominio multiplo

Se si dispongono più domini verificati, procedere come segue:

1. Rimuovere la regola esistente **IssuerID** creata da Azure AD Connect.  
Di seguito è riportato un esempio della regola: c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. Eseguire questo script: 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

        $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

        $rule2 = '@RuleName = "Issue account type for domain joined computers" 

        c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

        $rule3 = '@RuleName = "Pass through primary SID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(claim = c2);' 

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>Passaggio 3: Configurare AD FS per l'autenticazione della registrazione del dispositivo

Controllare di aver impostato la WIA come valida alternativa alla Multi-Factor Authentication per la registrazione del dispositivo in AD FS. Per farlo, è necessario disporre di una regola di trasformazione del rilascio che trasmetta il metodo di autenticazione.

1. Nella console di gestione di AD FS, passare ad**AD FS** > **Relazioni di attendibilità** > **Attendibilità componente**.
2. Fare clic con il pulsante destro del mouse sull'oggetto trust della relying party della Piattaforma delle identità di Microsoft Office 365 e quindi scegliere **Modifica regole attestazione**.
3. Nella scheda **Regole di trasformazione rilascio** selezionare **Aggiungi regola**.
4. Nell'elenco dei modelli delle **regole di attestazione** selezionare **Inviare attestazioni mediante una regola personalizzata**.
5. Selezionare **Avanti**.
6. Nella casella di testo **Nome regola attestazione** digitare **Auth Method Claim Rule (Regola attestazioni Metodo di autenticazione)**.
7. Nella casella di testo **Claim rule (Regola attestazione)** digitare questa regola:  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. Nel server federativo, digitare questo comando di PowerShell:
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** è il nome dell'oggetto relying party per l'oggetto trust della relying party di Azure AD. Tale oggetto è solitamente denominato **Piattaforma delle identità di Microsoft Office 365**.



##<a name="step-4-deployment-and-rollout"></a>Passaggio 4: Distribuzione e implementazione

Una volta soddisfatti i prerequisiti per i computer aggiunti al dominio, i computer aggiunti a un dominio sono pronti per la registrazione con Azure AD.

I computer con l'aggiornamento dell'anniversario di Windows 10 e Windows Server 2016 aggiunti a un dominio verranno automaticamente registrati ad Azure AD al successivo riavvio del dispositivo o accesso a Windows da parte dell'utente. I nuovi computer aggiunti al dominio verranno registrati con Azure AD al riavvio del dispositivo successivo all'operazione di aggiunta a un dominio.

> [!NOTE]
> I computer aggiunti a un dominio che eseguono l'aggiornamento di Windows 10 di novembre verranno registrati automaticamente in Azure AD solo se risulta impostato l'oggetto Criteri di gruppo di implementazione.
> 
> 

Per controllare l'implementazione della registrazione automatica dei computer Windows 10 e Windows Server 2016 aggiunti al dominio, è possibile usare un oggetto Criteri di gruppo. 

Per implementare la registrazione automatica dei computer non Windows 10 aggiunti a un dominio, esiste un pacchetto Windows Installer da distribuire nei computer desiderati.

> [!NOTE]
> Per tutti i computer che non eseguono Windows 10 o Windows Server 2016 è consigliabile usare il pacchetto Windows Installer, come descritto più avanti in questo documento.
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Creare un oggetto Criteri di gruppo per controllare l'implementazione della registrazione automatica

Per controllare l'implementazione della registrazione automatica dei computer aggiunti a un dominio con Azure AD, è possibile distribuire i Criteri di gruppo **Registrare come dispositivi i computer aggiunti al dominio** ai computer che si desidera registrare. Ad esempio, è possibile distribuire i criteri a un'unità organizzativa o a un gruppo di sicurezza.

**Per impostare i criteri:**

1. Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2. Passare al nodo corrispondente al dominio in cui si desidera attivare la registrazione automatica dei computer Windows 10 o Windows Server 2016.
3. Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e quindi scegliere **Nuovo**.
4. Immettere un nome da assegnare all'oggetto Criteri di gruppo. Ad esempio, *Registrazione automatica in Azure AD*. Selezionare **OK**.
5. Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
6. Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Registrazione dispositivo**. Fare doppio clic su **Registra i computer aggiunti a un dominio come dispositivi**, quindi selezionare **Modifica**.
   
   > [!NOTE]
   > Questo modello di Criteri di gruppo è stato rinominato dalle versioni precedenti della console Gestione Criteri di gruppo. Se si usa una versione precedente della console, accedere a **Configurazione Computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Aggiunta all'area di lavoro** > **Automatically workplace join client computers (Aggiunta automatica di computer client all'area di lavoro)**.
   > 
   > 
7. Selezionare **Abilitato** (Abilitato) e quindi **Apply** (Applica).
8. Selezionare **OK**.
9. Collegare l'oggetto Criteri di gruppo a una posizione scelta. Ad esempio, è possibile collegarlo a una determinata unità organizzativa, oppure a un determinato gruppo di sicurezza di computer che si registrano automaticamente con Azure AD. Per impostare questi criteri per tutti i computer Windows 10 e Windows Server 2016 aggiunti a un dominio nell'organizzazione, collegare l'oggetto Criteri di gruppo al dominio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Pacchetti di Windows Installer per i computer non Windows 10
Per registrare i computer aggiunti a un dominio che eseguono Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 in un ambiente federativo, è possibile scaricare e installare questi file del pacchetto di Windows Installer (con estensione MSI):

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Distribuire il pacchetto usando un sistema di distribuzione software, ad esempio System Center Configuration Manager. Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro *quiet*. Rispetto alle versioni precedenti, System Center Configuration Manager 2016 offre ulteriori vantaggi, come la possibilità di tenere traccia delle registrazioni completate. Per altre informazioni, vedere [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata nel momento in cui l'utente accede a Windows. L'attività registra automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione tramite l'Autenticazione integrata di Windows. Per visualizzare l'attività pianificata, accedere a **Microsoft** > **Workplace Join** (Aggiunta all'area di lavoro) e quindi passare alla libreria Utilità di pianificazione.

## <a name="next-steps"></a>Passaggi successivi
* [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Jan17_HO1-->


