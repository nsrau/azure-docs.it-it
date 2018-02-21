---
title: "Installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e nel disco condiviso per un'istanza ASCS/SCS di SAP in Azure | Microsoft Docs"
description: "Informazioni su come installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e nel disco condiviso per un'istanza ASCS/SCS di SAP."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 419bbdd57a391dbbf01c2110a1609cb3d0ded003
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e nel disco condiviso per un'istanza ASCS/SCS di SAP in Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

Questo articolo descrive come installare e configurare un sistema SAP a disponibilità elevata in Azure usando un cluster di failover Windows Server e un disco condiviso del cluster per il clustering di un'istanza ASCS/SCS di SAP.

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare l'installazione, esaminare questi documenti:

* [Architecture guide: Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk][sap-high-availability-guide-wsfc-shared-disk](Guida all'architettura: clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows che usa un disco condiviso del cluster)

* [Preparazione dell'infrastruttura di Azure per la disponibilità elevata di SAP con il cluster di failover Windows e i dischi condivisi per l'istanza ASCS/SCS di SAP][sap-high-availability-infrastructure-wsfc-shared-disk]

In questo articolo non viene trattata la configurazione del sistema DBMS perché varia a seconda del sistema DBMS usato. Si presuppone che i problemi di disponibilità elevata del sistema DBMS vengano risolti con le funzionalità supportate dai diversi fornitori di sistemi DBMS per Azure, ad esempio Gruppi di disponibilità AlwaysOn o mirroring del database per SQL Server e Oracle Data Guard per database Oracle. Nello scenario usato in questo articolo, non sono state aggiunte altre funzionalità di protezione per il sistema DBMS.

Non esistono particolari considerazioni per il caso in cui servizi DBMS differenti interagiscano con una configurazione ASCS o SCS di SAP in cluster in Azure.

> [!NOTE]
> La procedura di installazione dei sistemi SAP NetWeaver ABAP, Java e ABAP + Java è praticamente identica. La differenza principale è che un sistema SAP ABAP ha un'istanza di ASCS. Il sistema SAP Java ha un'istanza di SCS. Il sistema SAP ABAP + Java ha un'istanza ASCS e un'istanza SCS in esecuzione nello stesso gruppo di cluster di failover Microsoft. Eventuali differenze di installazione per ogni stack di installazione di SAP NetWeaver verranno indicate in modo esplicito. Si presume che tutte le altre parti siano uguali.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installare SAP con un'istanza ASCS/SCS a disponibilità elevata

> [!IMPORTANT]
> Non inserire il file di paging nei volumi con mirroring di SIOS DataKeeper. DataKeeper non supporta i volumi con mirroring. È possibile lasciare il file di paging nell'unità temporanea D di una macchina virtuale di Azure, ovvero l'impostazione predefinita. Se non è già presente, spostare il file di paging di Windows nell'unità D della macchina virtuale di Azure.
>
>

L'installazione di SAP con un'istanza di ASCS/SCS a disponibilità elevata prevede queste attività:

* Creare un nome host virtuale per l'istanza ASCS/SCS di SAP in cluster.
* Installare il primo nodo del cluster SAP.
* Modificare il profilo SAP dell'istanza di ASCS/SCS.
* Aggiungere una porta probe.
* Aprire la porta probe di Windows Firewall.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Creare un nome host virtuale per l'istanza ASCS/SCS di SAP in cluster

1.  In Gestore DNS di Windows creare una voce DNS per il nome host virtuale dell'istanza di ASCS/SCS.

  > [!IMPORTANT]
  > L'indirizzo IP assegnato al nome host virtuale dell'istanza di ASCS/SCS deve essere lo stesso indirizzo IP assegnato ad Azure Load Balancer (\<SID\>-lb-ascs).  
  >
  >

  L'indirizzo IP del nome host virtuale di SAP ASCS/SCS (pr1-ascs-sap) è lo stesso indirizzo IP di Azure Load Balancer (pr1-lb-ascs).

  ![Figura 1: Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

  _**Figura 1:** Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS_

2.  Per definire l'indirizzo IP assegnato al nome host virtuale, selezionare **Gestore DNS** > **Dominio**.

  ![Figura 2: Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Figura 2:** Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installare il primo nodo del cluster SAP

1.  Eseguire l'opzione del primo nodo del cluster nel nodo A, ad esempio nell'host pr1-ascs-0*.
2.  Per mantenere le porte predefinite per il bilanciamento del carico interno di Azure, selezionare:

  * Per il **sistema ABAP**: **ASCS** numero di istanza **00**
  * Per il **sistema Java**: **SCS** numero di istanza **01**
  * Per il **sistema ABAP + Java**: **ASCS** numero di istanza **00** e **SCS** numero di istanza **01**

  Per usare numeri di istanza diversi da 00 per l'istanza ABAP ASCS e 01 per l'istanza Java SCS, modificare in primo luogo le regole di bilanciamento del carico predefinite del servizio di bilanciamento del carico interno di Azure. Per altre informazioni, vedere [Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-8.9].

Le attività successive non sono descritte nella documentazione di installazione generale di SAP.

> [!NOTE]
> La documentazione di installazione di SAP descrive come installare il primo nodo del cluster ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificare il profilo SAP dell'istanza di ASCS/SCS

Aggiungere prima di tutto un nuovo parametro del profilo. Questo parametro impedisce la chiusura delle connessioni tra i processi di lavoro SAP e il server di accodamento quando sono inattivi per un tempo eccessivo. Lo scenario del problema è stato descritto nella sezione [Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster per l'istanza ASCS/SCS di SAP][sap-ha-guide-8.11]. In quella sezione sono anche illustrate due modifiche ad alcuni parametri della connessione TCP/IP di base. Nel secondo passaggio è necessario impostare il server di accodamento per l'invio di un segnale `keep_alive` per impedire alle connessioni di raggiungere la soglia di inattività del servizio di bilanciamento del carico interno di Azure.

Per modificare il profilo SAP dell'istanza di ASCS/SCS:

1.  Aggiungere questo parametro al profilo dell'istanza di SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  Nell'esempio il percorso è:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Ad esempio, al profilo dell'istanza di SAP SCS e al percorso corrispondente:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Per applicare le modifiche, riavviare l'istanza ASCS/SCS di SAP.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Aggiungere una porta probe

Usare la funzionalità probe del servizio di bilanciamento del carico interno per il corretto funzionamento della configurazione del cluster con Azure Load Balancer. Il servizio di bilanciamento del carico interno di Azure distribuisce in genere il carico di lavoro in ingresso in modo uniforme tra le macchine virtuali.

 Questa operazione non funziona tuttavia in alcune configurazioni di cluster perché è attiva una sola istanza. L'altra istanza è passiva e non può accettare carico di lavoro. La funzionalità probe è utile quando il servizio di bilanciamento del carico interno di Azure assegna il carico di lavoro solo a un'istanza attiva. Con la funzionalità probe, il servizio di bilanciamento del carico interno può rilevare l'istanza attiva e usare solo questa istanza come destinazione del carico di lavoro.

Per aggiungere una porta probe:

1.  Verificare il valore corrente di **ProbePort** eseguendo il comando di PowerShell seguente:

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   Eseguire il comando all'interno di una delle macchine virtuali nella configurazione del cluster.

2.  Definire una porta probe. Il numero predefinito della porta probe è 0. In questo esempio viene usata la porta probe 62000.

  ![Figura 3: La porta probe della configurazione del cluster è 0 per impostazione predefinita][sap-ha-guide-figure-3048]

  _**Figura 3:** La porta probe predefinita della configurazione del cluster è 0_

  Il numero della porta è definito nei modelli di Azure Resource Manager per SAP. È possibile assegnare il numero della porta in PowerShell.

  Per impostare un nuovo valore di ProbePort per la risorsa cluster IP \<SID\> di SAP, eseguire lo script di PowerShell seguente per aggiornare le variabili di PowerShell per l'ambiente:

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  Dopo avere portato online il ruolo cluster \<SID\> di SAP, verificare che **ProbePort** sia impostata sul nuovo valore.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  Dopo l'esecuzione dello script, viene richiesto di riavviare il gruppo di cluster SAP per applicare le modifiche.

  ![Figura 4: Eseguire il probe della porta del cluster dopo aver impostato il nuovo valore][sap-ha-guide-figure-3049]

  _**Figura 4:** Eseguire il probe della porta del cluster dopo aver impostato il nuovo valore_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Aprire la porta probe di Windows Firewall

Aprire una porta probe di Windows Firewall in entrambi i nodi del cluster. Usare lo script seguente per aprire una porta probe di Windows Firewall. Aggiornare le variabili PowerShell per l'ambiente.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** è impostata su **62000**. Ora è possibile accedere alla condivisione file \\\ascsha-clsap\sapmnt da altri host, ad esempio da ascsha-dbas.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installare l'istanza di database

Per installare l'istanza di database, seguire la procedura descritta nella documentazione relativa all'installazione di SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installare il secondo nodo del cluster

Per installare il secondo cluster, seguire la procedura descritta nella guida all'installazione di SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Cambiare il tipo di avvio dell'istanza del servizio Windows SAP ERS

Cambiare il tipo di avvio del servizio Windows SAP ERS su **(avvio ritardato) automatico** in entrambi i nodi del cluster.

![Figura 5: Cambiare il tipo di servizio per l'istanza SAP ERS in ritardo automatico][sap-ha-guide-figure-3050]

_**Figura 5:** Cambiare il tipo di servizio per l'istanza SAP ERS in ritardo automatico_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installare Primary Application Server (PAS) SAP

Installare l'istanza di Primary Application Server (PAS) \<SID\>-di-0 nella macchina virtuale designata per ospitare PAS. Non sono presenti dipendenze in Azure. Non sono presenti impostazioni specifiche di DataKeeper.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installare Additional Application Server (AAS) SAP

Installare un'istanza di Additional Application Server (AAS) SAP in tutte le macchine virtuali designate per ospitare un'istanza del server applicazioni SAP. Ad esempio, in \<SID\>-di-1 per \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Questa operazione completa l'installazione di un sistema SAP NetWeaver a disponibilità elevata. Procedere quindi con il test del failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testare il failover e la replica SIOS dell'istanza di SAP ASCS/SCS
È possibile testare e monitorare con facilità un failover dell'istanza di SAP ASCS/SCS e una replica del disco SIOS usando Gestione cluster di failover e lo strumento di configurazione e gestione di SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Istanza di SAP ASCS/SCS in esecuzione nel nodo A del cluster

Il gruppo di cluster SAP PR1 è in esecuzione nel nodo A del cluster, ad esempio in pr1-ascs-0. Assegnare al nodo A del cluster l'unità disco condivisa S, appartenente al gruppo di cluster SAP PR1 e usata anche dall'istanza di ASCS/SCS. 

![Figura 6: Gestione cluster di failover: il gruppo di cluster \<SID\> di SAP è in esecuzione nel nodo A del cluster][sap-ha-guide-figure-5000]

_**Figura 6:** Gestione cluster di failover: il gruppo di cluster \<SID\> SAP è in esecuzione nel nodo A del cluster_

Nello strumento di configurazione e gestione di SIOS DataKeeper è possibile verificare che i dati di dischi condivisi vengano replicati in modo sincrono dall'unità S del volume di origine nel nodo A del cluster all'unità S del volume di destinazione nel nodo B del cluster, ad esempio da pr1-ascs-0 [10.0.0.40] a pr1-ascs-1 [10.0.0.41].

![Figura 7: In SIOS DataKeeper replicare il volume locale dal nodo A al nodo B del cluster][sap-ha-guide-figure-5001]

_**Figura 7:** In SIOS DataKeeper replicare il volume locale dal nodo A al nodo B del cluster_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover dal nodo A al nodo B

1.  Scegliere una di queste opzioni per avviare un failover del gruppo di cluster \<SID\> di SAP dal nodo A al nodo B del cluster:
  - Gestione cluster di failover  
  - PowerShell per Clustering di failover

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Riavviare il nodo A del cluster all'interno del sistema operativo guest Windows. Verrà avviato un failover automatico del gruppo di cluster \<SID\> di SAP dal nodo A al nodo B.  
3.  Riavviare il nodo A del cluster dal portale di Azure. Verrà avviato un failover automatico del gruppo di cluster \<SID\> di SAP dal nodo A al nodo B.  
4.  Riavviare il nodo A del cluster usando Azure PowerShell. Verrà avviato un failover automatico del gruppo di cluster \<SID\> di SAP dal nodo A al nodo B.

  Dopo il failover, il gruppo di cluster \<SID\> di SAP è in esecuzione nel nodo B del cluster, ad esempio in pr1-ascs-1.

  ![Figura 8: In Gestione cluster di failover il gruppo di cluster \<SID\> di SAP è in esecuzione nel nodo B del cluster][sap-ha-guide-figure-5002]

  _**Figura 8:** In Gestione cluster di failover il gruppo di cluster \<SID\> di SAP è in esecuzione nel nodo B del cluster_

  Il disco condiviso è ora montato nel nodo B del cluster. SIOS DataKeeper replica i dati dall'unità S del volume di origine nel nodo B del cluster all'unità S del volume di destinazione nel nodo A del cluster, ad esempio da pr1-ascs-1 [10.0.0.41] a pr1-ascs-0 [10.0.0.40].

  ![Figura 9: SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster][sap-ha-guide-figure-5003]

  _**Figura 9:** SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster_
