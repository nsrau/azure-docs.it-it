---
title: Aggiornamento di Azure Stack 1805 | Documenti Microsoft
description: Informazioni sulle novità nell'aggiornamento 1805 per i sistemi Azure Stack integrato, inclusi i problemi noti e come scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cb6c4d5cd1d63403c102f7d09741eba4932a79bb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850509"
---
# <a name="azure-stack-1805-update"></a>Aggiornamento dello Stack 1805 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1805, conosciuti per questa versione e come scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi direttamente correlati al processo di aggiornamento e con la compilazione (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento    
È il numero di build aggiornamento Azure Stack 1805 **1.1805.1.47**.  

> [!TIP]  
> In base alle opinioni dei clienti, è un aggiornamento allo schema di versione in uso per lo Stack di Microsoft Azure.  A partire da questo aggiornamento, 1805, il nuovo schema rappresenti meglio la versione corrente di cloud.  
> 
> Lo schema di versione è ora *Version.YearYearMonthMonth.MinorVersion.BuildNumber* in cui i set di secondo e terzi indicano la versione e rilascio. Ad esempio, 1805.1 rappresenta il *release alla produzione* (RTM) versione 1805.  


### <a name="new-features"></a>Nuove funzionalità
Questo aggiornamento include i miglioramenti seguenti per lo Stack di Azure.
<!-- 2297790 - IS, ASDK --> 
- **Stack di Azure include ora un *Syslog* client** come una *funzionalità di anteprima*. Questa consente l'inoltro dei registri di controllo e sicurezza correlata all'infrastruttura di Azure Stack un Syslog server o la sicurezza informazioni ed eventi (SIEM) software di gestione che è esterno allo Stack di Azure. Attualmente, il client Syslog supporta solo le connessioni non autenticate UDP sulla porta predefinita 514. Il payload di ogni messaggio Syslog è formattato in comune formato dell'evento (CEF). 

  Per configurare il client di Syslog, utilizzare il **Set-SyslogServer** cmdlet esposta nell'Endpoint con privilegi. 

  Con questa versione di anteprima, è possibile visualizzare gli avvisi di tre seguenti. Quando verrà visualizzata dallo Stack di Azure, questi avvisi includono *descrizioni* e *correzione* informazioni aggiuntive. 
  - TITOLO: Integrità del codice Off  
  - TITOLO: Integrità del codice in modalità di controllo 
  - TITOLO: L'Account utente creato

  Anche se questa funzionalità è disponibile in anteprima, è necessario non è affidabile in ambienti di produzione.   




### <a name="fixed-issues"></a>Problemi risolti

<!-- # - applicability -->


- **Varie correzioni** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Prima di iniziare    

### <a name="prerequisites"></a>Prerequisiti
- Installare lo Stack di Azure [1804 aggiornare](azure-stack-update-1804.md) prima di applicare l'aggiornamento di Azure Stack 1805.    
- Prima di iniziare l'installazione dell'aggiornamento 1805, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) per convalidare lo stato dello Stack di Azure e risolvere eventuali problemi operativi trovati. Inoltre esaminare gli avvisi attivi e risolvere gli eventuali che richiedono un'azione. 

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento   
- Durante l'installazione dell'aggiornamento 1805, si potrebbero visualizzare avvisi con il titolo *Error: modello per il tipo FaultType UserAccounts.New mancante.*  È possibile ignorare questi avvisi. Questi avvisi verranno chiuso automaticamente dopo l'aggiornamento a 1805 viene completato.   

- <!-- 2489559 - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
*Non sono passaggi post-aggiornamento per aggiornamento 1805.*


## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono i problemi noti di post-installazione per questa versione di build.

### <a name="portal"></a>Portale  
- <!-- 2551834 - IS, ASDK --> When you select **Overview** for a storage account in either the admin or user portals, the information from the *Essentials* pane does not display.  The Essentials pane displays information about the account like its *Resource group*, *Location*, and *Subscription ID*.  Other options for Overview  are accessible, like *Services* and *Monitoring*, as well as options to *Open in Explorer* or to *Delete storage account*. 

  Per visualizzare le informazioni non disponibili, usare il [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet di PowerShell. 

- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Soluzione alternativa: Per risolvere questo problema dopo aver installato questo aggiornamento, passaggio 3 da utilizzare il [automazione Trigger per configurare le attestazioni trust del provider in Stack Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedure per reimpostare il proprietario della sottoscrizione del provider predefinito.   

- <!-- TBD - IS ASDK --> Some administrative subscription types are not available.  When you upgrade Azure Stack to this version, the two subscription types that were [introduced with version 1804](azure-stack-update-1804.md#new-features) are not visible in the console. This is expected. The unavailable subscription types are *Metering subscription*, and *Consumption subscription*. These subscription types are visible in new Azure Stack environments beginning with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Barra di navigazione](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Avviso #1:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità Heartbeat Scanner è disponibile. Ciò potrebbe influire su rapporti di stato e le metriche.  

  Avviso #2:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità scanner di codici di errore non è disponibile. Ciò potrebbe influire su rapporti di stato e le metriche.

  Entrambi gli avvisi possono essere tranquillamente ignorati e verrà chiuso automaticamente nel corso del tempo.  

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


### <a name="compute"></a>Calcolo
- <!-- TBD - IS, ASDK --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. Ogni metodo, è necessario specificare la dimensione di macchina virtuale che si desidera utilizzare.

  - **Modello di gestione risorse di Azure:** quando si utilizza un modello, impostare il *vmSize* nel modello per la dimensione di macchina virtuale da usare è uguale. Ad esempio, la voce seguente consente di distribuire una macchina virtuale che utilizza il *F32s_v2* dimensioni:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI di Azure:** è possibile usare il [creare vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e specificare le dimensioni della VM come parametro, simile a `--size "Standard_F32s_v2"`.

  - **PowerShell:** con PowerShell è possibile utilizzare [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) con il parametro che specifica le dimensioni della VM, simile a `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


### <a name="networking"></a>Rete
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tenta di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale originale e non a quello nuovo.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased.

  Per risolvere questo problema, utilizzare un piano del componente aggiuntivo per aumentare la Quota di rete quando il piano è già associato a una sottoscrizione. Per altre informazioni, vedere come [rendere disponibile un piano del componente aggiuntivo](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription.


- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group.

    Soluzione alternativa per il servizio App: se è necessario per il desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Di seguito sono riportati esempi di come *consentire*e quindi ripristinare la configurazione per *negare*:  

    - *Consenti:*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn’t work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Negazione:*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn’t work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
        ```


### <a name="sql-and-mysql"></a>SQL e MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1805, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially. For example, if you use version 1803, first apply version 1804, and then update to 1805.      
>   
> L'installazione dell'aggiornamento 1805 non influenza l'uso del provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse a cui che si utilizza, agli utenti di dati nei database non sono interessati e rimangono accessibili.    



### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- 2489178 - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.


### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1805 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica del processo di gestione nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
