---
title: Configurare un Account RunAs di Azure | Microsoft Docs
description: "Esercitazione che illustra come creare, testare e usare l&quot;autenticazione dell&quot;entità di sicurezza in Automazione di Azure."
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
ms.date: 03/27/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 6f2a3880c6cd307282020a689ddd4e22a95c17b0
ms.lasthandoff: 03/15/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Autenticare runbook con account RunAs di Azure
Questo argomento illustra come configurare un account di Automazione dal portale di Azure usando la funzionalità dell'account RunAs per autenticare i runbook che gestiscono risorse in Azure Resource Manager o Azure Service Management.

Quando si crea un nuovo account di Automazione nel portale di Azure, viene creato automaticamente quanto segue:

* Account RunAs che crea una nuova entità servizio in Azure Active Directory e un certificato e assegna il ruolo Collaboratore per il controllo degli accessi in base al ruolo, che verrà usato per gestire le risorse di Resource Manager con i runbook.   
* Account RunAs classico caricando un certificato di gestione che verrà usato per gestire le risorse classiche o di Azure Service Management con i runbook.  

Questo semplifica il processo e consente di iniziare rapidamente la compilazione e distribuzione di runbook per supportare le esigenze di automazione.      

Con un account RunAs e un account RunAs classico, è possibile:

* Standardizzare l'autenticazione con Azure quando si gestiscono risorse di Azure Resource Manager o Azure Service Management con i runbook nel portale di Azure.  
* Automatizzare l'uso di runbook globali configurati negli avvisi di Azure.

> [!NOTE]
> La [funzionalità di integrazione degli avvisi](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) di Azure con i runbook globali di automazione richiede un account di Automazione configurato con un account RunAs e un account RunAs classico. È possibile selezionare un account di Automazione per cui sono già definiti un account RunAs e un account RunAs classico o scegliere di crearne uno nuovo.
>  

L'articolo illustra come creare l'account di Automazione dal Portale di Azure, come aggiornare un account di Automazione con PowerShell, come gestire la configurazione dell'account e come eseguire l'autenticazione nei runbook.

Prima di procedere, è consigliabile comprendere e considerare alcuni aspetti.

1. Quanto descritto in questo articolo non influisce sugli account di Automazione esistenti già creati nel modello di distribuzione classica o Resource Manager.  
2. Quanto descritto in questo articolo si applica solo agli account di Automazione creati tramite il portale di Azure.  Provando a creare un account dal portale classico, non verrà replicata la configurazione dell'account RunAs.
3. Se sono presenti runbook e asset (pianificazioni, variabili e così via) creati in precedenza per gestire risorse classiche e si vuole che tali runbook eseguano l'autenticazione con il nuovo account RunAs classico, è necessario creare un account RunAs classico con Managing an Run As Account (Gestione dell'account RunAs) oppure aggiornare l'account esistente usando lo script di PowerShell riportato più avanti.  
4. Per eseguire l'autenticazione con il nuovo account RunAs e il nuovo account RunAs classico di Automazione, è necessario modificare i runbook esistenti con il codice di esempio riportato più avanti.  **Si noti** che l'account RunAs verrà usato per l'autenticazione per le risorse di Resource Manager con l'entità servizio basata su certificato, mentre l'account RunAs classico verrà usato per l'autenticazione per le risorse di Service Management con il certificato di gestione.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Creare un account di Automazione dal Portale di Azure
La procedura descritta in questa sezione consente di creare un nuovo account di Automazione di Azure dal portale di Azure.  Vengono così creati l'account RunAs e l'account RunAs classico.  

> [!NOTE]
> L'utente che segue questa procedura deve essere membro del ruolo Amministratori del servizio o coamministratore della sottoscrizione che concede l'accesso alla sottoscrizione per l'utente. Deve anche essere aggiunto come utente alla directory di Active Directory predefinita della sottoscrizione. Non è necessario che all'account sia assegnato un ruolo con privilegi. Gli utenti che non sono membri dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di coamministratore della sottoscrizione verranno aggiunti ad Active Directory come guest e riceveranno un avviso di "autorizzazioni non sufficienti" nel pannello **Aggiungi account di Automazione**. Gli utenti che prima sono stati aggiunti al ruolo di coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione è possibile usare il riquadro **Azure Active Directory** nel portale di Azure. A tale scopo selezionare **Utenti e gruppi**, **Tutti gli utenti**, selezionare l'utente specifico e quindi scegliere **Profilo**.  Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.  
> 

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Selezionare **Account di automazione**.
3. Nel pannello Account di automazione fare clic su **Aggiungi**.<br>![Aggiungi account di Automazione](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Se nel pannello **Aggiungi account di Automazione** viene visualizzato l'avviso seguente, l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.<br>![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di Automazione.
5. Se si hanno più sottoscrizioni, specificarne una per il nuovo account, un **Gruppo di risorse** nuovo o esistente e la **Località** del data center di Azure.
6. Verificare che sia selezionato il valore **Sì** per l'opzione **Crea un account RunAs di Azure** e fare clic sul pulsante **Crea**.  
   
   > [!NOTE]
   > Se si sceglie di non creare l'account RunAs selezionando l'opzione **No**, verrà visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**.  L'account verrà creato nel portale di Azure, ma non avrà un'identità di autenticazione corrispondente nel servizio directory della sottoscrizione classica o Resource Manager e, di conseguenza, non avrà accesso alle risorse nella sottoscrizione.  Questo impedirà ai runbook che fanno riferimento a questo account di eseguire l'autenticazione ed eseguire attività sulle risorse in tali modelli di distribuzione.
   > 
   > ![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.
   > 

7. Mentre Azure crea l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="resources-included"></a>Risorse incluse
Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse.  La tabella seguente offre un riepilogo delle risorse per l'account RunAs.<br>

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureAutomationTutorial |Runbook grafico di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialScript |Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| AzureRunAsCertificate |Asset di certificato creato automaticamente durante la creazione dell'account di Automazione oppure usando lo script di PowerShell riportato più avanti per un account esistente.  Consente di eseguire l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager con i runbook.  La durata di questo certificato è di un anno. |
| AzureRunAsConnection |Asset di connessione creato automaticamente durante la creazione dell'account di Automazione oppure usando lo script di PowerShell riportato più avanti per un account esistente. |

La tabella seguente offre un riepilogo delle risorse per l'account RunAs classico.<br>

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Runbook grafico di esempio che ottiene tutte le VM classiche in una sottoscrizione usando l'account RunAs classico (certificato) e quindi restituisce il nome e lo stato delle VM. |
| Runbook di script AzureClassicAutomationTutorial |Runbook di PowerShell di esempio che ottiene tutte le VM classiche in una sottoscrizione usando l'account RunAs classico (certificato) e quindi restituisce il nome e lo stato delle VM. |
| AzureClassicRunAsCertificate |Asset di certificato creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook.  La durata di questo certificato è di un anno. |
| AzureClassicRunAsConnection |Asset di connessione creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. |

## <a name="verify-run-as-authentication"></a>Verificare l'autenticazione con RunAs
A questo punto verrà eseguito un breve test per verificare che sia possibile eseguire l'autenticazione usando il nuovo account RunAs.     

1. Nel Portale di Azure aprire l'account di Automazione creato in precedenza.  
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook **AzureAutomationTutorialScript** e quindi fare clic su **Avvia** per avviarlo.  Verrà visualizzata la richiesta di confermare l'avvio del runbook.
4. Verrà creato un [processo del runbook](automation-runbook-execution.md) e verrà visualizzato il pannello Processo con lo stato del processo nel riquadro **Riepilogo processi** .  
5. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
6. Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.<br> ![Verifica del runbook dell'entità di sicurezza](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output** .
8. Nel pannello **Output** dovrebbe risultare che l'autenticazione è stata completata e che è stato restituito un elenco di tutte le risorse disponibili nel gruppo di risorse.
9. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.
10. Chiudere **Riepilogo processi** e il pannello del runbook **AzureAutomationTutorialScript** corrispondente.

## <a name="verify-classic-run-as-authentication"></a>Verificare l'autenticazione con RunAs classico
A questo punto verrà eseguito un breve test per verificare che sia possibile eseguire l'autenticazione usando il nuovo account RunAs classico.     

1. Nel Portale di Azure aprire l'account di Automazione creato in precedenza.  
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook **AzureClassicAutomationTutorialScript** e quindi fare clic su **Avvia** per avviarlo.  Verrà visualizzata la richiesta di confermare l'avvio del runbook.
4. Verrà creato un [processo del runbook](automation-runbook-execution.md) e verrà visualizzato il pannello Processo con lo stato del processo nel riquadro **Riepilogo processi** .  
5. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
6. Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.<br> ![Verifica del runbook dell'entità di sicurezza](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output** .
8. Nel pannello **Output** dovrebbe risultare che l'autenticazione è stata completata e che è stato restituito un elenco di tutte le VM classiche nella sottoscrizione.
9. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.
10. Chiudere **Riepilogo processi** e il pannello del runbook **AzureClassicAutomationTutorialScript** corrispondente.

## <a name="managing-azure-run-as-account"></a>Gestione dell'account RunAs di Azure
Per tutta la durata dell'account di automazione, è necessario rinnovare il certificato prima della scadenza oppure, se si ritiene che l'account sia stato compromesso, è possibile eliminare l'account RunAs e ricrearlo.  In questa sezione verrà illustrata la procedura dettagliata su come eseguire queste operazioni.  

### <a name="self-signed-certificate-renewal"></a>Rinnovo del certificato autofirmato
Il certificato autofirmato creato per l'account RunAs di Azure può essere rinnovato in qualsiasi momento fino alla scadenza, ovvero fino a un anno dalla data di creazione.  Quando lo si rinnova, il vecchio certificato valido verrà mantenuto per garantire che tutti i runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs, non subiranno modifiche.  Il certificato continuerà a esistere fino alla scadenza.    

> [!NOTE]
> Se l'account RunAs di Automazione è stato configurato per l'uso di un certificato emesso dall'Autorità di certificazione globale (enterprise) e si usa questa opzione, tale certificato verrà sostituito da un certificato autofirmato.  

1. Nel Portale di Azure aprire l'account di Automazione.  
2. Nel pannello Account di automazione, nel riquadro delle proprietà dell'account, selezionare **Account RunAs** nella sezione **Impostazioni account**.<br><br> ![Riquadro delle proprietà dell'account di automazione](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. Nel riquadro delle proprietà dell'**account RunAs**, selezionare l'Account RunAs o l'Account RunAs classico per cui si desidera rinnovare il certificato e nel pannello delle proprietà dell'account selezionato, fare clic su **Rinnova certificato**.<br><br> ![Rinnovare il certificato per l'account RunAs](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> Verrà richiesto di confermare l'operazione.  
4. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="delete-run-as-account"></a>Eliminare un account RunAs
I passaggi seguenti descrivono come eliminare e ricreare l'account RunAs di Azure o l'account RunAs classico.  Quando si esegue questa operazione l'account di automazione viene mantenuto.  Dopo aver eliminato l'account RunAs o l'account RunAs classico, è possibile ricrearlo nel portale.  

1. Nel Portale di Azure aprire l'account di Automazione.  
2. Nel pannello Account di automazione, nel riquadro delle proprietà dell'account, selezionare **Account RunAs** nella sezione **Impostazioni account**.
3. Nel riquadro delle proprietà dell'**account RunAs**, selezionare l'Account RunAs o l'Account RunAs classico che si desidera eliminare e nel pannello delle proprietà dell'account selezionato, quindi fare clic su **Elimina**.<br><br> ![Eliminare un account RunAs](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  Verrà richiesto di confermare l'operazione.
4. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.  Al termine dell'eliminazione, è possibile ricreare l'account dal pannello delle proprietà dell'**account RunAs**, selezionando l'opzione di creazione **Account RunAs di Azure**.<br><br> ![Ricreare l'account RunAs di automazione](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>Errore di configurazione
Se uno degli elementi di configurazione necessari per il corretto funzionamento dell'account RunAs o dell'account Run As classico viene eliminato o non è stato creato correttamente durante l'installazione iniziale, ad esempio:

* Asset del certificato 
* Asset di connessione 
* Account RunAs rimosso dal ruolo di collaboratore
* Entità servizio o applicazione in Azure AD

l'Automazione rileverà queste modifiche e invierà una notifica con l'indicazione di stato **Incompleto** nel pannello delle proprietà **Account RunAs** dell'account.<br><br> ![Messaggio dello stato di configurazione di RunAs Incompleto](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>Quando si seleziona l'account RunAs, nel riquadro delle proprietà dell'account viene visualizzato l'errore seguente:<br><br> ![Messaggio di avviso relativo alla configurazione di RunAs incompleta](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).<br>  
Se l'account RunAs non è configurato correttamente, è possibile risolvere rapidamente questo problema eliminando e ricreando l'account RunAs.   

## <a name="update-an-automation-account-using-powershell"></a>Aggiornare un account di automazione tramite PowerShell
Verrà ora illustrata la possibilità di usare PowerShell per aggiornare l'account di Automazione esistente se:

1. È stato creato un account di Automazione ma si è scelto di non creare l'account RunAs 
2. Si ha già un account di Automazione per gestire le risorse di Resource Manager e lo si vuole aggiornare per includere l'account RunAs per l'autenticazione dei runbook
4. Si ha già un account di Automazione per gestire le risorse classiche e lo si vuole aggiornare per usare l'account RunAs classico invece di creare un nuovo account ed eseguire la migrazione di runbook e asset al nuovo account   
5. Si vuole creare un account RunAs di Azure e un account RunAs classico usando un certificato emesso dalla CA globale (enterprise)

Per questo script esistono i prerequisiti seguenti:

1. Questo script supporta l'esecuzione solo in Windows 10 e Windows Server 2016 con i moduli di Azure Resource Manager 2.01 e versioni successive.  Non sono supportate le versioni precedenti di Windows.  
2. Azure PowerShell 1.0 e versioni successive. Per informazioni su questa versione e su come installarla, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
3. È stato creato un account di automazione.  A questo account viene fatto riferimento come valore per i parametri -AutomationAccountName e -ApplicationDisplayName nello script riportato più avanti in questo articolo.

Per ottenere i valori per *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, che costituiscono parametri obbligatori per gli script, nel portale di Azure selezionare l'account di Automazione nel pannello **Account di automazione** e quindi **Tutte le impostazioni**.  Nel pannello **Tutte le impostazioni** selezionare **Proprietà** in **Impostazioni account**.  Nel pannello **Proprietà** è possibile prendere nota di questi valori.<br><br> ![Proprietà dell'account di Automazione](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Creare lo script di PowerShell per l'account RunAs
Questo script di PowerShell include il supporto per le configurazioni seguenti: 

* Creare un account RunAs di Azure usando il certificato autofirmato
* Creare un account RunAs di Azure e un account RunAs classico di Azure usando il certificato autofirmato
* Creare un account RunAs di Azure e un account RunAs classico di Azure usando il certificato enterprise
* Creare un account RunAs di Azure e un account RunAs classico di Azure usando il certificato autofirmato nel cloud Azure per enti pubblici

Verrà creato quanto segue a seconda dell'opzione di configurazione selezionata:

* Un'applicazione Azure AD che verrà esportata con la chiave pubblica del certificato autofirmato o enterprise. Verrà creato un account dell'entità servizio per questa applicazione in Azure AD e a questo account verrà assegnato il ruolo Collaboratore (modificabile in Proprietario o in qualsiasi altro ruolo) nella sottoscrizione corrente.  Per altre informazioni, vedere l'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Un asset di certificato di automazione nell'account di Automazione specificato, denominato **AzureRunAsCertificate**, che contiene la chiave privata del certificato usata dall'applicazione Azure AD.
* Un asset della connessione di automazione nell'account di Automazione specificato, denominato **AzureRunAsConnection**, che contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.    

Per l'account RunAs classico:

* Un asset di certificato di automazione nell'account di Automazione specificato, denominato **AzureClassicRunAsCertificate**, che contiene la chiave privata del certificato usata dal certificato di gestione.  
* Un asset della connessione di automazione nell'account di Automazione specificato, denominato **AzureClassicRunAsConnection**, che contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

Se si seleziona l'opzione per creare l'account RunAs classico, dopo l'esecuzione dello script sarà necessario caricare il certificato pubblico (nel formato con estensione cer) nell'archivio di gestione della sottoscrizione in cui è stato creato l'account di Automazione. La procedura seguente illustra il processo di esecuzione dello script e caricamento del certificato.    

1. Salvare lo script seguente nel computer.  Per questo esempio, salvare il file con il nome **New-RunAsAccount.ps1**.  
   
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
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use Key credentials and create AAD Application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
   
        # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
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

        # Create Run As Account using Service Principal
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

        # Create Service Principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId} 

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues  

        if ($CreateClassicRunAsAccount) {  
            # Create Run As Account using Service Principal
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

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
3. Dalla shell della riga di comando di PowerShell con privilegi elevati passare alla cartella che contiene lo script creato nel passaggio 1 ed eseguire lo script impostando i valori dei parametri richiesti in base alla configurazione necessaria.  

    **Creare un account RunAs di Azure usando il certificato autofirmato**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false` 

    **Creare un account RunAs di Azure e un account RunAs classico di Azure usando il certificato autofirmato**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Creare un account RunAs di Azure e un account RunAs classico di Azure usando il certificato enterprise**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Creare un account RunAs di Azure e un account RunAs classico di Azure usando il certificato autofirmato nel cloud Azure per enti pubblici**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`
 
    > [!NOTE]
    > Verrà richiesto di autenticarsi con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
    > 
    > 

Al termine dell'esecuzione dello script, se è stato creato un account RunAs classico con un certificato pubblico autofirmato in formato CER, viene creato e salvato nella cartella dei file temporanei del computer nel profilo utente usato per eseguire la sessione di PowerShell, *%USERPROFILE%\AppData\Local\Temp*. Se invece è stato creato un account RunAs classico con un certificato pubblico aziendale in formato CER, è necessario usare questo certificato.  Seguire la procedura per [caricare un certificato dell'API di gestione](../azure-api-management-certs.md) nel portale di Azure classico e quindi vedere il [codice di esempio](#sample-code-to-authenticate-with-service-management-resources) per convalidare la configurazione delle credenziali con le risorse di Service Management.  Se non è stato creato un account RunAs classico, per eseguire l'autenticazione con le risorse di Resource Manager e convalidare la configurazione delle credenziali vedere il [codice di esempio](#sample-code-to-authenticate-with-resource-manager-resources) riportato di seguito.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Codice di esempio per l'autenticazione con le risorse di Resource Manager
È possibile usare il codice di esempio aggiornato seguente, tratto dal runbook di esempio **AzureAutomationTutorialScript** , per eseguire l'autenticazione usando l'account RunAs per gestire le risorse di Resource Manager con i runbook.   

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


Lo script include due righe di codice aggiuntive per supportare il riferimento al contesto di una sottoscrizione per poter lavorare facilmente in più sottoscrizioni. Un asset di variabile denominato SubscriptionId contiene l'ID della sottoscrizione e dopo l'istruzione del cmdlet Add-AzureRmAccount è riportata l'istruzione del [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) con il set di parametri *-SubscriptionId*. Se il nome della variabile è troppo generico, è possibile modificarlo includendo un prefisso o un'altra convenzione di denominazione per identificarla più facilmente in base alle proprie esigenze. In alternativa, è possibile usare il set di parametri -SubscriptionName invece di -SubscriptionId con un asset della variabile corrispondente.    

Si noti che il cmdlet usato per l'autenticazione nel runbook (**Add-AzureRmAccount**) usa il set di parametri *ServicePrincipalCertificate*  ed esegue l'autenticazione usando il certificato dell'entità servizio, non le credenziali.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Codice di esempio per l'autenticazione con le risorse di Service Management
È possibile usare il codice di esempio aggiornato seguente, tratto dal runbook di esempio **AzureClassicAutomationTutorialScript** , per eseguire l'autenticazione usando l'account RunAs classico per gestire le risorse classiche con i runbook.

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
* Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).
* Per altre informazioni sul controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md)


