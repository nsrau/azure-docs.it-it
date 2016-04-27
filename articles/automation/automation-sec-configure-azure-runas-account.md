<properties
    pageTitle="Configurare un Account RunAs di Azure | Microsoft Azure"
    description="Esercitazione che illustra come creare, testare e usare l'autenticazione dell'entità di sicurezza in Automazione di Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="magoedte"/>

# Autenticare runbook con account RunAs di Azure
Questo argomento illustra come configurare un account di automazione dal portale di Azure usando la nuova funzionalità di account RunAs, anche detta entità servizio, per accedere alle risorse di Azure Resource Manager nella sottoscrizione con i runbook di automazione. Quando si crea un nuovo account di automazione nel portale di Azure, per impostazione predefinita viene creata automaticamente una nuova entità servizio assegnata al ruolo Collaboratore di controllo degli accessi in base al ruolo (RBAC) nella sottoscrizione. Questo semplifica il processo e consente di iniziare rapidamente la compilazione e distribuzione di runbook per supportare le esigenze di automazione.

Con un'entità servizio è possibile:

* Standardizzare l'autenticazione con Azure quando si gestiscono risorse di Azure Resource Manager tramite runbook.
* Automatizzare l'uso di runbook globali configurati negli avvisi di Azure.


>[AZURE.NOTE] La [funzionalità di integrazione degli avvisi](../azure-portal/insights-receive-alert-notifications.md) di Azure con i runbook globali di automazione richiede un account di automazione configurato con un'entità servizio. È possibile selezionare un account di automazione in cui è già definito un utente dell'entità servizio o scegliere di crearne uno nuovo.



L'articolo illustra come creare l'account di automazione dal portale di Azure e aggiornarne uno con un account RunAs usando Azure PowerShell e come eseguire l'autenticazione con l'entità servizio nei runbook.

## Creare un account di automazione dal portale di Azure
La procedura descritta in questa sezione permette di creare un nuovo account di automazione di Azure e un'entità servizio dal portale di Azure.

>[AZURE.NOTE] L'utente che esegue queste operazioni *deve* essere un membro del ruolo Amministratori della sottoscrizione.

1. Accedere al portale di Azure come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Account di automazione**.
3. Nel pannello Account di automazione fare clic su **Aggiungi**.<br>![Aggiungi account di Automazione](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di automazione.
5. Se si hanno più sottoscrizioni, specificare quella per il nuovo account, un **Gruppo di risorse** nuovo o esistente e la **Località** per il data center di Azure.
6. Assicurarsi che sia selezionato il valore **Sì** per l'opzione **Crea un account RunAs di Azure** e fare clic sul pulsante **Crea**.  

    ![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Se si sceglie di non creare l'account RunAs selezionando **No**, verrà visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**. Durante la creazione e l'assegnazione al ruolo **Collaboratore** nella sottoscrizione, l'account non ha un'identità di autenticazione corrispondente all'interno del servizio directory delle sottoscrizioni e, di conseguenza, non ha accesso alle risorse nella sottoscrizione. Questo impedisce ai runbook che fanno riferimento a questo account di autenticarsi ed eseguire attività sulle risorse di Azure Resource Manager.

    ![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Se dopo aver selezionato il pulsante **Crea** si riceve un messaggio di errore di autorizzazione negata, l'account non è un membro del ruolo Amministratori della sottoscrizione.

7. Mentre Azure crea l'account di automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Al termine, l'account di automazione viene creato con un asset del certificato denominato **AzureRunAsCertificate** che ha una durata di un anno e un asset della connessione denominato **AzureRunAsConnection**.

## Aggiornare un account di automazione tramite PowerShell
La procedura seguente aggiorna un account di automazione esistente e crea l'entità servizio tramite PowerShell. Questa procedura è necessaria se è stato creato un account ma si è scelto di non creare l'account RunAs.

Prima di procedere, verificare quanto segue:

1. È stato scaricato e installato il [modulo di Azure Active Directory per Windows PowerShell (versione a 64 bit)](http://go.microsoft.com/fwlink/p/?linkid=236297).
2. È stato creato un account di automazione. A questo account viene fatto riferimento come valore per i parametri -AutomationAccountName e -ApplicationDisplayName nello script riportato più avanti in questo articolo.
3. È stato installato [Azure Automation Authoring Toolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.2).

```
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser
```

Lo script di PowerShell configura quanto segue:

* Un'applicazione Azure AD che può essere autenticata con il certificato autofirmato e un account dell'entità servizio per questa applicazione in Azure AD. Il ruolo Collaboratore viene assegnato a questo account nella sottoscrizione corrente. Il ruolo può essere cambiato in proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere l'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](../automation/automation-role-based-access-control.md).  
* Un asset del certificato di Automazione nell'account di automazione specificato denominato **AzureRunAsCertificate**, che contiene il certificato usato nell'entità servizio.
* Un asset della connessione di Automazione nell'account di automazione specificato denominato **AzureRunAsConnection**, che contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.  


### Eseguire lo script di PowerShell

1. Salvare lo script seguente nel computer. Per questo esempio, salvare il file con il nome **New-AzureServicePrincipal.ps1**.  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount
    Import-Module AzureRM.Resources

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
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
3. Dalla shell della riga di comando di PowerShell con privilegi elevati passare alla cartella che contiene lo script creato nel passaggio 1 ed eseguire lo script modificando i valori dei parametri *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName* e *-CertPlainPassword*.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> `
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Verrà richiesto di autenticarsi con Azure dopo aver eseguito lo script. È *necessario* accedere con un account di amministratore del servizio nella sottoscrizione. <br>
4. Dopo aver completato lo script, passare alla sezione successiva per testare e verificare la nuova configurazione di credenziali.

### Verificare l'autenticazione
A questo punto dell'esercitazione viene eseguito un breve test per confermare che sia possibile eseguire l'autenticazione usando la nuova entità servizio. Se non si riesce a eseguire l'autenticazione, tornare al passaggio 1 e verificare nuovamente i singoli passaggi.

1. Nel portale di Azure aprire l'account di automazione creato in precedenza.  
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Creare un nuovo runbook facendo clic sul pulsante **Aggiungi runbook** e selezionare **Crea un nuovo runbook** nel pannello **Aggiungi runbook**.
4. Denominare il runbook *Test-SecPrin-Runbook* e selezionare PowerShell per il **Tipo di runbook**. Fare clic su **Crea** per creare il runbook.
5. Nel pannello **Modifica runbook di PowerShell** incollare il codice seguente nell'area di disegno:<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection `
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```  
<br>
6. Salvare il runbook facendo clic su **Salva**.
7. Fare clic su **Riquadro di test** per aprire il pannello **Test**.
8. Fare clic su **Avvia** per avviare il test.
9. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.  
10. Lo stato del processo verrà avviato come *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi a *Avvio in corso* quando un thread di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
11. Al termine del processo del runbook, viene visualizzato l'output. In questo caso lo stato visualizzato dovrebbe essere **Completato**.<br> ![Verifica del runbook dell'entità di sicurezza](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. Chiudere il pannello **Test** per tornare all'area di disegno.
13. Chiudere il pannello **Modifica runbook di PowerShell**.
14. Chiudere il pannello **Test-SecPrin-Runbook**.

Il codice qui usato per verificare se il nuovo account è configurato correttamente è quello che verrà usato nei runbook di PowerShell per eseguire l'autenticazione in Automazione di Azure per la gestione delle risorse di Azure Resource Manager. È anche possibile continuare a eseguire l'autenticazione con l'account di automazione usato finora.

## Passaggi successivi
- Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).
- Per altre informazioni sul [controllo degli accessi in base al ruolo in Automazione di Azure](../automation/automation-role-based-access-control.md), vedere il relativo articolo.

<!---HONumber=AcomDC_0420_2016-->