---
title: Aggiornamento di Azure Stack 1803 | Documenti Microsoft
description: Informazioni sulle novità nell'aggiornamento 1803 per lo Stack di Azure integrati sistemi, i problemi noti e come scaricare l'aggiornamento.
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
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 26c77b706f17f49eff782e6d0d73087050739874
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-1803-update"></a>Aggiornamento dello Stack 1803 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1803, conosciuti per questa versione e come scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi direttamente correlati al processo di aggiornamento e con la compilazione (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento    
È il numero di build aggiornamento Azure Stack 1803 **20180329.1**.


## <a name="before-you-begin"></a>Prima di iniziare    
> [!IMPORTANT]    
> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per ulteriori informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Prerequisiti
- Installare lo Stack di Azure [1802 aggiornare](azure-stack-update-1802.md) prima di applicare l'aggiornamento di Azure Stack 1803.    


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
- Dopo l'installazione di 1803, installare gli aggiornamenti rapidi applicabili. Per ulteriori informazioni, vedere i seguenti articoli della knowledge base, nonché il nostro [manutenzione criteri](azure-stack-servicing-policy.md).

  - [KB 4103348 - servizio API di Controller di rete si blocca quando si tenta di installare un aggiornamento dello Stack di Azure](https://support.microsoft.com/en-us/help/4103348)

- Dopo aver installato questo aggiornamento, esaminare la configurazione del firewall per garantire [porte necessarie](azure-stack-integrate-endpoints.md) sono aperte. Ad esempio, questo aggiornamento introduce monitoraggio di Azure che include una modifica dei registri di controllo per i log di attività. Con questa modifica, porta 13012 viene ora usata e deve essere aperta anche.  

### <a name="new-features"></a>Nuove funzionalità 
Questo aggiornamento include i seguenti miglioramenti e correzioni per lo Stack di Azure.

- **Aggiornare i segreti Azure Stack** - (account e dei certificati). Per ulteriori informazioni sulla gestione dei segreti, vedere [ruotare i segreti nello Stack di Azure](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   Anche come parte di questa modifica, sotto **altri servizi**, *log di controllo* viene visualizzato come *log attività*. La funzionalità è coerenza con il portale di Azure. 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   I file sparse sono un formato di file efficiente utilizzato per ridurre l'uso di spazio di archiviazione e migliorare i/o.  Per altre informazioni, vedere [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) per Windows Server. 

### <a name="fixed-issues"></a>Problemi risolti

- <!-- 1739988 --> Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 1868283 --> Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324 --> Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- Quando si crea una macchina virtuale, il messaggio *non è possibile visualizzare i prezzi* non viene più visualizzata quando si sceglie una dimensione per le dimensioni della VM.

- Varie correzioni per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure.


### <a name="changes"></a>Modifiche
- Il modo per modificare lo stato di un'offerta appena creato da *privato* a *pubblica* o *rimosse* è stato modificato. Per altre informazioni, vedere [creare un'offerta](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento    
<!-- 2328416 --> During installation of the 1803 update, there can be downtime of the blob service and internal services that use blob service. This includes some virtual machine operations. This down time can cause failures of tenant operations or alerts from services that can’t access data. This issue resolves itself when the update completes installation. 


### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono i problemi noti di post-installazione per la compilazione **20180323.2**.

#### <a name="portal"></a>Portale
- Il possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per lo Stack di Azure usare sistemi integrati, https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Che non sia possibile visualizzare le risorse di calcolo o di archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo non corretto come completata correttamente. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- Quando si visualizzano le proprietà di una risorsa o un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Lo spostamento di risorse o gruppi di risorse tra gruppi di risorse o le sottoscrizioni non è attualmente supportato.

- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarla.

- Nel portale di amministrazione, è possibile notare un avviso critico per la *Microsoft.Update.Admin* componente. Il nome dell'avviso, la descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

  Questo avviso può essere tranquillamente ignorato. 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Marketplace
- Gli utenti di sfogliare il marketplace completo senza una sottoscrizione e possono vedere gli elementi di amministrazione come piani e le offerte. Questi elementi sono non funzionale agli utenti.



#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- Quando si crea un set di disponibilità in del portale, passare a **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento 1. In alternativa, quando si crea una nuova macchina virtuale, creare il set tramite l'interfaccia CLI, PowerShell o dall'interno di disponibilità il portale.

- Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie DS. Macchine virtuali di serie DS possono contenere tutti i dischi di dati come la configurazione di Azure.

- Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

-  Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Rete
- Dopo aver creata e associata a un indirizzo IP pubblico di una macchina virtuale, è non è possibile annullare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo.



- Stack di Azure supporta un singolo *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, le successive tenta di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP sono bloccati.

- In una rete virtuale che è stato creato con un'impostazione di Server DNS di *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate a macchine virtuali in tale rete virtuale.

- Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, che devono essere definite in fase di distribuzione.

- <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Soluzione alternativa per il servizio App: se è necessario per il desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Di seguito sono riportati esempi di come *consentire*e quindi ripristinare la configurazione per *negare*:  
    
    - *Consenti:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- Prima di procedere, rivedere la nota importante in [prima di iniziare](#before-you-begin) all'inizio di queste note sulla versione.

- Può richiedere fino a un'ora prima che gli utenti possono creare database in una nuova distribuzione di SQL o MySQL.

- Per creare gli elementi nei server di tale host SQL o MySQL, è supportato solo il provider di risorse. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  


> [!NOTE]  
> Dopo l'aggiornamento alla 1803 dello Stack di Azure, è possibile continuare a utilizzare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare MySQL e SQL Server quando diventa disponibile una nuova versione. Come Stack di Azure, applicare gli aggiornamenti in sequenza per i provider di risorse MySQL e SQL Server.  Ad esempio, se si utilizza versione 1711, applicare prima versione 1712 quindi 1802 e quindi aggiornare a 1803.      
>   
> L'installazione dell'aggiornamento 1803 non influenza l'uso del provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse a cui che si utilizza, agli utenti di dati nei database non sono interessati e rimangono accessibili.    



#### <a name="app-service"></a>Servizio app
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.


#### <a name="usage"></a>Uso  
- Dati misuratore sull'utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* stamp che indica quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si utilizza il *invoke-webrequest* da Github degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure, viene visualizzato un errore:     
    -  *Invoke-webrequest: la richiesta è stata interrotta: Impossibile creare un canale protetto SSL/TLS.*     

  Questo errore si verifica a causa di un recente deprecazione di supporto di GitHub degli Tlsv1 e Tlsv1.1 crittografia standard (impostazione predefinita per PowerShell). Per ulteriori informazioni, vedere [si noti la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

  Per risolvere questo problema, aggiungere `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` all'inizio dello script per forzare la console di PowerShell per utilizzare TLSv1.2 durante il download dal repository di GitHub.


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1803 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica del processo di gestione nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
