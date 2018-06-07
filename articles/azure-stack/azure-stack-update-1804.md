---
title: Aggiornamento di Azure Stack 1804 | Documenti Microsoft
description: Informazioni sulle novità nell'aggiornamento 1804 per lo Stack di Azure integrati sistemi, i problemi noti e come scaricare l'aggiornamento.
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
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 2c2813a7f2d909a23c8f5d4f5ac0280b3f932ba6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700125"
---
# <a name="azure-stack-1804-update"></a>Aggiornamento dello Stack 1804 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1804, conosciuti per questa versione e come scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi direttamente correlati al processo di aggiornamento e con la compilazione (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento    
È il numero di build aggiornamento Azure Stack 1804 **20180513.1**.   

### <a name="new-features"></a>Nuove funzionalità
Questo aggiornamento include i miglioramenti seguenti per lo Stack di Azure.

- <!-- 15028744 - IS -->  **Visual Studio support for disconnected Azure Stack deployments using AD FS**. Within Visual Studio you now can add subscriptions and authenticate using AD FS federated User credentials. 
 
- <!-- 1779474, 1779458 - IS --> **Use Av2 and F series virtual machines**. Azure Stack can now use virtual machines based on the Av2-series and F-series virtual machine sizes. For more information see [Virtual machine sizes supported in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  Se si aggiorna Stack Azure alla versione 1804, i due nuovi tipi di sottoscrizione non sono visibili. Tuttavia, nuove distribuzioni di Azure Stack integrati sistemi e le installazioni della versione del Kit di sviluppo di Azure Stack 1804 o versioni successiva possono accedere a tutti i tipi di sottoscrizione tre.  

  Questi nuovi tipi di sottoscrizione fanno parte di una modifica di dimensioni maggiore per proteggere la sottoscrizione del Provider predefinito e per semplificare la distribuzione di risorse condivise come server di Hosting SQL. Man mano che si aggiungono altre parti di questa modifica di dimensioni maggiore con gli aggiornamenti futuri allo Stack di Azure, le risorse distribuite in questi nuovi tipi di sottoscrizione potrebbero andare perse. 

  I tipi di tre sottoscrizione ora visibili sono:  
  - Sottoscrizione di Provider predefinito: continuare a utilizzare questo tipo di sottoscrizione. 
  - Sottoscrizione di controllo: *non utilizzano questo tipo di sottoscrizione.*
  - Sottoscrizione di utilizzo: *non utilizzano questo tipo di sottoscrizione*

  



## <a name="fixed-issues"></a>Problemi risolti

- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information.
 
- <!-- 2050709  -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.
 
- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option.

- **Varie correzioni** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure.

## <a name="additional-releases-timed-with-this-update"></a>Versioni aggiuntive programmate con questo aggiornamento  
Di seguito è ora disponibili, ma non richiede l'aggiornamento dello Stack Azure 1804.
- **Aggiornamento in Microsoft Azure Stack System Center Operations Manager Monitoring Pack**. È disponibile per una nuova versione (1.0.3.0) di Microsoft System Center Operations Manager Monitoring Pack per Azure Stack [scaricare](https://www.microsoft.com/download/details.aspx?id=55184). Con questa versione, è possibile utilizzare le entità servizio quando si aggiunge una distribuzione di Azure Stack connessa. Questa versione include anche un'esperienza di gestione degli aggiornamenti che consente di eseguire l'azione di correzione direttamente all'interno di Operations Manager. Sono inoltre disponibili nuovi dashboard che visualizzano i provider di risorse, unità di scala e ridimensionare i nodi di unità.

- **Nuova versione di Azure Stack amministrazione PowerShell versione 1.3.0**.  Azure PowerShell Stack 1.3.0 è ora disponibile per l'installazione. Questa versione sono disponibili comandi per tutti i provider di risorse di amministrazione gestire Azure Stack.  Con questa versione, alcuni contenuti verranno deprecati a partire da Azure Stack strumenti GitHub [repository](https://github.com/Azure/AzureStack-Tools). 

   Per informazioni dettagliate di installazione, seguire le [istruzioni](azure-stack-powershell-install.md) o il [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per il modulo di Azure Stack 1.3.0. 

- **Versione del riferimento all'API Rest di Azure Stack iniziale**. Il [riferimento API per tutti i provider di risorse di Azure Stack Admin](https://docs.microsoft.com/rest/api/azure-stack/) ora viene pubblicato. 


## <a name="before-you-begin"></a>Prima di iniziare    

### <a name="prerequisites"></a>Prerequisiti
- Installare lo Stack di Azure [1803 aggiornare](azure-stack-update-1803.md) prima di applicare l'aggiornamento di Azure Stack 1804.    

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento   
- Durante l'installazione dell'aggiornamento 1804, si potrebbero visualizzare avvisi con il titolo *Error: modello per il tipo FaultType UserAccounts.New mancante.*  È possibile ignorare questi avvisi. Questi avvisi verranno chiuso automaticamente dopo l'aggiornamento a 1804 viene completato.   
 
- <!-- TBD - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
*Non sono passaggi post-aggiornamento per aggiornamento 1804.*



### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono i problemi noti di post-installazione per la compilazione **20180513.1**.

#### <a name="portal"></a>Portale
- <!-- 1272111 - IS --> After you install or update to this version of Azure Stack, you might not be able to view Azure Stack scale units in the Admin portal.  
  Soluzione alternativa: Utilizzare PowerShell per visualizzare le informazioni sulle unità di scala. Per altre informazioni, vedere la [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per il modulo di Azure Stack 1.3.0. 

- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Soluzione alternativa: Per risolvere questo problema dopo aver installato questo aggiornamento, passaggio 3 da utilizzare il [automazione Trigger per configurare le attestazioni trust del provider in Stack Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedure per reimpostare il proprietario della sottoscrizione del provider predefinito.   

- <!-- TBD - IS ASDK --> Some administrative subscription types are not available.  When you upgrade Azure Stack to this version, the two subscription types that were [introduced with version 1804](#new-features) are not visible in the console. This is expected. The unavailable subscription types are *Metering subscription*, and *Consumption subscription*. These subscription types are visible in new Azure Stack environments beginning with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  


- <!-- TBD -  IS ASDK -->The ability [to open a new support request from the dropdown](azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Per lo Stack di Azure usare sistemi integrati, https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Barra di navigazione](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

- <!-- TBD - IS ASDK --> In the admin portal, you might see a critical alert for the *Microsoft.Update.Admin* component. The Alert name, description, and remediation all display as:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

  Questo avviso può essere tranquillamente ignorato. 


#### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
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

  Entrambi gli avvisi possono essere tranquillamente ignorati. Verrà chiusa automaticamente nel corso del tempo.  
 

#### <a name="compute"></a>Calcolo
- <!-- TBD - IS --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. Ogni metodo, è necessario specificare la dimensione di macchina virtuale che si desidera utilizzare.

  - **Modello di gestione risorse di Azure:** quando si utilizza un modello, impostare il *vmSize* nel modello per la dimensione di macchina virtuale desiderata è uguale. Ad esempio, nell'esempio viene utilizzato per distribuire una macchina virtuale che utilizza il *F32s_v2* dimensioni:  

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

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Rete
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo.

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

#### <a name="sql-and-mysql"></a>SQL e MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1804, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially.  For example, if you use version 1802, first apply version 1803, and then update to 1804.      
>   
> L'installazione dell'aggiornamento 1804 non influenza l'uso del provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse a cui che si utilizza, agli utenti di dati nei database non sono interessati e rimangono accessibili.    



#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.

- <!-- TBD - IS ASDK --> App Service can only be deployed into the **Default Provider Subscription** at this time.  In a future update App Service will deploy into the new Metering Subscription introduced in Azure Stack 1804 and all existing deployments will be migrated to this new subscription also.

#### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1804 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica del processo di gestione nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
