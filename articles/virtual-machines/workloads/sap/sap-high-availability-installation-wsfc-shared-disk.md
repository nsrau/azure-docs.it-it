---
title: Installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e nel disco condiviso per un'istanza ASCS/SCS di SAP in Azure | Microsoft Docs
description: Informazioni su come installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e nel disco condiviso per un'istanza ASCS/SCS di SAP.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c914afb3a72ccac4753a1942b6c4303850654a2e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682639"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e nel disco condiviso per un'istanza ASCS/SCS di SAP in Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

Questo articolo descrive come installare e configurare un sistema SAP a disponibilità elevata in Azure usando un cluster di failover Windows Server e un disco condiviso del cluster per il clustering di un'istanza ASCS/SCS di SAP. Come descritto in [Guida all'architettura: cluster di un'istanza di SAP ASC/SCS in un cluster di failover Windows tramite un disco condiviso del cluster][sap-high-availability-guide-wsfc-shared-disk], sono disponibili due alternative per il *disco condiviso del cluster*:

- [Dischi condivisi di Azure](../../disks-shared.md)
- Uso di il [clustering Datakeeper cluster Edition](https://us.sios.com/products/datakeeper-cluster/) per creare l'archiviazione con mirroring, che simula il disco condiviso del cluster 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'installazione, esaminare questi documenti:

* [Architecture guide: Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk][sap-high-availability-guide-wsfc-shared-disk](Guida all'architettura: clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows che usa un disco condiviso del cluster)

* [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP usando un cluster di failover Windows e un disco condiviso per un'istanza di SAP ASC/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

In questo articolo non viene trattata la configurazione del sistema DBMS perché varia a seconda del sistema DBMS usato. Si presuppone che i problemi di disponibilità elevata del sistema DBMS vengano risolti con le funzionalità supportate dai diversi fornitori di sistemi DBMS per Azure, ad esempio Gruppi di disponibilità AlwaysOn o mirroring del database per SQL Server e Oracle Data Guard per database Oracle. Gli scenari di disponibilità elevata per il sistema DBMS non sono trattati in questo articolo.

Non esistono particolari considerazioni per il caso in cui servizi DBMS differenti interagiscano con una configurazione ASCS o SCS di SAP in cluster in Azure.

> [!NOTE]
> La procedura di installazione dei sistemi SAP NetWeaver ABAP, Java e ABAP + Java è praticamente identica. La differenza principale è che un sistema SAP ABAP ha un'istanza di ASCS. Il sistema SAP Java ha un'istanza di SCS. Il sistema SAP ABAP + Java ha un'istanza di ASCS e un'istanza di SCS in esecuzione nello stesso gruppo cluster di failover Microsoft. Eventuali differenze di installazione per ogni stack di installazione di SAP NetWeaver verranno indicate in modo esplicito. Si può presupporre che gli altri passaggi siano uguali.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installare SAP con un'istanza di ASC/SCS a disponibilità elevata

> [!IMPORTANT]
> Se si usa il sistema di indicizzazione per presentare il disco condiviso, non inserire il file di paging nei volumi con mirroring di datakeeper di Hangul. È possibile lasciare il file di paging nell'unità temporanea D di una macchina virtuale di Azure, ovvero l'impostazione predefinita. Se non è già presente, spostare il file di paging di Windows nell'unità D della macchina virtuale di Azure.  


L'installazione di SAP con un'istanza di ASCS/SCS a disponibilità elevata prevede queste attività:

* Creare un nome host virtuale per l'istanza ASCS/SCS di SAP in cluster.
* Installare SAP nel primo nodo del cluster.
* Modificare il profilo SAP dell'istanza di ASCS/SCS.
* Aggiungere una porta probe.
* Aprire la porta probe di Windows Firewall.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Creare un nome host virtuale per l'istanza di SAP ASC/SCS in cluster

1. In Gestore DNS di Windows creare una voce DNS per il nome host virtuale dell'istanza di ASCS/SCS.

   > [!IMPORTANT]
   > L'indirizzo IP assegnato al nome host virtuale dell'istanza di ASC/SCS deve corrispondere all'indirizzo IP assegnato a Azure Load Balancer.  
   
   
   ![Figura 1: Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

   _Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS_

2. Se usa il nuovo server di replica di Accodamento SAP 2, che è anche un'istanza in cluster, è necessario riservare il nome host virtuale a DNS per ERS2. 

   > [!IMPORTANT]
   > L'indirizzo IP assegnato al nome host virtuale dell'istanza di ERS2 deve corrispondere al secondo indirizzo IP assegnato Azure Load Balancer.    
   
   
   ![Figura 1A: definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASC/SCS][sap-ha-guide-figure-3046-ers2]

   _Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ERS2_


3. Per definire l'indirizzo IP assegnato al nome host virtuale, selezionare **gestore DNS**  >  **dominio**.

   ![Figura 2: Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster SAP ASC/SCS_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installare il primo nodo del cluster SAP

1. Eseguire l'opzione del primo nodo del cluster nel nodo A del cluster. Selezionare:

   * Per il **sistema ABAP**: **ASCS** numero di istanza **00**
   * Per il **sistema Java**: **SCS** numero di istanza **01**
   * Per il **sistema ABAP + Java**: **ASCS** numero di istanza **00** e **SCS** numero di istanza **01**

   
   > [!IMPORTANT]
   > Tenere presente che la configurazione nelle regole di bilanciamento del carico del servizio di bilanciamento del carico interno di Azure (se si usa lo SKU Basic) e i numeri di istanza di SAP selezionati devono corrispondere.

2. Seguire la procedura di installazione di SAP descritta. Assicurarsi che l'opzione Avvia installazione "primo nodo del cluster" scelga "disco condiviso cluster" come opzione di configurazione.

> [!TIP]
> La documentazione di installazione di SAP descrive come installare il primo nodo del cluster ASCS/SCS.



### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificare il profilo SAP dell'istanza di ASCS/SCS

Se è presente il server di replica di Accodamento 1, aggiungere il parametro del profilo SAP `enque/encni/set_so_keepalive` come descritto di seguito. Questo parametro impedisce la chiusura delle connessioni tra i processi di lavoro SAP e il server di accodamento quando sono inattivi per un tempo eccessivo. Il parametro SAP non è obbligatorio per ERS2. 

1. Aggiungere questo parametro del profilo al profilo dell'istanza di SAP ASC/SCS, se si usa ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Per ERS1 e ERS2, assicurarsi che i parametri del `keepalive` sistema operativo siano impostati come descritto nella nota SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Per applicare le modifiche apportate al parametro del profilo SAP, riavviare l'istanza di SAP ASC/SCS.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Aggiungere una porta probe

Usare la funzionalità probe del servizio di bilanciamento del carico interno per il corretto funzionamento della configurazione del cluster con Azure Load Balancer. Il servizio di bilanciamento del carico interno di Azure distribuisce in genere il carico di lavoro in ingresso in modo uniforme tra le macchine virtuali.

Questa operazione non funziona tuttavia in alcune configurazioni di cluster perché è attiva una sola istanza. L'altra istanza è passiva e non può accettare carico di lavoro. Una funzionalità Probe aiuta quando il servizio di bilanciamento del carico interno di Azure rileva quale istanza è attiva e ha come destinazione solo l'istanza attiva.  

> [!IMPORTANT]
> In questa configurazione di esempio, **ProbePort** è impostato su 620 **Nr**. Per l'istanza di SAP ASC con numero **00** , è 620 **00**. Sarà necessario modificare la configurazione in modo che corrisponda ai numeri di istanza di SAP e al SID SAP.

Per aggiungere una porta Probe, eseguire questo modulo di PowerShell in una delle macchine virtuali del cluster:

- Nel caso di un'istanza di SAP ASC/SCS 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- Se si usa ERS2, che è in cluster. Non è necessario configurare la porta Probe per ERS1, perché non è in cluster.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
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
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
                    
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

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Aprire la porta probe di Windows Firewall

Aprire una porta probe di Windows Firewall in entrambi i nodi del cluster. Usare lo script seguente per aprire una porta probe di Windows Firewall. Aggiornare le variabili PowerShell per l'ambiente.  
Se si usa ERS2, sarà necessario aprire anche la porta del firewall per la porta Probe ERS2.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installare l'istanza di database

Per installare l'istanza di database, seguire la procedura descritta nella documentazione relativa all'installazione di SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installare il secondo nodo del cluster

Per installare il secondo cluster, seguire la procedura descritta nella guida all'installazione di SAP.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installare Primary Application Server (PAS) SAP

Installare l'istanza del server applicazioni principale (PAS) \<SID\> -di-0 nella macchina virtuale designata per ospitare la pas. Non sono presenti dipendenze in Azure. Se si usa il numero di impostazioni, non sono disponibili impostazioni specifiche di datakeeper.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installare Additional Application Server (AAS) SAP

Installare un'istanza di Additional Application Server (AAS) SAP in tutte le macchine virtuali designate per ospitare un'istanza del server applicazioni SAP. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testare il failover dell'istanza di SAP ASC/SCS

Per i test di failover delineati si presuppone che SAP ASC sia attivo nel nodo A.  

1. Verificare che il sistema SAP sia in grado di eseguire correttamente il failover dal nodo A al nodo B scegliere una di queste opzioni per avviare un failover del \<SID\> gruppo di cluster SAP dal nodo a al nodo b del cluster:
    - Gestione cluster di failover  
    - PowerShell per Clustering di failover

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Riavviare il nodo A del cluster all'interno del sistema operativo guest Windows. Viene avviato un failover automatico del \<SID\> gruppo di cluster SAP dal nodo a al nodo B.  
3. Riavviare il nodo A del cluster dal portale di Azure. Viene avviato un failover automatico del \<SID\> gruppo di cluster SAP dal nodo a al nodo B.  
4. Riavviare il nodo A del cluster usando Azure PowerShell. Viene avviato un failover automatico del \<SID\> gruppo di cluster SAP dal nodo a al nodo B.

5. Verifica
   - Dopo il failover, verificare che il \<SID\> gruppo di cluster SAP sia in esecuzione nel nodo B del cluster. 

      ![Figura 8: In Gestione cluster di failover il gruppo di cluster \<SID\> di SAP è in esecuzione nel nodo B del cluster][sap-ha-guide-figure-5002]

      _In Gestione cluster di failover, il \<SID\> gruppo di cluster SAP è in esecuzione nel nodo B del cluster_

   - Dopo il failover, verificare che il disco condiviso sia ora montato nel nodo B del cluster. 
   - Dopo il failover, se si usa il numero di istanze, verificare che la replica dei dati dall'unità S del volume di origine nel nodo B del cluster nell'unità del volume di destinazione nel nodo A del cluster venga eseguita. 

      ![Figura 9: SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster][sap-ha-guide-figure-5003]

      _Il valore di Hangul datakeeper replica il volume locale dal nodo B al nodo a del cluster_