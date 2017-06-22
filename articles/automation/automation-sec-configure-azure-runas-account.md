---
title: Configurare un Account RunAs di Azure | Microsoft Docs
description: "Questa esercitazione illustra come creare, testare e usare l&quot;autenticazione dell&quot;entità di sicurezza in Automazione di Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "nome entità servizio, setspn, autenticazione di Azure"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
ROBOTS: NOINDEX
redirect_url: /azure/automation/
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 90570886b3a1ae0c48683691cb438b5a61195d76
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Autenticare runbook con un account RunAs di Azure
Questo articolo illustra come configurare un account di Automazione di Azure nel portale di Azure. A tale scopo, viene usata la funzionalità dell'account RunAs per autenticare i runbook che gestiscono risorse in Azure Resource Manager o Azure Service Management.

Quando si crea un account di Automazione nel portale di Azure, vengono creati automaticamente due account:

* Un account RunAs. Questo account crea un'entità servizio in Azure Active Directory (Azure AD) e un certificato. Assegna il controllo degli accessi in base al ruolo Collaboratore, che permette di gestire le risorse di Resource Manager usando i runbook.
* Un account RunAs classico. Questo account carica un certificato di gestione, che viene usato per gestire le risorse di Azure Service Management o classiche usando i runbook.

La creazione di un account di Automazione semplifica il processo e permette di iniziare rapidamente a compilare e distribuire runbook a supporto delle esigenze di automazione.

Con un account RunAs e un account RunAs classico è possibile:

* Standardizzare l'autenticazione con Azure quando si gestiscono risorse di Resource Manager o Service Management con i runbook nel portale di Azure.
* Automatizzare l'uso di runbook globali, che è possibile configurare negli avvisi di Azure.

> [!NOTE]
> La [funzionalità di integrazione degli avvisi di Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) con i runbook globali di Automazione richiede un account di Automazione configurato con un account RunAs e un account RunAs classico. È possibile selezionare un account di Automazione per cui sono già definiti un account RunAs e un account RunAs classico o scegliere di creare un nuovo account di Automazione.
>  

Questo articolo illustra come creare un account di Automazione dal portale di Azure, come aggiornare un account di Automazione usando Azure PowerShell, come gestire la configurazione dell'account e come eseguire l'autenticazione nei runbook.

Prima di iniziare a creare un account di Automazione, è consigliabile comprendere e considerare quanto segue:

* La creazione di un account di Automazione non influisce sugli account di Automazione esistenti già creati nel modello di distribuzione classica o Resource Manager.
* Il processo si applica solo agli account di Automazione creati nel portale di Azure. Se si prova a creare un account dal portale di Azure classico, la configurazione dell'account RunAs non viene replicata.
* Se sono già disponibili runbook e risorse, come pianificazioni o variabili, per la gestione delle risorse classiche e si vogliono autenticare i runbook con il nuovo account RunAs classico, eseguire una di queste operazioni:

  * Per creare un account RunAs classico, seguire le istruzioni riportate nella sezione "Gestire l'account RunAs". 
  * Per aggiornare l'account esistente, usare lo script di PowerShell riportato nella sezione "Aggiornare l'account di Automazione usando PowerShell".
* Per eseguire l'autenticazione con il nuovo account RunAs e il nuovo account RunAs classico di Automazione, è necessario modificare i runbook esistenti con il codice di esempio riportato nella sezione relativa agli [esempi di codice di autenticazione](#authentication-code-examples).

    >[!NOTE]
    >L'account Run As deve essere usato per l'autenticazione su risorse di Resource Manager tramite l'entità servizio basata su certificato. L'account RunAs classico deve essere usato per l'autenticazione su risorse di Service Management tramite un certificato di gestione.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Creare un account di Automazione dal portale di Azure
Questa sezione illustra come creare un account di Automazione di Azure dal portale di Azure, che a sua volta crea un account RunAs e un account RunAs classico.

>[!NOTE]
>Per creare un account di Automazione, l'utente deve essere membro del ruolo Amministratori del servizio o coamministratore della sottoscrizione che concede l'accesso alla sottoscrizione. Deve anche essere aggiunto come utente all'istanza di Active Directory predefinita di tale sottoscrizione. Non è necessario assegnare all'account un ruolo con privilegi.
>
>Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In tal caso si riceve un avviso di tipo "L'utente non è autorizzato a creare..." nel pannello **Aggiungi account di Automazione**.
>
>Gli utenti che prima sono stati aggiunti al ruolo di coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione dal riquadro **Azure Active Directory** nel portale di Azure, selezionare **Utenti e gruppi**, **Tutti gli utenti**, selezionare l'utente specifico e quindi scegliere **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.
>

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

2. Selezionare **Account di automazione**.

3. Nel pannello **Account di automazione** fare clic su **Aggiungi**.
Verrà visualizzato il pannello **Aggiungi account di Automazione**.

 ![Pannello "Aggiungi account di Automazione"](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Se l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione, nel pannello **Aggiungi account di Automazione** viene visualizzato l'avviso seguente:
   >
   >![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di Automazione.

5. Se è disponibile più di una sottoscrizione, seguire questa procedura:

    a. In **Sottoscrizione** specificarne una per il nuovo account.

    b. In **Gruppo di risorse** fare clic su **Crea nuovo** o **Usa esistente**.

    c. In **Località** specificare un data center di Azure.

6. In **Crea un account RunAs di Azure** scegliere **Sì** e quindi fare clic su **Crea**.

   > [!NOTE]
   > Se si sceglie di non creare l'account RunAs selezionando **No**, viene visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**. L'account verrà creato nel portale di Azure, ma non avrà un'identità di autenticazione corrispondente nel servizio directory della sottoscrizione classica o Resource Manager. Di conseguenza, l'account non avrà accesso alle risorse nella sottoscrizione. Questo scenario impedisce ai runbook che fanno riferimento a questo account di autenticarsi ed eseguire attività sulle risorse in tali modelli di distribuzione.
   >
   > ![Messaggio di avviso nel pannello "Aggiungi account di Automazione"](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.
   >

7. Mentre Azure crea l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="resources"></a>Risorse
Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse. Le risorse sono riepilogate nelle due tabelle seguenti:

#### <a name="run-as-account-resources"></a>Risorse dell'account RunAs

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureAutomationTutorial | Runbook grafico di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialScript | Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| AzureRunAsCertificate | Asset di certificato creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell riportato più avanti per un account esistente. Il certificato permette di eseguire l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager con i runbook. La durata di questo certificato è di un anno. |
| AzureRunAsConnection | Asset di connessione creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell per un account esistente. |

#### <a name="classic-run-as-account-resources"></a>Risorse dell'account RunAs classico

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureClassicAutomationTutorial | Runbook grafico di esempio che ottiene tutte le macchine virtuali create con il modello di distribuzione classica in una sottoscrizione usando l'account RunAs classico (certificato) e quindi scrive il nome e lo stato delle macchine virtuali. |
| Runbook di script AzureClassicAutomationTutorial | Runbook di PowerShell di esempio che ottiene tutte le macchine virtuali classiche in una sottoscrizione usando l'account RunAs classico (certificato) e quindi scrive il nome e lo stato delle macchine virtuali. |
| AzureClassicRunAsCertificate | Asset di certificato creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. La durata di questo certificato è di un anno. |
| AzureClassicRunAsConnection | Asset di connessione creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. |

## <a name="verify-run-as-authentication"></a>Verificare l'autenticazione con RunAs
Eseguire un breve test per verificare che sia possibile eseguire l'autenticazione usando il nuovo account RunAs.

1. Nel portale di Azure aprire l'account di automazione creato in precedenza.

2. Fare clic nel pannello **Runbook** per aprire l'elenco dei runbook.

3. Selezionare il runbook **AzureAutomationTutorialScript** e quindi fare clic su **Avvia** per avviarlo. Si verificano gli eventi seguenti:
 * Viene creato un [processo del runbook](automation-runbook-execution.md) e viene visualizzato il pannello **Processo** con lo stato del processo nel riquadro **Riepilogo processi**.
 * Lo stato del processo è inizialmente **In coda** per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud.
 * Lo stato passa a **Avvio in corso** quando un ruolo di lavoro richiede il processo.
 * Lo stato passa a **In esecuzione** quando l'esecuzione del runbook viene avviata.
 * Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output**.  
Nel pannello **Output** che viene visualizzato l'autenticazione del runbook risulta completata e viene restituito un elenco di tutte le risorse disponibili nel gruppo di risorse.

5. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.

6. Chiudere il pannello **Riepilogo processi** e il pannello del runbook **AzureAutomationTutorialScript** corrispondente.

## <a name="verify-classic-run-as-authentication"></a>Verificare l'autenticazione con RunAs classico
Eseguire un breve test simile al precedente per verificare che sia possibile eseguire l'autenticazione usando il nuovo account RunAs classico.

1. Nel portale di Azure aprire l'account di automazione creato in precedenza.

2. Fare clic nel pannello **Runbook** per aprire l'elenco dei runbook.

3. Selezionare il runbook **AzureClassicAutomationTutorialScript** e quindi fare clic su **Avvia** per avviarlo. Si verificano gli eventi seguenti:

 * Viene creato un [processo del runbook](automation-runbook-execution.md) e viene visualizzato il pannello **Processo** con lo stato del processo nel riquadro **Riepilogo processi**.
 * Lo stato del processo è inizialmente **In coda** per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud.
 * Lo stato passa a **Avvio in corso** quando un ruolo di lavoro richiede il processo.
 * Lo stato passa a **In esecuzione** quando l'esecuzione del runbook viene avviata.
 * Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.

    ![Verifica del runbook dell'entità di sicurezza](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output**.  
Nel pannello **Output** che viene visualizzato l'autenticazione del runbook risulta completata e viene restituito un elenco di tutte le macchine virtuali nella sottoscrizione.

5. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.

6. Chiudere il pannello **Riepilogo processi** e il pannello del runbook **AzureAutomationTutorialScript** corrispondente.

## <a name="managing-your-run-as-account"></a>Gestire l'account RunAs
Prima della scadenza dell'account di Automazione, è necessario rinnovare il certificato. Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminarlo e crearlo nuovamente. Questa sezione illustra come eseguire tali operazioni.

### <a name="self-signed-certificate-renewal"></a>Rinnovo del certificato autofirmato
Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Quando si procede al rinnovo, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

> [!NOTE]
> Se l'account RunAs di Automazione è stato configurato per l'uso di un certificato rilasciato dall'Autorità di certificazione globale (enterprise) e si usa tale opzione, il certificato viene sostituito da un certificato autofirmato.

Per rinnovare il certificato, seguire questa procedura:

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel pannello delle **proprietà dell'account** in **Account di Automazione** selezionare **Account RunAs** nella sezione **Impostazioni account**.

    ![Riquadro delle proprietà dell'account di Automazione](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. Nel pannello delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico per cui si vuole rinnovare il certificato.

4. Nel pannello **Proprietà** dell'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico
Questa sezione illustra come eliminare e creare nuovamente un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account RunAs o l'account RunAs classico, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel pannello delle proprietà dell'account in **Account di Automazione** selezionare **Account RunAs**.

3. Nel pannello delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico da eliminare. Nel pannello **Proprietà** dell'account selezionato fare quindi clic su **Elimina**.

 ![Eliminare un account RunAs](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

5. Dopo aver eliminato l'account, è possibile crearlo nuovamente nel pannello delle proprietà **Account RunAs** selezionando l'opzione di creazione **Account RunAs di Azure**.

 ![Ricreare l'account RunAs di Automazione](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Errore di configurazione
Alcuni elementi di configurazione necessari per il corretto funzionamento dell'account RunAs o dell'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tali elementi includono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di collaboratore
* Entità servizio o applicazione in Azure AD

In caso di errore di configurazione, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel pannello delle proprietà **Account RunAs** per l'account.

![Stato di configurazione Incompleto dell'account RunAs](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, nel riquadro **Proprietà** dell'account viene visualizzato il messaggio di errore seguente:

![Messaggio di avviso relativo alla configurazione incompleta dell'account RunAs](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="update-your-automation-account-by-using-powershell"></a>Aggiornare l'account di Automazione usando PowerShell
È possibile usare PowerShell per aggiornare l'account di Automazione esistente se:

* Si crea un account di Automazione ma si sceglie di non creare l'account RunAs.
* Si usa già un account di Automazione per gestire le risorse di Resource Manager e lo si vuole aggiornare per includere l'account RunAs per l'autenticazione dei runbook.
* Si usa già un account di Automazione per gestire le risorse classiche e lo si vuole aggiornare per usare l'account RunAs classico anziché creare un nuovo account ed eseguire la migrazione di runbook e asset nel nuovo account.   
* Si vuole creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).

Di seguito sono riportati i prerequisiti dello script:

* Lo script può essere eseguito solo in Windows 10 e Windows Server 2016 con i moduli di Azure Resource Manager 2.01 e versioni successive. Non sono supportate le versioni precedenti di Windows.
* Azure PowerShell 1.0 e versioni successive. Per informazioni su PowerShell 1.0, vedere [come installare e configurare Azure PowerShell](/powershell/azure/overview).
* Un account di automazione, a cui viene fatto riferimento come valore per i parametri *-AutomationAccountName* e *-ApplicationDisplayName* nello script di PowerShell seguente.

Per ottenere i valori per i parametri *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, obbligatori per gli script, seguire questa procedura:
1. Nel portale di Azure scegliere l'account di Automazione nel pannello **Account di Automazione** e selezionare **Tutte le impostazioni**. 
2. Nel pannello **Tutte le impostazioni** selezionare **Proprietà** in **Impostazioni account**. 
3. Prendere nota dei valori nel pannello **Proprietà**.

![Pannello "Proprietà" dell'account di Automazione](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Creare lo script di PowerShell per l'account RunAs
Questo script di PowerShell include il supporto per le configurazioni seguenti:

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato enterprise.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

A seconda dell'opzione di configurazione selezionata, lo script crea gli elementi riportati di seguito.

**Per gli account RunAs:**

* Crea un'applicazione Azure AD da esportare con la chiave pubblica del certificato autofirmato o enterprise. Crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile cambiare l'impostazione in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Crea un asset di certificato di Automazione denominato *AzureRunAsCertificate* nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.
* Crea un asset di connessione di Automazione denominato *AzureRunAsConnection* nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

**Per gli account RunAs classici:**

* Crea un asset di certificato di Automazione denominato *AzureClassicRunAsCertificate* nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.
* Crea un asset di connessione di Automazione denominato *AzureClassicRunAsConnection* nell'account di Automazione specificato. L'asset di connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset di certificato.

>[!NOTE]
> Se si seleziona una delle due opzioni per creare l'account RunAs classico, dopo l'esecuzione dello script è necessario caricare il certificato pubblico, con estensione cer, nell'archivio di gestione della sottoscrizione in cui è stato creato l'account di Automazione.
> 

Per eseguire lo script e caricare il certificato, seguire questa procedura:

1. Salvare lo script seguente nel computer. Per questo esempio, salvare il file con il nome *New-RunAsAccount.ps1*.

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
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. Fare clic sul menu **Start** del computer e avviare **Windows PowerShell** con diritti utente elevati.

3. Dalla shell della riga di comando di PowerShell con privilegi elevati passare alla cartella che contiene lo script creato nel passaggio 1.

4. Eseguire lo script usando i valori dei parametri per la configurazione richiesta.

    **Creare un account RunAs usando un certificato autofirmato**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Creare un account RunAs e un account RunAs classico usando un certificato autofirmato**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Creare un account RunAs e un account RunAs classico usando un certificato enterprise**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Dopo l'esecuzione dello script, viene richiesto di autenticarsi con Azure. Accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
    >
    >

Al termine dell'esecuzione dello script, tenere presente quanto segue:
* Se è stato creato un account RunAs classico con un certificato pubblico autofirmato, con estensione cer, lo script lo crea e lo salva nella cartella dei file temporanei del computer con il profilo utente *%USERPROFILE%\AppData\Local\Temp*, usato per eseguire la sessione di PowerShell.
* Se è stato creato un account RunAs classico con un certificato pubblico enterprise, con estensione cer, usare questo certificato. Seguire le istruzioni per [caricare un certificato dell'API di gestione nel portale di Azure classico](../azure-api-management-certs.md) e quindi usare il [codice di esempio per l'autenticazione con le risorse di Service Management](#sample-code-to-authenticate-with-service-management-resources) per convalidare la configurazione delle credenziali con tali risorse. 
* Se *non* è stato creato un account RunAs classico, eseguire l'autenticazione con le risorse di Resource Manager e convalidare la configurazione delle credenziali usando il [codice di esempio per l'autenticazione con le risorse di Service Management](#sample-code-to-authenticate-with-resource-manager-resources).

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Codice di esempio per l'autenticazione con le risorse di Resource Manager
È possibile usare il codice di esempio aggiornato seguente, tratto dal runbook di esempio *AzureAutomationTutorialScript*, per eseguire l'autenticazione usando l'account RunAs per gestire le risorse di Resource Manager con i runbook.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
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

Per poter lavorare facilmente in più sottoscrizioni, lo script include due righe di codice aggiuntive che supportano il riferimento al contesto di una sottoscrizione. Un asset della variabile denominato *SubscriptionId* contiene l'ID della sottoscrizione. Dopo l'istruzione del cmdlet `Add-AzureRmAccount`, il cmdlet [`Set-AzureRmContext`](/powershell/module/azurerm.profile/set-azurermcontext) viene dichiarato con il set di parametri *-SubscriptionId*. Se il nome della variabile è troppo generico, è possibile modificarlo includendo un prefisso o usando un'altra convenzione di denominazione per identificarla più facilmente. In alternativa, è possibile usare il set di parametri *-SubscriptionName* anziché *-SubscriptionId* con un asset della variabile corrispondente.

Il cmdlet `Add-AzureRmAccount` usato per l'autenticazione nel runbook usa il set di parametri *ServicePrincipalCertificate* ed esegue l'autenticazione usando il certificato dell'entità servizio, anziché le credenziali utente.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Codice di esempio per l'autenticazione con le risorse di Service Management
È possibile usare il codice di esempio aggiornato seguente, tratto dal runbook di esempio *AzureClassicAutomationTutorialScript*, per eseguire l'autenticazione usando l'account RunAs classico per gestire le risorse classiche con i runbook.

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

## <a name="next-steps"></a>Passaggi successivi
* [Oggetti applicazione e oggetti entità servizio in Azure AD](../active-directory/active-directory-application-objects.md)
* [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md)
* [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md)

