<properties
    pageTitle="Configurare un Account RunAs di Azure | Microsoft Azure"
    description="Esercitazione che illustra come creare, testare e usare l'autenticazione dell'entità di sicurezza in Automazione di Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="nome entità servizio, setspn, autenticazione di Azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="magoedte"/>  

# Autenticare runbook con account RunAs di Azure

Questo argomento illustra come configurare un account di Automazione dal portale di Azure usando la funzionalità dell'account RunAs per autenticare i runbook che gestiscono risorse in Azure Resource Manager o Azure Service Management.

Quando si crea un nuovo account di Automazione nel portale di Azure, viene creato automaticamente quanto segue:

- Account RunAs che crea una nuova entità servizio in Azure Active Directory e un certificato e assegna il ruolo Collaboratore per il controllo degli accessi in base al ruolo, che verrà usato per gestire le risorse di Resource Manager con i runbook.
- Account RunAs classico caricando un certificato di gestione che verrà usato per gestire le risorse classiche o di Azure Service Management con i runbook.

Questo semplifica il processo e consente di iniziare rapidamente la compilazione e distribuzione di runbook per supportare le esigenze di automazione.

Con un account RunAs e un account RunAs classico, è possibile:

- Standardizzare l'autenticazione con Azure quando si gestiscono risorse di Azure Resource Manager o Azure Service Management con i runbook nel portale di Azure.
- Automatizzare l'uso di runbook globali configurati negli avvisi di Azure.


>[AZURE.NOTE] La [funzionalità di integrazione degli avvisi](../azure-portal/insights-receive-alert-notifications.md) di Azure con i runbook globali di automazione richiede un account di Automazione configurato con un account RunAs e un account RunAs classico. È possibile selezionare un account di Automazione per cui sono già definiti un account RunAs e un account RunAs classico o scegliere di crearne uno nuovo.

L'articolo illustra come creare l'account di Automazione dal portale di Azure, come aggiornare un account di Automazione con PowerShell e come eseguire l'autenticazione nei runbook.

Prima di procedere, è consigliabile comprendere e considerare alcuni aspetti.

1. Quanto descritto in questo articolo non influisce sugli account di Automazione esistenti già creati nel modello di distribuzione classica o Resource Manager.
2. Quanto descritto in questo articolo si applica solo agli account di Automazione creati tramite il portale di Azure. Provando a creare un account dal portale classico, non verrà replicata la configurazione dell'account RunAs.
3. Se sono presenti runbook e asset (pianificazioni, variabili e così via) creati in precedenza per gestire risorse classiche e si vuole che tali runbook eseguano l'autenticazione con il nuovo account RunAs classico, è necessario eseguirne la migrazione al nuovo account di Automazione oppure aggiornare l'account esistente usando lo script di PowerShell riportato più avanti.
4. Per eseguire l'autenticazione con il nuovo account RunAs e il nuovo account RunAs classico di Automazione, è necessario modificare i runbook esistenti con il codice di esempio riportato più avanti. **Si noti** che l'account RunAs verrà usato per l'autenticazione per le risorse di Resource Manager con l'entità servizio basata su certificato, mentre l'account RunAs classico verrà usato per l'autenticazione per le risorse di Service Management con il certificato di gestione.


## Creare un account di automazione dal portale di Azure

La procedura descritta in questa sezione consente di creare un nuovo account di Automazione di Azure dal portale di Azure. Vengono così creati l'account RunAs e l'account RunAs classico.

>[AZURE.NOTE] L'utente che esegue questa procedura *deve* essere membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione che concede l'accesso alla sottoscrizione per l'utente. Deve anche essere aggiunto come utente alla directory di Active Directory predefinita della sottoscrizione. Non è necessario che all'account sia assegnato un ruolo con privilegi.

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Selezionare **Account di automazione**.
3. Nel pannello Account di automazione fare clic su **Aggiungi**.<br>![Aggiungi account di Automazione](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

    >[AZURE.NOTE] Se nel pannello **Aggiungi account di Automazione** viene visualizzato l'avviso seguente, l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.<br>![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di Automazione.
5. Se si hanno più sottoscrizioni, specificarne una per il nuovo account, un **Gruppo di risorse** nuovo o esistente e la **Località** del data center di Azure.
6. Verificare che sia selezionato il valore **Sì** per l'opzione **Crea un account RunAs di Azure** e fare clic sul pulsante **Crea**.

    >[AZURE.NOTE] Se si sceglie di non creare l'account RunAs selezionando l'opzione **No**, verrà visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**. L'account verrà creato nel portale di Azure, ma non avrà un'identità di autenticazione corrispondente nel servizio directory della sottoscrizione classica o Resource Manager e, di conseguenza, non avrà accesso alle risorse nella sottoscrizione. Questo impedirà ai runbook che fanno riferimento a questo account di eseguire l'autenticazione ed eseguire attività sulle risorse in tali modelli di distribuzione.
    
    >![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.


7. Mentre Azure crea l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### Risorse incluse

Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse. La tabella seguente offre un riepilogo delle risorse per l'account RunAs.<br>

Risorsa|Descrizione 
--------|-----------
Runbook AzureAutomationTutorial|Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager.
Runbook AzureAutomationTutorialScript|Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. 
AzureRunAsCertificate|Asset di certificato creato automaticamente durante la creazione dell'account di Automazione oppure usando lo script di PowerShell riportato più avanti per un account esistente. Consente di eseguire l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager con i runbook. La durata di questo certificato è di un anno. 
AzureRunAsConnection|Asset di connessione creato automaticamente durante la creazione dell'account di Automazione oppure usando lo script di PowerShell riportato più avanti per un account esistente.

La tabella seguente offre un riepilogo delle risorse per l'account RunAs classico.<br>

Risorsa|Descrizione 
--------|-----------
Runbook AzureClassicAutomationTutorial|Runbook di esempio che ottiene tutte le VM classiche in una sottoscrizione usando l'account RunAs classico (certificato) e quindi restituisce il nome e lo stato delle VM.
Runbook di script AzureClassicAutomationTutorial|Runbook di esempio che ottiene tutte le VM classiche in una sottoscrizione usando l'account RunAs classico (certificato) e quindi restituisce il nome e lo stato delle VM.
AzureClassicRunAsCertificate|Asset di certificato creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. La durata di questo certificato è di un anno. 
AzureClassicRunAsConnection|Asset di connessione creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook.  

## Verificare l'autenticazione con RunAs

A questo punto verrà eseguito un breve test per verificare che sia possibile eseguire l'autenticazione usando il nuovo account RunAs.

1. Nel portale di Azure aprire l'account di automazione creato in precedenza.
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook **AzureAutomationTutorialScript** e quindi fare clic su **Avvia** per avviare il runbook. Verrà visualizzata la richiesta di confermare l'avvio del runbook.
4. Verrà creato un [processo del runbook](automation-runbook-execution.md) e verrà visualizzato il pannello Processo con lo stato del processo nel riquadro **Riepilogo processi**.
5. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi a *Avvio in corso* quando un thread di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.
6. Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.<br> ![Verifica del runbook dell'entità di sicurezza](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output**.
8. Nel pannello **Output** dovrebbe risultare che l'autenticazione è stata completata e che è stato restituito un elenco di tutte le risorse disponibili nel gruppo di risorse.
9. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.
13. Chiudere **Riepilogo processi** e il pannello del runbook **AzureAutomationTutorialScript** corrispondente.

## Verificare l'autenticazione con RunAs classico

A questo punto verrà eseguito un breve test per verificare che sia possibile eseguire l'autenticazione usando il nuovo account RunAs classico.

1. Nel portale di Azure aprire l'account di automazione creato in precedenza.
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook **AzureClassicAutomationTutorialScript** e quindi fare clic su **Avvia** per avviare il runbook. Verrà visualizzata la richiesta di confermare l'avvio del runbook.
4. Verrà creato un [processo del runbook](automation-runbook-execution.md) e verrà visualizzato il pannello Processo con lo stato del processo nel riquadro **Riepilogo processi**.
5. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi a *Avvio in corso* quando un thread di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.
6. Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.<br> ![Verifica del runbook dell'entità di sicurezza](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output**.
8. Nel pannello **Output** dovrebbe risultare che l'autenticazione è stata completata e che è stato restituito un elenco di tutte le VM classiche nella sottoscrizione.
9. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.
13. Chiudere **Riepilogo processi** e il pannello del runbook **AzureClassicAutomationTutorialScript** corrispondente.

## Aggiornare un account di automazione tramite PowerShell

Verrà ora illustrata la possibilità di usare PowerShell per aggiornare l'account di Automazione esistente se:

1. È stato creato un account di Automazione ma si è scelto di non creare l'account RunAs
2. Si ha già un account di Automazione per gestire le risorse di Resource Manager e lo si vuole aggiornare per includere l'account RunAs per l'autenticazione dei runbook
2. Si ha già un account di Automazione per gestire le risorse classiche e lo si vuole aggiornare per usare l'account RunAs classico invece di creare un nuovo account ed eseguire la migrazione di runbook e asset al nuovo account

Prima di procedere, verificare quanto segue:

1. È stato scaricato e installato [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855), se si esegue Windows 7. Se si esegue Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 o Windows 7 SP1, è disponibile per l'installazione [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395).
2. Azure PowerShell 1.0. Per informazioni su questa versione e su come installarla, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
3. È stato creato un account di automazione. A questo account verrà fatto riferimento come valore per i parametri -AutomationAccountName e -ApplicationDisplayName in entrambi gli script riportati più avanti in questo articolo.

Per ottenere i valori per *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, che costituiscono parametri obbligatori per gli script, nel portale di Azure selezionare l'account di Automazione nel pannello **Account di automazione** e quindi **Tutte le impostazioni**. Nel pannello **Tutte le impostazioni** selezionare **Proprietà** in **Impostazioni account**. Nel pannello **Proprietà** è possibile prendere nota di questi valori.<br> ![Proprietà dell'account di Automazione](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### Creare lo script di PowerShell per l'account RunAs

Lo script di PowerShell riportato di seguito configura quanto segue:

- Un'applicazione Azure AD che verrà autenticata con il certificato autofirmato. Verrà creato un account dell'entità servizio per questa applicazione in Azure AD e a questo account verrà assegnato il ruolo Collaboratore nella sottoscrizione corrente, che può essere cambiato in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere l'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](../automation/automation-role-based-access-control.md).
- Un asset del certificato di automazione nell'account di Automazione specificato, denominato **AzureRunAsCertificate**, che contiene il certificato usato dall'entità servizio.
- Un asset della connessione di automazione nell'account di Automazione specificato, denominato **AzureRunAsConnection**, che contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

La procedura seguente illustra il processo di esecuzione dello script.

1. Salvare lo script seguente nel computer. Per questo esempio, salvare il file con il nome **New-AzureServicePrincipal.ps1**.

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue

        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        } 

        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1

        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
3. Dalla shell della riga di comando di PowerShell con privilegi elevati passare alla cartella contenente lo script creato nel passaggio 1 ed eseguire lo script modificando i valori dei parametri *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* e *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Verrà richiesto di autenticarsi con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
    
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

Al termine dell'esecuzione dello script, per eseguire l'autenticazione con le risorse di Resource Manager e convalidare la configurazione delle credenziali vedere il [codice di esempio](#sample-code-to-authenticate-with-resource-manager-resources) riportato più avanti.

### Creare lo script di PowerShell per l'account RunAs classico

Lo script di PowerShell riportato di seguito configura quanto segue:

- Un asset del certificato di automazione nell'account di Automazione specificato, denominato **AzureClassicRunAsCertificate**, che contiene il certificato usato per l'autenticazione dei runbook.
- Un asset della connessione di automazione nell'account di Automazione specificato, denominato **AzureClassicRunAsConnection**, che contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

Lo script creerà un certificato di gestione autofirmato che verrà salvato nel computer nella cartella dei file temporanei del profilo utente usato per eseguire la sessione di PowerShell (*%USERPROFILE%\\AppData\\Local\\Temp*). Dopo l'esecuzione dello script, sarà necessario caricare il certificato di gestione di Azure nell'archivio di gestione della sottoscrizione in cui è stato creato l'account di Automazione. La procedura seguente illustra il processo di esecuzione dello script e caricamento del certificato.

1. Salvare lo script seguente nel computer. Per questo esempio, salvare il file con il nome **New-AzureClassicRunAsAccount.ps1**.

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues 

        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
3. Dalla shell della riga di comando di PowerShell con privilegi elevati passare alla cartella contenente lo script creato nel passaggio 1 ed eseguire lo script modificando i valori dei parametri *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* e *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Verrà richiesto di autenticarsi con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" 

Al termine dell'esecuzione dello script, è necessario copiare il certificato creato nella cartella **Temp** del proprio profilo utente. Seguire la procedura per [caricare un certificato dell'API di gestione](../azure-api-management-certs.md) nel portale di Azure classico e quindi vedere il [codice di esempio](#sample-code-to-authenticate-with-service-management-resources) per convalidare la configurazione delle credenziali con le risorse di Service Management.

## Codice di esempio per l'autenticazione con le risorse di Resource Manager

È possibile usare il codice di esempio aggiornato seguente, tratto dal runbook di esempio **AzureAutomationTutorialScript**, per eseguire l'autenticazione usando l'account RunAs per gestire le risorse di Resource Manager con i runbook.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
       
       "Logging in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
	   "Setting context to a specific subscription"	 
	   Set-AzureRmContext -SubscriptionId $SubId	 		 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    } 
   

Lo script include due righe di codice aggiuntive per supportare il riferimento al contesto di una sottoscrizione per poter lavorare facilmente in più sottoscrizioni. Un asset di variabile denominato SubscriptionId contiene l'ID della sottoscrizione e dopo l'istruzione del cmdlet Add-AzureRmAccount è riportata l'istruzione del [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) con il set di parametri *-SubscriptionId*. Se il nome della variabile è troppo generico, è possibile modificarlo includendo un prefisso o un'altra convenzione di denominazione per identificarla più facilmente in base alle proprie esigenze. In alternativa, è possibile usare il set di parametri -SubscriptionName invece di -SubscriptionId con un asset della variabile corrispondente.

Si noti che il cmdlet usato per l'autenticazione nel runbook (**Add-AzureRmAccount**) usa il set di parametri *ServicePrincipalCertificate* ed esegue l'autenticazione usando il certificato dell'entità servizio, non le credenziali.

## Codice di esempio per l'autenticazione con le risorse di Service Management

È possibile usare il codice di esempio aggiornato seguente, tratto dal runbook di esempio **AzureClassicAutomationTutorialScript**, per eseguire l'autenticazione usando l'account RunAs classico per gestire le risorse classiche con i runbook.
    
    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        
    
    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }
      
    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }
      
    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert 
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## Passaggi successivi

- Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).
- Per altre informazioni sul controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](../automation/automation-role-based-access-control.md).
- Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).

<!---HONumber=AcomDC_0817_2016-->