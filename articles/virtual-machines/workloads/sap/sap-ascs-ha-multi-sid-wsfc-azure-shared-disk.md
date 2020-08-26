---
title: Disponibilità elevata dei SID di SAP ASC/SCS con WSFC e dischi condivisi di Azure | Microsoft Docs
description: Disponibilità elevata a più SID per un'istanza di SAP ASC/SCS con WSFC e un disco condiviso di Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4856b2578a007f72aeeec64588ac7f9c58158de
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861183"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Disponibilità elevata a più SID dell'istanza di SAP ASC/SCS con Windows Server failover clustering e dischi condivisi di Azure

> ![Sistema operativo Windows][Logo_Windows] Windows
>

Questo articolo è incentrato su come passare da un'installazione di ASC/SCS singola a una configurazione di SAP a più SID tramite l'installazione di istanze aggiuntive del cluster ASC/SCS di SAP in un cluster WSFC (Windows Server Failover Clustering) esistente con un disco condiviso di Azure. Al termine di questo processo, verrà configurato un cluster multi-SID di SAP.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limiti

Attualmente è possibile usare i dischi di Azure SSD Premium come disco condiviso di Azure per l'istanza di SAP ASC/SCS. Sono disponibili le seguenti limitazioni:

-  Il [disco Azure ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) non è supportato come disco condiviso di Azure per i carichi di lavoro SAP. Attualmente non è possibile inserire macchine virtuali di Azure usando il disco Ultra di Azure in un set di disponibilità
-  Il [disco condiviso di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared) con dischi SSD Premium è supportato solo con le VM nel set di disponibilità. Non è supportata nella distribuzione di zone di disponibilità. 
-  Il valore [maxShares](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable?tabs=azure-cli#disk-sizes) del disco condiviso di Azure determina il numero di nodi del cluster che possono usare il disco condiviso. In genere, per l'istanza di SAP ASC/SCS verranno configurati due nodi nel cluster di failover di Windows, pertanto il valore di `maxShares` deve essere impostato su due.
-  Tutte le macchine virtuali del cluster SAP ASC/SCS devono essere distribuite nello stesso [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups).   
   Sebbene sia possibile distribuire le macchine virtuali del cluster Windows nel set di disponibilità con il disco condiviso di Azure senza PPG, PPG assicurerà la vicinanza fisica dei dischi condivisi di Azure e delle macchine virtuali del cluster, ottenendo pertanto una latenza più bassa tra le macchine virtuali e il livello di archiviazione.    

Per altri dettagli sulle limitazioni per il disco condiviso di Azure, vedere attentamente la sezione [limitazioni](https://docs.microsoft.com/azure/virtual-machines/linux/disks-shared#limitations) della documentazione su dischi condivisi di Azure.  

> [!IMPORTANT]
> Quando si distribuisce un cluster di failover di Windows SAP ASC/SCS con disco condiviso di Azure, tenere presente che la distribuzione funzionerà con un singolo disco condiviso in un cluster di archiviazione. L'istanza di SAP ASC/SCS avrà un effetto, in caso di problemi con il cluster di archiviazione, in cui viene distribuito il disco condiviso di Azure.  

> [!IMPORTANT]
> Il programma di installazione deve soddisfare le condizioni seguenti:
> * Ogni SID DBMS deve avere un cluster WSFC dedicato.  
> * I server applicazioni SAP che fanno parte di un SID del sistema SAP devono avere proprie VM dedicate.  
> * Una combinazione del server di replica di Accodamento 1 e del server di replica di Accodamento 2 nello stesso cluster non è supportata.  


## <a name="supported-os-versions"></a>Versioni dei sistemi operativi supportate

Sono supportati sia Windows Server 2016 che Windows Server 2019 (usare le immagini data center più recenti).

Si consiglia vivamente di utilizzare **Windows Server 2019 datacenter**, come:
- Il servizio cluster di failover di Windows 2019 è in grado di riconoscere Azure
- Sono stati aggiunti l'integrazione e la conoscenza della manutenzione dell'host di Azure e l'esperienza migliorata monitorando gli eventi di pianificazione di Azure.
- È possibile utilizzare il nome di rete distribuita (opzione predefinita). Non è pertanto necessario disporre di un indirizzo IP dedicato per il nome di rete del cluster. Non è inoltre necessario configurare questo indirizzo IP nel Load Balancer interno di Azure. 

## <a name="architecture"></a>Architecture

Il server di replica di Accodamento (ERS1) e il server di replica di Accodamento 2 (ERS2) sono supportati nella configurazione a più SID.  Una combinazione di ERS1 e ERS2 non è supportata nello stesso cluster. 

1. Il primo esempio mostra due SID SAP, entrambi con l'architettura ERS1, in cui:

   - SAP SID1 viene distribuito in un disco condiviso con ERS1. L'istanza ERS viene installata nell'host locale e nell'unità locale.
     SAP SID1 dispone di un proprio indirizzo IP (virtuale) (SID1 (A) SCS IP1), configurato nel servizio di bilanciamento del carico interno di Azure.

   - SAP SID2 viene distribuito in un disco condiviso con ERS1. L'istanza ERS viene installata nell'host locale e nell'unità locale.
     SAP SID2 dispone di un proprio indirizzo IP (virtuale) (SID2 (A) SCS IP2), configurato anche nel servizio di bilanciamento del carico interno di Azure.

![Istanza di SAP ASC/SCS a disponibilità elevata: due istanze con configurazione ERS1][sap-ha-guide-figure-6007]

2. Il secondo esempio mostra due SID SAP, entrambi con l'architettura ERS2, in cui: 

   - SAP SID1 con ERS2, è anche in cluster ed è distribuito nell'unità locale.  
     SAP SID1 dispone di un proprio indirizzo IP (virtuale) (SID1 (A) SCS IP1), configurato nel servizio di bilanciamento del carico interno di Azure.
     SAP ERS2, usato dal sistema SAP SID1, dispone di un proprio indirizzo IP (virtuale) (SID1 ERS2 IP2), configurato nel servizio di bilanciamento del carico interno di Azure.

   - SAP SID2 con ERS2, è anche in cluster ed è distribuito nell'unità locale.  
     SAP SID2 dispone di un proprio indirizzo IP (virtuale) (SID2 (A) SCS IP3), configurato nel servizio di bilanciamento del carico interno di Azure.
     SAP ERS2, usato dal sistema SAP SID2, dispone di un proprio indirizzo IP (virtuale) (SID2 ERS2 IP4), configurato nel servizio di bilanciamento del carico interno di Azure.

   Qui abbiamo un totale di quattro indirizzi IP virtuali:  
   - SID1 (A) SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A) SCS IP3
   - SID2 ERS2 IP4

![Istanza di SAP ASC/SCS a disponibilità elevata: due istanze con configurazione ERS1 e ERS2][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Preparazione dell'infrastruttura

Verrà installato un nuovo SID SAP **Pr2**, oltre all'istanza di SAP **Pr1** ASC/SCS in **cluster esistente** .  

### <a name="host-names-and-ip-addresses"></a>Nomi host e indirizzi IP

| Ruolo nome host | Nome host | Indirizzo IP statico | Set di disponibilità | Gruppo posizionamento prossimità |
| --- | --- | --- |---| ---|
| primo cluster ASC/SCS del nodo cluster |PR1-ASC-10 |10.0.0.4 |PR1-ASC-avset |PR1PPG |
| 2 ° nodo cluster ASC/SCS cluster |PR1-ASC-11 |10.0.0.5 |PR1-ASC-avset |PR1PPG |
| Nome rete di cluster | pr1clust |10.0.0.42 (**solo** per il cluster Win 2016) | n/d | n/d |
| **SID1** Nome rete cluster ASC | PR1-ascscl |10.0.0.43 | n/d | n/d |
| **SID1** Nome di rete del cluster ERS (**solo** per ERS2) | PR1-erscl |10.0.0.44 | n/d | n/d |
| **SID2** Nome rete cluster ASC | Pr2-ascscl |10.0.0.45 | n/d | n/d |
| **SID2** Nome di rete del cluster ERS (**solo** per ERS2) | PR1-erscl |10.0.0.46 | n/d | n/d |

### <a name="create-azure-internal-load-balancer"></a>Creare un servizio di bilanciamento del carico interno di Azure

SAP ASC, SAP SCS e la nuova ERS2 SAP, usano il nome host virtuale e gli indirizzi IP virtuali. In Azure è necessario un servizio di [bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) per usare un indirizzo IP virtuale. Si consiglia vivamente di usare [Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). 

Sarà necessario aggiungere la configurazione al servizio di bilanciamento del carico esistente per la seconda istanza di SAP SID ASC/SCS/ERS **Pr2**. La configurazione per il primo SID SAP **Pr1** dovrebbe essere già attiva.  

**Un SCS PR2 [numero di istanza 02]**
- Configurazione front-end
    - Indirizzo IP ASC/SCS statico **10.0.0.45**
- Configurazione back-end  
    Già presente: le macchine virtuali sono già state aggiunte al pool back-end, durante la configurazione di SAP SID **Pr1**
- Porta probe
    - Porta 620**Nr** [**62002**] lasciare l'opzione predefinita per protocollo (TCP), intervallo (5), soglia non integro (2)
- Regole di bilanciamento del carico
    - Se si usa Load Balancer Standard, selezionare Porte a disponibilità elevata
    - Se si usa Load Balancer Basic, creare regole di bilanciamento del carico per le porte seguenti
        - 32**Nr** TCP [**3202**]
        - 36**Nr** TCP [**3602**]
        - 39**Nr** TCP [**3902**]
        - 81**Nr** TCP [**8102**]
        - 5**Nr**13 TCP [**50213**]
        - 5**Nr**14 TCP [**50214**]
        - 5**Nr**16 TCP [**50216**]
        - Associare l'indirizzo IP front-end ASC di **Pr2** , il probe di integrità e il pool back-end esistente.  

    - Verificare che il timeout di inattività (minuti) sia impostato sul valore massimo 30 e che l'indirizzo IP mobile (Direct Server Return) sia abilitato.

**ERS2 PR2 [numero di istanza 12]** 

Poiché il server di replica di Accodamento 2 (ERS2) è anche in cluster, è necessario configurare anche l'indirizzo IP virtuale ERS2 in Azure ILB, oltre a quello di SAP ASC/SCS IP. Questa sezione si applica solo se si usa l'architettura del server di replica di Accodamento per **Pr2**.  
- Nuova configurazione front-end
    - Indirizzo IP di SAP ERS2 statico **10.0.0.46**

- Configurazione back-end  
  Le macchine virtuali sono già state aggiunte al pool back-end ILB.  

- Nuova porta Probe
    - Porta 621**Nr**  [**62112**] lasciare l'opzione predefinita per protocollo (TCP), intervallo (5), soglia non integro (2)

- Nuove regole di bilanciamento del carico
    - Se si usa Load Balancer Standard, selezionare Porte a disponibilità elevata
    - Se si usa Load Balancer Basic, creare regole di bilanciamento del carico per le porte seguenti
        - 32**Nr** TCP [**3212**]
        - 33**Nr** TCP [**3312**]
        - 5**Nr**13 TCP [**51212**]
        - 5**Nr**14 TCP [**51212**]
        - 5**Nr**16 TCP [**51212**]
        - Associare l'indirizzo IP front-end ERS2 di **Pr2** , il probe di integrità e il pool back-end esistente.  

    - Verificare che il timeout di inattività (minuti) sia impostato sul valore massimo, ad esempio 30, e che l'indirizzo IP mobile (Direct Server Return) sia abilitato.


### <a name="create-and-attach-second-azure-shared-disk"></a>Creare e alleghi il secondo disco condiviso di Azure

Eseguire questo comando in uno dei nodi del cluster. Sarà necessario modificare i valori per il gruppo di risorse, l'area di Azure, SAPSID e così via.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Formattare il disco condiviso con PowerShell
1. Ottenere il numero del disco. Eseguire i comandi di PowerShell in uno dei nodi del cluster:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formattare il disco. In questo esempio il disco è numero 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Verificare che il disco sia ora visibile come disco del cluster.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Registrare il disco nel cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Creare un nome host virtuale per l'istanza ASCS/SCS di SAP in cluster

1. Creare una voce DNS per il nome host virtuale per la nuova istanza di SAP ASC/SCS nel gestore DNS di Windows.  
   L'indirizzo IP assegnato al nome host virtuale in DNS deve corrispondere all'indirizzo IP assegnato in Azure Load Balancer.  

   ![_Define la voce DNS per il nome virtuale e l'indirizzo IP del cluster SAP ASC/SCS][sap-ha-guide-figure-6009]
 
   _Definire la voce DNS per il nome virtuale e l'indirizzo IP del cluster SAP ASC/SCS_

2. Se si usa il server di replica di Accodamento di SAP, che è anche un'istanza in cluster, è necessario riservare il nome host virtuale a DNS per ERS2. 
   L'indirizzo IP assegnato al nome host virtuale per ERS2 in DNS deve corrispondere all'indirizzo IP assegnato in Azure Load Balancer.  

   ![_Define la voce DNS per il nome virtuale e l'indirizzo IP del cluster SAP ERS2][sap-ha-guide-figure-6010]
 
   _Definire la voce DNS per il nome virtuale e l'indirizzo IP del cluster SAP ERS2_

3. Per definire l'indirizzo IP assegnato al nome host virtuale, selezionare **gestore DNS**  >  **dominio**.

   ![Nuovo nome virtuale e indirizzo IP per la configurazione del cluster SAP ASC/SCS e ERS2][sap-ha-guide-figure-6011]

   _Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster SAP ASC/SCS e ERS2_

## <a name="sap-installation"></a>Installazione di SAP 

### <a name="install-the-sap-first-cluster-node"></a>Installare il primo nodo del cluster SAP

Seguire la procedura di installazione di SAP descritta. Assicurarsi nell'opzione Avvia installazione "primo nodo del cluster" e scegliere "disco condiviso cluster" come opzione di configurazione.  
Scegliere la nuova creazione del disco condiviso.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Modificare il profilo SAP dell'istanza di ASC/SCS

Se si esegue l'Accodamento server di replica 1, aggiungere il parametro del profilo SAP `enque/encni/set_so_keepalive` come descritto di seguito. Questo parametro impedisce la chiusura delle connessioni tra i processi di lavoro SAP e il server di accodamento quando sono inattivi per un tempo eccessivo. Il parametro SAP non è obbligatorio per ERS2. 

1. Aggiungere questo parametro del profilo al profilo dell'istanza di SAP ASC/SCS, se si usa ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Per ERS1 e ERS2, assicurarsi che i parametri del `keepalive` sistema operativo siano impostati come descritto nella nota SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Per applicare le modifiche apportate al parametro del profilo SAP, riavviare l'istanza di SAP ASC/SCS.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Configurare la porta Probe sulla risorsa cluster

Usare la funzionalità probe del servizio di bilanciamento del carico interno per il corretto funzionamento della configurazione del cluster con Azure Load Balancer. Il servizio di bilanciamento del carico interno di Azure distribuisce in genere il carico di lavoro in ingresso in modo uniforme tra le macchine virtuali.

Questa operazione non funziona tuttavia in alcune configurazioni di cluster perché è attiva una sola istanza. L'altra istanza è passiva e non può accettare carico di lavoro. Una funzionalità Probe aiuta quando il servizio di bilanciamento del carico interno di Azure rileva quale istanza è attiva e ha come destinazione solo l'istanza attiva.  

> [!IMPORTANT]
> In questa configurazione di esempio, **ProbePort** è impostato su 620**Nr**. Per l'istanza di SAP ASC con numero **02** è 620**02**.
> Sarà necessario modificare la configurazione in modo che corrisponda ai numeri di istanza di SAP e al SID SAP.

Per aggiungere una porta Probe, eseguire questo modulo di PowerShell in una delle macchine virtuali del cluster:

- Nel caso di un'istanza di SAP ASC/SCS con numero di istanza **02** 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Se si usa ERS2, con il numero di istanza **12**, che è in cluster. Non è necessario configurare la porta Probe per ERS1, perché non è in cluster.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 Il codice per la funzione `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` avrà un aspetto simile al seguente:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Continua con l'installazione di SAP

1. Installare l'istanza del database seguendo il processo descritto nella Guida all'installazione di SAP.  
2. Installare SAP nel secondo nodo del cluster attenendosi alla procedura descritta nella Guida all'installazione di SAP.  
3. Installare l'istanza del server applicazioni primario (PAS) SAP nella macchina virtuale che è stata designata per ospitare la PAS.   
   Seguire la procedura descritta nella Guida all'installazione di SAP. Non sono presenti dipendenze in Azure.
4. Installare ulteriori server applicazioni SAP nelle macchine virtuali, designate per ospitare le istanze del server applicazioni SAP.  
   Seguire la procedura descritta nella Guida all'installazione di SAP. Non sono presenti dipendenze in Azure. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testare il failover dell'istanza di SAP ASC/SCS
Per i test di failover delineati si presuppone che SAP ASC sia attivo nel nodo A.  

1. Verificare che il sistema SAP sia in grado di eseguire correttamente il failover dal nodo A al nodo B. In questo esempio, il test viene eseguito per SAPSID **Pr2**.  
   Verificare che ogni SAPSID possa essere spostato correttamente nell'altro nodo del cluster.   
   Scegliere una di queste opzioni per avviare un failover del \<SID\> gruppo di cluster SAP dal nodo a al nodo B del cluster:
    - Gestione cluster di failover  
    - PowerShell per Clustering di failover

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Riavviare il nodo A del cluster all'interno del sistema operativo guest Windows. Viene avviato un failover automatico del \<SID\> gruppo di cluster SAP dal nodo a al nodo B.  
3. Riavviare il nodo A del cluster dal portale di Azure. Viene avviato un failover automatico del \<SID\> gruppo di cluster SAP dal nodo a al nodo B.  
4. Riavviare il nodo A del cluster usando Azure PowerShell. Viene avviato un failover automatico del \<SID\> gruppo di cluster SAP dal nodo a al nodo B.

## <a name="next-steps"></a>Passaggi successivi

* [Preparazione dell'infrastruttura di Azure per la disponibilità elevata di SAP con il cluster di failover Windows e i dischi condivisi per l'istanza SAP (A)SCS][sap-high-availability-infrastructure-wsfc-shared-disk]
* [Installazione della disponibilità elevata di SAP NetWeaver nel cluster di failover Windows e nei dischi condivisi per l'istanza SAP (A)SCS in Azure][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md