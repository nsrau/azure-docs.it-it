---
title: "Disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver | Documentazione Microsoft"
description: "Guida alle funzionalità di disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d00db895ffcf9ba9a51e3df2dae5d33c0277dd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver

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

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


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



Macchine virtuali di Azure è la soluzione ideale per le organizzazioni che necessitano di risorse di calcolo, di archiviazione e di rete in tempi minimi e con cicli di approvvigionamento brevi. È possibile usare Macchine virtuali di Azure per distribuire applicazioni classiche, ad esempio ABAP basato su SAP NetWeaver, Java e uno stack ABAP+Java. È possibile estendere l'affidabilità e la disponibilità senza risorse locali aggiuntive. Macchine virtuali di Azure supporta la connettività cross-premise e quindi è possibile integrare Macchine virtuali di Azure nei domini locali, nei cloud privati e nel panorama applicativo del sistema SAP dell'organizzazione.

Questo articolo illustra la procedura che è possibile eseguire per distribuire sistemi SAP a disponibilità elevata in Azure, usando il modello di distribuzione Azure Resource Manager. Le attività principali che verranno illustrate sono le seguenti:

* Trovare le guide all'installazione e le note di SAP appropriate elencate nella sezione [Risorse][sap-ha-guide-2]. Questo articolo integra la documentazione sull'installazione di SAP e le note su SAP, che sono le risorse principali per l'installazione e la distribuzione del software SAP in piattaforme specifiche.
* Informazioni sulle differenze tra il modello di distribuzione classica di Azure e il modello di distribuzione Azure Resource Manager.
* Informazioni sulle modalità quorum di Windows Server Failover Clustering per consentire la selezione del modello più adatto per la distribuzione di Azure.
* Comprendere l'archiviazione condivisa di Windows Server Failover Clustering nei servizi di Azure.
* Leggere le informazioni su come proteggere in Azure i componenti con un singolo punto di guasto, ad esempio Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) e i sistemi di gestione di database (DBMS), oltre ai componenti ridondanti, ad esempio i server applicazioni SAP.
* Seguire un esempio dettagliato di installazione e configurazione di un sistema SAP a disponibilità elevata in un cluster Windows Server Failover Clustering in Azure usando Azure Resource Manager.
* Apprendere gli altri passaggi obbligatori per usare Windows Server Failover Clustering in Azure, ma non necessari in una distribuzione locale.

Per semplificare la distribuzione e la configurazione, in questo articolo verranno usati modelli di Resource Manager a disponibilità elevata a tre livelli per SAP. Il modelli automatizzano la distribuzione dell'intera infrastruttura necessaria per un sistema SAP a disponibilità elevata. L'infrastruttura supporta anche il ridimensionamento SAP Application Performance Standard (SAPS) del sistema SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Prerequisiti
Prima di iniziare, verificare che siano soddisfatti i prerequisiti descritti nei capitoli seguenti. Assicurarsi anche di controllare tutte le risorse elencate nella sezione [Risorse][sap-ha-guide-2].

In questo articolo vengono usati i modelli di Azure Resource Manager per [SAP NetWeaver a tre livelli con Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Per una panoramica dei modelli, vedere i [modelli di Azure Resource Manager per SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Risorse
Questi articoli descrivono le distribuzioni SAP in Azure:

* [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP NetWeaver][dbms-guide]
* [Disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver (questa guida)][sap-ha-guide]

> [!NOTE]
> Quando è possibile, viene fornito un collegamento alla guida di riferimento per l'installazione di SAP. Vedere le [guide all'installazione di SAP][sap-installation-guides]. Per i prerequisiti e per informazioni sul processo di installazione, è consigliabile leggere attentamente le guide all'installazione di SAP NetWeaver. Questo articolo illustra solo attività specifiche per i sistemi basati su SAP NetWeaver che è possibile usare con Macchine virtuali di Azure.
>
>

Queste note su SAP sono correlate all'argomento relativo a SAP in Azure:

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: dimensioni e prodotti supportati |
| [2015553] |SAP in Microsoft Azure: prerequisiti per il supporto |
| [1999351] |Monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1999351] |Virtualizzazione in Windows: monitoraggio avanzato |
| [2243692] |Use of Azure Premium SSD Storage for SAP DBMS Instance (Uso dell'archiviazione unità SSD di Azure Premium per l'istanza DBMS di SAP) |

Sono disponibili altre informazioni sulle [limitazioni delle sottoscrizioni di Azure][azure-subscription-service-limits-subscription], incluse le limitazioni massime e quelle predefinite generali.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP a disponibilità elevata con il modello di distribuzione classica di Azure e il modello Azure Resource Manager
I modelli di distribuzione di Azure Resource Manager e di distribuzione classica presentano le differenze seguenti:

- Gruppi di risorse
- Dipendenza del servizio di bilanciamento del carico interno di Azure nel gruppo di risorse di Azure
- Supporto per scenari di SAP con più SID

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Gruppi di risorse
In Azure Resource Manager è possibile usare i gruppi di risorse per gestire tutte le risorse dell'applicazione nella sottoscrizione di Azure. In un approccio integrato tutte le risorse di un gruppo di risorse hanno lo stesso ciclo di vita. Ad esempio, tutte le risorse vengono create ed eliminate contemporaneamente. Altre informazioni sui [gruppi di risorse](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Dipendenza del servizio di bilanciamento del carico interno di Azure nel gruppo di risorse di Azure

Nel modello di distribuzione classica di Azure esiste una dipendenza tra il servizio di bilanciamento del carico interno di Azure (servizio Azure Load Balancer) e il servizio cloud. Ogni servizio di bilanciamento del carico interno necessita di un servizio cloud.

In Azure Resource Manager ogni risorsa di Azure deve essere inserita in un gruppo di risorse di Azure, come anche Azure Load Balancer, ma non è necessario avere un gruppo di risorse di Azure per ogni istanza di Azure Load Balancer. Un gruppo di risorse di Azure, ad esempio, può contenere più istanze di Azure Load Balancer. L'ambiente è più semplice e flessibile. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Supporto per scenari di SAP con più SID

In Azure Resource Manager è possibile installare istanze di ASCS/SCS con più SAP ID (SID). Le istanze con più SID sono consentite grazie al supporto di più indirizzi IP per ogni servizio di bilanciamento del carico interno di Azure.

Per usare il modello di distribuzione classica di Azure, seguire le procedure descritte in [SAP NetWeaver in Azure: Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver in Azure: clustering di istanze di SAP ASCS/SCS tramite Windows Server Failover Clustering in Azure con SIOS DataKeeper).

> [!IMPORTANT]
> Per le installazioni SAP, è consigliabile usare il modello di distribuzione Azure Resource Manager perché offre molti vantaggi non disponibili nel modello di distribuzione classica. Sono disponibili altre informazioni sui [modelli di distribuzione][virtual-machines-azure-resource-manager-architecture-benefits-arm] di Azure.   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
Windows Server Failover Clustering è alla base di un'installazione SAP ASCS/SCS e di DBMS.

Un cluster di failover è un gruppo di 1 + n server (nodi) indipendenti che funzionano insieme per aumentare la disponibilità di applicazioni e servizi. Se si verifica un errore in un nodo, Windows Server Failover Clustering calcola il numero di errori che possono verificarsi e mantiene un cluster integro per fornire le applicazioni e i servizi. A questo scopo è possibile scegliere tra diverse modalità quorum per ottenere il clustering di failover.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modalità quorum
È possibile scegliere tra quattro modalità quorum quando si usa Windows Server Failover Clustering:

* **Maggioranza dei nodi**. Ogni nodo del cluster può votare. Il cluster funziona solo con la maggioranza dei voti, vale a dire con più della metà dei voti. È consigliabile scegliere questa opzione per i cluster con un numero dispari di nodi. Ad esempio, anche se si verificano errori in tre nodi di un cluster con sette nodi, il cluster ottiene comunque la maggioranza e continua l'esecuzione.  
* **Maggioranza dei nodi e dei dischi**. Ogni nodo e un disco designato (un disco di controllo) nello spazio di archiviazione del cluster possono votare quando sono disponibili e in comunicazione. Il cluster funziona solo con la maggioranza dei voti, vale a dire con più della metà dei voti. Questa modalità ha senso in un ambiente cluster con un numero pari di nodi. Se metà dei nodi e il disco sono online, il cluster rimane in uno stato di integrità.
* **Maggioranza dei nodi e delle condivisioni file**. Ogni nodo e una condivisione file designata (controllo di condivisione file) creata dall'amministratore possono votare, indipendentemente dal fatto che i nodi e la condivisione file siano disponibili e in comunicazione. Il cluster funziona solo con la maggioranza dei voti, vale a dire con più della metà dei voti. Questa modalità ha senso in un ambiente cluster con un numero pari di nodi. È simile alla modalità Maggioranza dei nodi e dei dischi, ma usa una condivisione file di controllo invece di un disco di controllo. Questa modalità è facile da implementare, ma la condivisione file potrebbe diventare un singolo punto di guasto, se non è a disponibilità elevata.
* **Non di maggioranza - solo disco**. Il cluster ha un quorum se un nodo è disponibile e in comunicazione con un disco specifico nello spazio di archiviazione del cluster. Solo i nodi che sono anche in comunicazione con questo disco possono aggiungersi al cluster. Si consiglia di non usare questa modalità.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server Failover Clustering locale
La figura 1 illustra un cluster con due nodi. Se la connessione di rete tra i nodi viene interrotta ed entrambi i nodi rimangono operativi, un disco quorum o una condivisione file determina quale nodo continuerà a fornire le applicazioni e i servizi del cluster. Il nodo che ha accesso alla condivisione file o al disco quorum è il nodo che garantisce la continuità dei servizi.

Poiché questo esempio usa un cluster con due nodi, viene usata la modalità quorum Maggioranza dei nodi e delle condivisioni file. Anche l'opzione Maggioranza dei nodi e dei dischi è valida. In un ambiente di produzione è consigliabile usare un disco quorum. Per fare in modo che sia a disponibilità elevata, è possibile usare una tecnologia basata su un sistema di archiviazione e sulla rete.

![Figura 1: Esempio di configurazione di Windows Server Failover Clustering proposta per SAP ASCS/SCS in Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Esempio di configurazione di Windows Server Failover Clustering proposta per SAP ASCS/SCS in Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Spazio di archiviazione condiviso
La figura 1 illustra anche un cluster di archiviazione condiviso a due nodi. In un cluster di archiviazione condiviso locale tutti i nodi del cluster rilevano lo spazio di archiviazione condiviso. Un meccanismo di blocco protegge i dati da danneggiamenti. Tutti i nodi possono rilevare se si verifica un errore in un altro nodo. Se si verifica un errore in un nodo, il nodo rimanente assume la proprietà delle risorse di archiviazione e assicura la disponibilità dei servizi.

> [!NOTE]
> Non sono necessari dischi condivisi per la disponibilità elevata con alcune applicazioni DBMS, ad esempio SQL Server. La funzionalità AlwaysOn di SQL Server replica i file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster nel disco locale di un altro nodo del cluster. In tal caso, la configurazione del cluster Windows non richiede un disco condiviso.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Rete e risoluzione dei nomi
I computer client raggiungono il cluster tramite un indirizzo IP virtuale e un nome host virtuale forniti dal server DNS. I nodi locali e il server DNS possono gestire più indirizzi IP.

In un'installazione tipica si usano due o più connessioni di rete:

* Una connessione dedicata all'archiviazione
* Una connessione di rete interna al cluster per l'heartbeat
* Una rete pubblica usata dai client per la connessione al cluster

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering in Azure
Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un Windows Server Failover Clustering in Macchine virtuali di Azure. Quando si crea un disco cluster condiviso, è necessario impostare diversi indirizzi IP e nomi host virtuali per l'istanza di SAP ASCS/SCS.

Questo articolo illustra i concetti chiave e i passaggi aggiuntivi necessari per creare un cluster SAP Central Services a disponibilità elevata in Azure. Viene illustrato come configurare lo strumento SIOS DataKeeper di terze parti e come configurare il servizio di bilanciamento del carico interno di Azure. È possibile usare questi strumenti per creare un cluster di failover Windows con un controllo di condivisione file in Azure.

![Figura 2: Configurazione di Windows Server Failover Clustering in Azure senza disco condiviso][sap-ha-guide-figure-1001]

_**Figura 2:** Configurazione di Windows Server Failover Clustering in Azure senza disco condiviso_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco condiviso in Azure con SIOS DataKeeper
È necessario spazio di archiviazione condiviso del cluster per un'istanza di SAP ASCS/SCS a disponibilità elevata. A partire da settembre 2016, Azure non offre spazio di archiviazione condiviso da usare per creare un cluster di archiviazione condiviso. È possibile usare il software SIOS DataKeeper Cluster Edition di terze parti per creare una risorsa di archiviazione con mirroring che simula la risorsa di archiviazione condivisa del cluster. La soluzione SIOS fornisce la replica di dati sincrona in tempo reale. Per creare una risorsa disco condiviso per un cluster, seguire questa procedura:

1. Collegare un disco aggiuntivo a ogni macchina virtuale (VM) in una configurazione cluster Windows.
2. Eseguire SIOS DataKeeper Cluster Edition in entrambi i nodi delle macchine virtuali.
3. Configurare SIOS DataKeeper Cluster Edition per eseguire il mirroring del contenuto del volume collegato del disco aggiuntivo dalla macchina virtuale di origine al volume collegato del disco aggiuntivo della macchina virtuale di destinazione. SIOS DataKeeper astrae i volumi locali di origine e di destinazione e quindi li presenta a Windows Server Failover Clustering come un disco condiviso.

Altre informazioni su [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Figura 3: Configurazione di Windows Server Failover Clustering in Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Configurazione di Windows Server Failover Clustering in Azure con SIOS DataKeeper_

> [!NOTE]
> Non sono necessari dischi condivisi per la disponibilità elevata con alcuni prodotti DBMS, ad esempio SQL Server. La funzionalità AlwaysOn di SQL Server replica i file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster nel disco locale di un altro nodo del cluster. In questo caso, la configurazione del cluster Windows non richiede un disco condiviso.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Risoluzione dei nomi in Azure
La piattaforma cloud di Azure non consente di configurare indirizzi IP virtuali, ad esempio indirizzi IP mobili. È necessaria una soluzione alternativa per configurare un indirizzo IP virtuale per poter raggiungere la risorsa cluster nel cloud.
Azure include un servizio di bilanciamento del carico interno nel servizio Azure Load Balancer. Con il servizio di bilanciamento del carico interno, i client raggiungono il cluster tramite l'indirizzo IP virtuale del cluster.
È necessario distribuire il servizio di bilanciamento del carico interni nel gruppo di risorse che contiene i nodi del cluster. Configurare quindi tutte le necessarie regole di port forwarding con le porte probe del servizio di bilanciamento del carico interno.
I client possono connettersi tramite il nome host virtuale. Il server DNS risolve l'indirizzo IP del cluster e il servizio di bilanciamento del carico interno gestisce il port forwarding al nodo attivo del cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Disponibilità elevata di SAP NetWeaver nell'infrastruttura distribuita come servizio (IaaS) di Azure
Per ottenere la disponibilità elevata per le applicazioni SAP, ad esempio per i componenti software SAP, è necessario proteggere i componenti seguenti:

* Istanza del server applicazioni SAP
* Istanza di SAP ASCS/SCS
* Server DBMS

Per altre informazioni sulla protezione dei componenti SAP in scenari a disponibilità elevata, vedere [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Server applicazioni SAP a disponibilità elevata
In genere non è necessaria una specifica soluzione a disponibilità elevata per le istanze e del server applicazioni SAP e di dialogo. La disponibilità elevata si ottiene tramite la ridondanza e si dovranno configurare più istanze delle finestre di dialogo in istanze diverse di Macchine virtuali di Azure. È necessario avere almeno due istanze dell'applicazione SAP installate in due istanze di Macchine virtuali di Azure.

![Figura 4: Server applicazioni SAP a disponibilità elevata][sap-ha-guide-figure-2000]

_**Figura 4:** Server applicazioni SAP a disponibilità elevata_

È necessario inserire tutte le macchine virtuali che ospitano le istanze del server applicazioni SAP nello stesso set di disponibilità di Azure. Un set di disponibilità di Azure assicura che:

* Tutte le macchine virtuali facciano parte dello stesso dominio di aggiornamento. Un dominio di aggiornamento, ad esempio, verifica che le macchine virtuali non vengano aggiornate contemporaneamente durante i tempi di inattività per la manutenzione pianificati.
* Tutte le macchine virtuali facciano parte dello stesso dominio di errore. Un dominio di errore, ad esempio, verifica che le macchine virtuali vengano distribuite in modo che nessun singolo punto di guasto influisca sulla disponibilità di tutte le macchine virtuali.

Altre informazioni su come [gestire la disponibilità delle macchine virtuali][virtual-machines-manage-availability].

Solo dischi non gestiti: poiché l'account di archiviazione di Azure è un potenziale singolo punto di guasto, è importante avere almeno due account di archiviazione di Azure, in cui verranno distribuite almeno due macchine virtuali. In una configurazione ideale, il disco di ogni macchina virtuale che esegue l'istanza di una finestra di dialogo SAP verrà distribuito in un account di archiviazione diverso.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Istanza di SAP ASCS/SCS a disponibilità elevata
La figura 5 è un esempio di istanza di SAP ASCS/SCS a disponibilità elevata.

![Figura 5: Istanza di SAP ASCS/SCS a disponibilità elevata][sap-ha-guide-figure-2001]

_**Figura 5:** Istanza di SAP ASCS/SCS a disponibilità elevata_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Disponibilità elevata dell'istanza di SAP ASCS/SCS con Windows Server Failover Clustering in Azure
Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un Windows Server Failover Clustering in Macchine virtuali di Azure. Per creare un cluster di failover Windows, sono necessari un disco cluster condiviso, più indirizzi IP, più nomi host virtuali e un servizio di bilanciamento del carico interno di Azure per il clustering di un'istanza di SAP ASCS/SCS. Questo argomento viene illustrato più dettagliatamente più avanti nell'articolo.

![Figura 6: Windows Server Failover Clustering per una configurazione SAP ASCS/SCS in Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Windows Server Failover Clustering per una configurazione SAP ASCS/SCS in Azure con SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Istanza di DBMS a disponibilità elevata
Anche DBMS è un singolo punto di contatto in un sistema SAP. È necessario proteggerlo usando una soluzione a disponibilità elevata. La figura 7 illustra una soluzione a disponibilità elevata SQL Server AlwaysOn in Azure con Windows Server Failover Clustering e il bilanciamento del carico interno di Azure. SQL Server AlwaysOn replica i file di dati e di log DBMS usando la replica DBMS. In questo caso, non sono necessari dischi condivisi di cluster, semplificando così l'intera configurazione.

![Figura 7: Esempio di SAP DBMS a disponibilità elevata con SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 7:** Esempio di SAP DBMS a disponibilità elevata con SQL Server AlwaysOn_

Per altre informazioni sul clustering di SQL Server in Azure con il modello di distribuzione Azure Resource Manager, vedere questi articoli:

* [Configurare manualmente il gruppo di disponibilità AlwaysOn in Macchine virtuali di Azure con Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configurare un servizio di bilanciamento del carico interno di Azure per un gruppo di disponibilità AlwaysOn in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scenari di distribuzione a disponibilità elevata end-to-end

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenario di distribuzione usando il modello architetturale 1

La figura 8 illustra un esempio di architettura a disponibilità elevata SAP NetWeaver in Azure per **un** sistema SAP. Questo scenario viene configurato come segue:

- Un cluster dedicato viene usato per l'istanza di SAP ASCS/SCS.
- Un cluster dedicato viene usato per l'istanza di DBMS.
- Le istanze del server applicazioni SAP vengono distribuite in proprie VM dedicate.

![Figura 8: Modello architetturale 1 a disponibilità elevata di SAP, con cluster dedicato per ASCS/SCS e DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modello architetturale 1 a disponibilità elevata di SAP, cluster dedicati per ASCS/SCS e DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenario di distribuzione usando il modello architetturale 2

La figura 9 illustra un esempio di architettura a disponibilità elevata di SAP NetWeaver in Azure per **un** sistema SAP. Questo scenario viene configurato come segue:

- Un cluster dedicato viene usato **sia** per l'istanza di SAP ASCS/SCS che per l'istanza di DBMS.
- Le istanze del server applicazioni SAP vengono distribuite in proprie VM dedicate.

![Figura 9: Modello architetturale 2 a disponibilità elevata di SAP, con un cluster dedicato per ASCS/SCS e un cluster dedicato per DBMS][sap-ha-guide-figure-2005]

_**Figura 9:** Modello architetturale 2 a disponibilità elevata di SAP, con un cluster dedicato per ASCS/SCS e un cluster dedicato per DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenario di distribuzione usando il modello architetturale 3

La figura 10 illustra un esempio di architettura a disponibilità elevata di SAP NetWeaver in Azure per **due** sistemi SAP, con &lt;SID1&gt; e &lt;SID2&gt;. Questo scenario viene configurato come segue:

- Un cluster dedicato viene usato **sia** per l'istanza di SAP ASCS/SCS SID1 *che* per l'istanza di SAP ASCS/SCS SID2 (un cluster).
- Un cluster dedicato viene usato per DBMS SID1 e un altro cluster dedicato viene usato per DBMS SID2 (due cluster).
- Le istanze del server applicazioni SAP per il SID1 del sistema SAP hanno proprie VM dedicate.
- Le istanze del server applicazioni SAP per il SID2 del sistema SAP hanno proprie VM dedicate.

![Figura 10: Modello architetturale 3 a disponibilità elevata per SAP, con un cluster dedicato per istanze diverse di ASCS/SCS][sap-ha-guide-figure-6003]

_**Figura 10:** Modello architetturale 3 a disponibilità elevata per SAP, con un cluster dedicato per istanze diverse di ASCS/SCS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparare l'infrastruttura

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparare l'infrastruttura per il modello architetturale 1
I modelli di Azure Resource Manager per SAP consentono di semplificare la distribuzione delle risorse necessarie.

I modelli a tre livelli in Azure Resource Manager supportano anche scenari a disponibilità elevata, ad esempio il modello architetturale 1 con due cluster. Ogni cluster è un singolo punto di guasto SAP per SAP ASCS/SCS e DBMS.

Ecco dove è possibile ottenere i modelli di Azure Resource Manager per questo scenario di esempio descritto in questo articolo:

* [Immagine di Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Immagine di Azure Marketplace che usa Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Immagine personalizzata che usa Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Per preparare l'infrastruttura per il modello architetturale 1:

- Nella casella **SYSTEMAVAILABILITY** nel pannello **Parametri** del portale di Azure selezionare **HA**.

  ![Figura 11: Impostare i parametri di Azure Resource Manager di disponibilità elevata per SAP][sap-ha-guide-figure-3000]

_**Figura 11:** Impostare i parametri di Azure Resource Manager di disponibilità elevata per SAP_


  I modelli creano:

  * **Macchine virtuali**:
    * Macchine virtuali del server applicazioni SAP: <*SIDSistemaSAP*>-di-<*Numero*>
    * Macchine virtuali del cluster ASCS/SCS: <*SIDSistemaSAP*>-ascs-<*Numero*>
    * Cluster DBMS: <*SIDSistemaSAP*>-db-<*Numero*>

  * **Schede di rete per tutte le macchine virtuali, con gli indirizzi IP associati**:
    * <*SIDSistemaSAP*&gt;-nic-di-&lt;*Numero*>
    * <*SIDSistemaSAP*>-nic-ascs-<*Numero*>
    * <*SIDSistemaSAP*>-nic-db-<*Numero*>

  * **Account di archiviazione di Azure (solo dischi non gestiti)**

  * **Gruppi di disponibilità** per:
    * Macchine virtuali del server applicazioni SAP: <*SIDSistemaSAP*>-avset-di
    * Macchine virtuali del cluster SAP ASCS/SCS: <*SIDSistemaSAP*>-avset-ascs
    * Macchine virtuali del cluster DBMS: <*SIDSistemaSAP*>-avset-db

  * **Servizio di bilanciamento del carico interno di Azure**:
    * Con tutte le porte per l'istanza di ASCS/SCS e l'indirizzo IP <*SIDSistemaSAP*>-lb-ascs
    * Con tutte le porte per l'istanza di SQL Server DBMS e l'indirizzo IP <*SIDSistemaSAP*>-lb-db

  * **Gruppo di sicurezza di rete**: <*SIDSistemaSAP*>-nsg-ascs-0  
    * Con una porta Remote Desktop Protocol (RDP) esterna aperta alla macchina virtuale <*SIDSistemaSAP*>-ascs-0

> [!NOTE]
> Tutti gli indirizzi IP delle schede di rete e i servizi di bilanciamento del carico interno di Azure sono **dinamici** per impostazione predefinita. Impostarli come indirizzi IP **statici**, Questo argomento verrà illustrato più avanti nell'articolo.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuire macchine virtuali con connettività di rete aziendale (cross-premise) da usare in fase di produzione
Per i sistemi SAP di produzione, distribuire le macchine virtuali di Azure con la [connettività di rete aziendale (cross-premise)][planning-guide-2.2] usando la rete VPN da sito a sito di Azure o Azure ExpressRoute.

> [!NOTE]
> È possibile usare l'istanza di Rete virtuale di Azure. La rete virtuale e la subnet sono già state create e preparate.
>
>

1.  Nella casella **NEWOREXISTINGSUBNET** nel pannello **Parametri** del portale di Azure selezionare **existing**.
2.  Nella casella**SUBNETID** aggiungere la stringa completa del campo SubnetID relativo alla rete di Azure preparata, in cui si prevede di distribuire le macchine virtuali di Azure.
3.  Per ottenere un elenco di tutte le subnet di rete di Azure, eseguire questo comando di PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  Il campo **ID** contiene il valore di **SUBNETID**.
4. Per ottenere un elenco di tutti i valori **SUBNETID**, eseguire questo comando PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Il valore di **SUBNETID** è il seguente:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuire delle istanze di SAP solo cloud a scopo di test e dimostrazione
È possibile distribuire il sistema SAP a disponibilità elevata in un modello di distribuzione solo cloud. Questo tipo di distribuzione è utile soprattutto per i casi d'uso di demo e test. Non è adatto per i casi d'uso in un ambiente di produzione.

- Nella casella **NEWOREXISTINGSUBNET** nel pannello **Parametri** del portale di Azure selezionare **new**. Lasciare vuoto il campo **SUBNETID**.

  Il modello di Azure Resource Manager per SAP crea automaticamente la rete virtuale di Azure e la subnet.

> [!NOTE]
> È anche necessario distribuire almeno una macchina virtuale dedicata per Active Directory e DNS nella stessa istanza di Rete virtuale di Azure. Il modello non crea queste macchine virtuali.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparare l'infrastruttura per il modello architetturale 2

È possibile usare questo modello di Azure Resource Manager in modo che SAP semplifichi la distribuzione delle risorse di infrastruttura necessarie per il modello di SAP 2.

Ecco dove è possibile ottenere i modelli di Azure Resource Manager per questo scenario di distribuzione:

* [Immagine di Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Immagine di Azure Marketplace che usa Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Immagine personalizzata che usa Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparare l'infrastruttura per il modello architetturale 3

È possibile preparare l'infrastruttura e configurare SAP per **più SID**. Ad esempio, è possibile aggiungere un'istanza di SAP ASCS/SCS aggiuntiva in una configurazione di cluster *esistente*. Per altre informazioni, vedere [Configurare un'istanza di SAP ASCS/SCS aggiuntiva in una configurazione di cluster esistente per creare una configurazione di SAP a più SID in Azure Resource Manager][sap-ha-multi-sid-guide].

Per creare un nuovo cluster con più SID, è possibile usare i [modelli di avvio rapido su GitHub](https://github.com/Azure/azure-quickstart-templates) relativi a più SID.
Per creare un nuovo cluster a più SID, è necessario distribuire i tre modelli seguenti:

* [Modello di ASCS/SCS](#ASCS-SCS-template)
* [Modello di database](#database-template)
* [Modello dei server applicazioni](#application-servers-template)

Le sezioni seguenti includono informazioni dettagliate sui modelli e parametri che è necessario fornire nei modelli.

#### <a name="ASCS-SCS-template"></a> Modello di ASCS/SCS

Il modello di ASCS/SCS distribuisce due macchine virtuali che possono essere usate per creare un cluster di failover di Windows che ospita più istanze di ASCS/SCS.

Per configurare il modello a più SID di ASCS/SCS nel [modello a più SID di ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] o il [modello a più SID di ASCS/SCS con Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md], immettere i valori per i parametri seguenti:

  - **Resource Prefix**.  Impostare il prefisso della risorsa, che viene usato per assegnare un prefisso a tutte le risorse create durante la distribuzione. Poiché le risorse non appartengono a un solo sistema SAP, il prefisso della risorsa non è il SID di un sistema SAP.  Il prefisso deve essere compreso fra **tre e sei caratteri**.
  - **Stack Type**. Selezionare il tipo di stack del sistema SAP. A seconda del tipo di stack, Azure Load Balancer dispone di un indirizzo IP (solo ABAP o Java) o due indirizzi IP (ABAP+Java) privati per ogni sistema SAP.
  -  **OS Type**. Selezionare il sistema operativo delle macchine virtuali.
  -  **SAP System Count**. Selezionare il numero di sistemi SAP da installare in questo cluster.
  -  **System Availability**. Selezionare **HA**.
  -  **Admin Username and Admin Password**. Creare un nuovo utente che può essere usato per accedere alla macchina.
  -  **New Or Existing Subnet**. Impostare se devono essere create una nuova rete virtuale e una nuova subnet o se deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare **existing**.
  -  **Subnet Id**. Impostare l'ID della subnet a cui devono essere connesse le macchine virtuali. Selezionare la subnet della rete virtuale ExpressRoute o VPN per connettere la macchina virtuale alla rete locale. L'ID in genere è simile al seguente:

   /subscriptions/<*ID sottoscrizione*>/resourceGroups/<*nomegruppo risorse*>/providers/Microsoft.Network/VirtualNetwork/<*nome rete virtuale*>>/subnets/<*nome subnet*>

Il modello consente di distribuire un'istanza di Azure Load Balancer che supporta più sistemi SAP.

- Le istanze ASCS sono configurate per i numeri di istanza 00, 10, 20...
- Le istanze SCS sono configurate per i numeri di istanza 01, 11, 21...
- Le istanze di ASCS Enqueue Replication Server (ERS) (Linux) sono configurate per i numeri di istanza 02, 12, 22...
- Le istanze SCS ERS (solo Linux) sono configurate per i numeri di istanza 03, 13, 23...

Il bilanciamento del carico contiene 1 (2 per Linux) indirizzo VIP, 1 indirizzo VIP per ASCS/SCS e 1 indirizzo VIP per ERS (solo Linux).

L'elenco seguente contiene tutte le regole di bilanciamento del carico (dove x è il numero del sistema SAP, ad esempio, 1, 2, 3...):
- Porte specifiche di Windows per ogni sistema SAP 445, 5985
- Porte ASCS (numero di istanza x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porte SCS (numero di istanza x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porte ASCS ERS in Linux (numero di istanza x2): 33x2, 5x213, 5x214, 5x216
- Porte SCS ERS in Linux (numero di istanza x3): 33x3, 5x313, 5x314, 5x316

Il servizio di bilanciamento del carico viene configurato per l'uso delle porte probe seguenti (dove x è il numero del sistema SAP, ad esempio 1, 2, 3...):
- Porta probe del servizio di bilanciamento del carico interno ASCS/SCS: 620x0
- Porta probe del servizio di bilanciamento del carico interno ERS (solo Linux): 621x2

#### <a name="database-template"></a> Modello di database

Il modello di database distribuisce una o due macchine virtuali che è possibile usare per installare il sistema di gestione di database relazionali (RDBMS) per un sistema SAP. Se, ad esempio, si distribuisce un modello ASCS/SCS per 5 sistemi SAP, è necessario distribuire questo modello cinque volte.

Per configurare il modello a più SID di database nel [modello a più SID di database][sap-templates-3-tier-multisid-db-marketplace-image] o il [modello a più SID di database con Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md], immettere i valori per i parametri seguenti:

  -  **Sap System Id**. Immettere l'ID del sistema SAP che si vuole installare. L'ID verrà usato come prefisso per le risorse distribuite.
  -  **Os Type**. Selezionare il sistema operativo delle macchine virtuali.
  -  **Dbtype**. Selezionare il tipo di database che si vuole installare nel cluster. Selezionare **SQL** se si vuole installare Microsoft SQL Server. Selezionare **HANA** se si prevede di installare SAP HANA nelle macchine virtuali. Assicurarsi di selezionare il tipo di sistema operativo corretto: selezionare **Windows** per SQL e selezionare una distribuzione Linux per HANA. L'istanza di Azure Load Balancer connessa alle macchine virtuali verrà configurata per supportare il tipo di database selezionato:
    * **SQL**. Il servizio di bilanciamento del carico caricherà la porta di bilanciamento 1433. Assicurarsi di usare questa porta per l'installazione di SQL Server Always On.
    * **HANA**. Il servizio di bilanciamento del carico caricherà le porta di bilanciamento 35015 e 35017. Assicurarsi di installare SAP HANA con il numero di istanza **50**.
    Il servizio bilanciamento del carico userà la porta probe 62550.
  -  **Sap System Size**. Impostare il numero di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
  -  **System Availability**. Selezionare **HA**.
  -  **Admin Username and Admin Password**. Creare un nuovo utente che può essere usato per accedere alla macchina.
  -  **Subnet Id**. Immettere l'ID della subnet usata durante la distribuzione del modello di ASCS/SCS o l'ID della subnet creata come parte della distribuzione del modello di ASCS/SCS.

#### <a name="application-servers-template"></a> Modello dei server applicazioni

Il modello dei server applicazioni consente di distribuire due o più macchine virtuali che possono essere usate come istanze del server applicazioni SAP per un sistema SAP. Se, ad esempio, si distribuisce un modello ASCS/SCS per 5 sistemi SAP, è necessario distribuire questo modello cinque volte.

Per configurare il modello a più SID dei server applicazioni nel [modello a più SID dei server applicazioni][sap-templates-3-tier-multisid-apps-marketplace-image] o il [modello a più SID dei server applicazioni con Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], immettere i valori per i parametri seguenti:

  -  **Sap System Id**. Immettere l'ID del sistema SAP che si vuole installare. L'ID verrà usato come prefisso per le risorse distribuite.
  -  **Os Type**. Selezionare il sistema operativo delle macchine virtuali.
  -  **Sap System Size**. Numero di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
  -  **System Availability**. Selezionare **HA**.
  -  **Admin Username and Admin Password**. Creare un nuovo utente che può essere usato per accedere alla macchina.
  -  **Subnet Id**. Immettere l'ID della subnet usata durante la distribuzione del modello di ASCS/SCS o l'ID della subnet creata come parte della distribuzione del modello di ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rete virtuale di Azure
In questo esempio lo spazio degli indirizzi della rete virtuale di Azure è 10.0.0.0/16. Esiste una subnet denominata **Subnet**, con un intervallo di indirizzi di 10.0.0.0/24. Tutte le macchine virtuali e i servizi di bilanciamento del carico interni vengono distribuiti in questa rete virtuale.

> [!IMPORTANT]
> Non apportare modifiche alle impostazioni di rete nel sistema operativo guest, inclusi indirizzi IP, server DNS e la subnet. Configurare tutte le impostazioni di rete in Azure. Il servizio Dynamic Host Configuration Protocol (DHCP) propaga le impostazioni.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Indirizzi IP DNS

Per impostare gli indirizzi IP DNS necessari, attenersi alla procedura seguente.

1.  Nel pannello **Server DSN** del portale di Azure verificare che l'opzione **Server DNS** della rete virtuale sia impostata su **DNS personalizzato**.
2.  Selezionare le impostazioni in base al tipo di rete esistente. Per altre informazioni, vedere le seguenti risorse:
    * [Connettività di rete aziendale (cross-premise)][planning-guide-2.2]: aggiungere gli indirizzi IP dei server DNS locali.  
    È possibile estendere i server DNS locali alle macchine virtuali in esecuzione in Azure. In tale scenario è possibile aggiungere gli indirizzi IP delle macchine virtuali di Azure in cui si esegue il servizio DNS.
    * [Distribuzione solo cloud][planning-guide-2.1]: distribuire una macchina virtuale aggiuntiva nella stessa istanza di Rete virtuale che funge da server DNS. Aggiungere gli indirizzi IP delle macchine virtuali di Azure configurate per l'esecuzione del servizio DNS.

    ![Figura 12: Configurare i server DNS per Rete virtuale di Azure][sap-ha-guide-figure-3001]

    _**Figura 12:** Configurare i server DNS per Rete virtuale di Azure_

  > [!NOTE]
  > Se si modificano gli indirizzi IP dei server DNS, è necessario riavviare le macchine virtuali di Azure per applicare la modifica e propagare i nuovi server DNS.
  >
  >

Nell'esempio il servizio DNS viene installato e configurato in queste macchine virtuali Windows:

| Ruolo macchina virtuale | Nome host macchina virtuale | Nome scheda di rete | Indirizzo IP statico |
| --- | --- | --- | --- |
| Primo server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Secondo server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomi host e indirizzi IP statici per l'istanza in cluster di SAP ASCS/SCS e l'istanza in cluster di DBMS

Per una distribuzione locale, sono necessari questi nomi host e indirizzi IP riservati:

| Ruolo nome host virtuale | Nome host virtuale | Indirizzo IP statico virtuale |
| --- | --- | --- |
| Primo nome host virtuale del cluster SAP ASCS/SCS (per la gestione del cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome host virtuale dell'istanza di SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Secondo nome host virtuale del cluster SAP DBMS (gestione del cluster) |pr1-dbms-vir |10.0.0.32 |

Quando si crea il cluster, creare i nomi host virtuale **pr1-ascs-vir** e **pr1-dbms-vir** e gli indirizzi IP associati che gestiscono il cluster stesso. Per informazioni su come eseguire questa operazione, vedere [Raccogliere i nodi del cluster in una configurazione cluster][sap-ha-guide-8.12.1].

È possibile creare manualmente gli altri due nomi host virtuale, **pr1-ascs-sap** e **pr1-dbms-sap** e gli indirizzi IP associati, nel server DNS. L'istanza di SAP ASCS/SCS in cluster e l'istanza di DBMS in cluster usano queste risorse, Per informazioni su come eseguire questa operazione, vedere [Creare un nome host virtuale per un'istanza di SAP ASCS/SCS in cluster][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Impostare gli indirizzi IP statici per le macchine virtuali SAP
Dopo avere distribuito le macchine virtuali da usare nel cluster, è necessario impostare gli indirizzi IP statici per tutte le macchine virtuali. Eseguire questa operazione nella configurazione di Rete virtuale di Azure e non nel sistema operativo guest.

1.  Nel portale di Azure selezionare **Gruppo di risorse** > **Scheda di rete** > **Impostazioni** > **Indirizzo IP**.
2.  Nel pannello **Indirizzi IP** in **Assegnazione** selezionare **Statica**. Nella casella **Indirizzo IP** immettere l'indirizzo IP da usare.

  > [!NOTE]
  > Se si modifica l'indirizzo IP della scheda di rete, è necessario riavviare le macchine virtuali di Azure per applicare la modifica.  
  >
  >

  ![Figura 13: Impostare gli indirizzi IP statici per la scheda di rete di ogni macchina virtuale][sap-ha-guide-figure-3002]

  _**Figura 13:** Impostare gli indirizzi IP statici per la scheda di rete di ogni macchina virtuale_

  Ripetere questo passaggio per tutte le interfacce di rete, ovvero per tutte le macchine virtuali, incluse le macchine virtuali che si vuole usare per il servizio Active Directory/DNS.

In questo esempio vengono usate le macchine virtuali e gli indirizzi IP statici seguenti:

| Ruolo macchina virtuale | Nome host macchina virtuale | Nome scheda di rete | Indirizzo IP statico |
| --- | --- | --- | --- |
| Prima istanza del server applicazioni SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Seconda istanza del server applicazioni SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Ultima istanza del server applicazioni SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primo nodo del cluster per l'istanza di ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Secondo nodo del cluster per l'istanza di ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primo nodo del cluster per l'istanza di DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Secondo nodo del cluster per l'istanza di DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Impostare un indirizzo IP statico per il servizio di bilanciamento del carico interno di Azure

Il modello di Azure Resource Manager per SAP crea un servizio di bilanciamento del carico interno di Azure usato per il cluster dell'istanza di SAP ASCS/SCS e per il cluster DBMS.

> [!IMPORTANT]
> L'indirizzo IP del nome host virtuale di SAP ASCS/SCS è lo stesso del servizio di bilanciamento del carico interno di SAP ASCS/SCS: **pr1-lb-ascs**.
> L'indirizzo IP del nome virtuale di DBMS è lo stesso del servizio di bilanciamento del carico interno di DBMS: **pr1-lb-dbms**.
>
>

Per impostare un indirizzo IP statico per il servizio di bilanciamento del carico interno di Azure:

1.  La distribuzione iniziale imposta l'indirizzo IP del servizio di bilanciamento del carico interno come **dinamico**. Nel pannello **Indirizzi IP** del portale di Azure selezionare **Statica** in **Assegnazione**.
2.  Impostare l'indirizzo IP del bilanciamento del carico interno **pr1-lb-ascs** sull'indirizzo IP del nome host virtuale dell'istanza di SAP ASCS/SCS.
3.  Impostare l'indirizzo IP del bilanciamento del carico interno **pr1-lb-dbms** sull'indirizzo IP del nome host virtuale dell'istanza di DBMS.

  ![Figura 14: Impostare indirizzi IP statici per il servizio di bilanciamento del carico interno per l'istanza di SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Figura 14:** Impostare indirizzi IP statici per il servizio di bilanciamento del carico interno per l'istanza di SAP ASCS/SCS_

Nell'esempio sono presenti due servizi di bilanciamento del carico interno di Azure con questi indirizzi IP statici:

| Ruolo del servizio di bilanciamento del carico interno di Azure | Nome del servizio di bilanciamento del carico interno di Azure | Indirizzo IP statico |
| --- | --- | --- |
| Servizio di bilanciamento del carico interno dell'istanza di SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Servizio di bilanciamento del carico interno di SAP DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure

Il modello di Azure Resource Manager per SAP crea le porte necessarie:
* Un'istanza di ABAP ASCS, con il numero di istanza predefinito **00**
* Un'istanza di Java SCS, con il numero di istanza predefinito **01**

Quando si installa l'istanza di SAP ASCS/SCS, è necessario usare il numero di istanza predefinito **00** per l'istanza di ABAP ASCS e il numero di istanza predefinito **01** per l'istanza di Java SCS.

Creare quindi gli endpoint di bilanciamento del carico interno obbligatori per le porte di SAP NetWeaver.

Per creare gli endpoint di bilanciamento del carico interno obbligatori, creare prima questi endpoint per le porte di SAP NetWeaver ABAP ASCS:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza ASCS con l'istanza numero 00) (ERS con 10) |
| --- | --- | --- |
| Server di accodamento/ *lbrule3200* |32<*NumeroIstanza*> |3200 |
| Server messaggi ABAP/ *lbrule3600* |36<*NumeroIstanza*> |3600 |
| Messaggio ABAP interno/ *lbrule3900* |39<*NumeroIstanza*> |3900 |
| HTTP server messaggi/ *Lbrule8100* |81<*NumeroIstanza*> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5<*NumeroIstanza*>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5<*NumeroIstanza*>14 |50014 |
| Replica di accodamento/ *Lbrule50016* |5<*NumeroIstanza*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*NumeroIstanza*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*NumeroIstanza*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Condivisione file *Lbrule445* | |445 |

_**Tabella 1:** Numeri di porta delle istanze di SAP NetWeaver ABAP ASCS_

Creare quindi questi endpoint di bilanciamento del carico per le porte SCS di SAP NetWeaver Java:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza SCS con numero di istanza 01) (ERS con 11) |
| --- | --- | --- |
| Server di accodamento/ *lbrule3201* |32<*NumeroIstanza*> |3201 |
| Server gateway/ *lbrule3301* |33<*NumeroIstanza*> |3301 |
| Server messaggi Java/ *lbrule3900* |39<*NumeroIstanza*> |3901 |
| HTTP server messaggi/ *Lbrule8101* |81<*NumeroIstanza*> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5<*NumeroIstanza*>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5<*NumeroIstanza*>14 |50114 |
| Replica di accodamento/ *Lbrule50116* |5<*NumeroIstanza*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*NumeroIstanza*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*NumeroIstanza*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Condivisione file *Lbrule445* | |445 |

_**Tabella 2:** Numeri di porta delle istanze di SAP NetWeaver Java SCS_

![Figura 15: Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-figure-3004]

_**Figura 15:** Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure_

Impostare l'indirizzo IP del servizio di bilanciamento del carico **pr1-lb-dbms** sull'indirizzo IP del nome host virtuale dell'istanza di DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure

Per usare numeri diversi per le istanze di SAP ASCS o SCS, è necessario cambiare i nomi e i valori di queste porte rispetto ai valori predefiniti.

1.  Nel portale di Azure selezionare **<*SID*>-lb-ascs load balancer** > **Regole di bilanciamento del carico**.
2.  Per tutte le regole di bilanciamento del carico appartenenti all'istanza di SAP ASCS o SCS, modificare questi valori:

  * NOME
  * Porta
  * Porta back-end

  Ad esempio, per sostituire il numero di istanza di ASCS predefinito 00 con 31, è necessario apportare le modifiche per tutte le porte elencate nella tabella 1.

  Ecco un esempio di aggiornamento per la porta *lbrule3200*.

  ![Figura 16: Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-figure-3005]

  _**Figura 16:** Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Aggiungere macchine virtuali Windows al dominio

Dopo avere assegnato un indirizzo IP statico alle macchine virtuali, aggiungere le macchine virtuali al dominio.

![Figura 17: Aggiungere una macchina virtuale a un dominio][sap-ha-guide-figure-3006]

_**Figura 17:** Aggiungere una macchina virtuale a un dominio_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster per l'istanza di SAP ASCS/SCS

Azure Load Balancer ha un servizio di bilanciamento del carico interno che chiude le connessioni quando rimangono inattive per un periodo di tempo impostato (timeout di inattività). I processi di lavoro SAP nelle istanze delle finestre di dialogo aprono le connessioni al processo di accodamento SAP non appena deve essere inviata la prima richiesta di accodamento/rimozione dalla coda. Queste connessioni restano in genere stabilite fino al riavvio del processo di lavoro o del processo di accodamento. Se tuttavia la connessione rimane inattiva per un determinato periodo di tempo, il servizio di bilanciamento del carico interno di Azure chiude le connessioni. Questo non è un problema perché il processo di lavoro SAP ristabilisce la connessione al processo di accodamento se non esiste più. Queste attività sono documentate nelle tracce degli sviluppatori dei processi SAP, ma creano una grande quantità di contenuti aggiuntivi in tali tracce. È consigliabile modificare i parametri `KeepAliveTime` e `KeepAliveInterval` TCP/IP in entrambi i nodi del cluster. Combinare queste modifiche nei parametri TCP/IP con i parametri del profilo SAP, illustrati più avanti nell'articolo.

Per aggiungere le voci del Registro di sistema in entrambi i nodi del cluster dell'istanza di SAP ASCS/SCS, aggiungere prima queste voci del Registro di sistema di Windows in entrambi i nodi del cluster Windows per SAP ASCS/SCS:

| path | HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome variabile |`KeepAliveTime` |
| Tipo di variabile |REG_DWORD (decimale) |
| Valore |120000 |
| Collegamento alla documentazione |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

_**Tabella 3:** Modificare il primo parametro TCP/IP_

Aggiungere quindi le voci del Registro di sistema Windows in entrambi i nodi del cluster Windows per SAP ASCS/SCS:

| path | HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome variabile |`KeepAliveInterval` |
| Tipo di variabile |REG_DWORD (decimale) |
| Valore |120000 |
| Collegamento alla documentazione |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

_**Tabella 4:** Modificare il secondo parametro TCP/IP_

**Per applicare le modifiche, riavviare entrambi i nodi del cluster**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurare un cluster di Windows Server Failover Clustering per un'istanza di SAP ASCS/SCS

La configurazione di un cluster Windows Server Failover Clustering per un'istanza di SAP ASCS/SCS prevede queste attività:

- Raccolta dei nodi del cluster in una configurazione di cluster
- Configurazione di un controllo di condivisione file del cluster

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Raccogliere i nodi del cluster in una configurazione cluster

1.  Nella procedura guidata Aggiungi ruoli e funzionalità aggiungere il clustering di failover a entrambi i nodi del cluster.
2.  Configurare il cluster di failover usando Gestione cluster di failover. In Gestione Cluster di Failover selezionare **Crea cluster**, quindi aggiungere solo il nome del primo cluster, il nodo A. Non aggiungere il secondo nodo; aggiungere il secondo nodo in un passaggio successivo.

  ![Figura 18: Aggiungere il nome del server o della macchina virtuale del primo nodo del cluster][sap-ha-guide-figure-3007]

  _**Figura 18:** Aggiungere il nome del server o della macchina virtuale del primo nodo del cluster_

3.  Immettere il nome della rete (nome host virtuale) del cluster.

  ![Figura 19: Definire il nome del cluster][sap-ha-guide-figure-3008]

  _**Figura 19:** Definire il nome del cluster_

4.  Dopo avere creato il cluster, eseguire un test di convalida del cluster.

  ![Figura 20: Eseguire il controllo di convalida del cluster][sap-ha-guide-figure-3009]

  _**Figura 20:** Eseguire il controllo di convalida del cluster_

  A questo punto del processo è possibile ignorare gli avvisi sui dischi. Più avanti si aggiungeranno un controllo di condivisione file e i dischi condivisi SIOS. In questa fase, non è necessario un quorum.

  ![Figura 21: Nessun disco quorum trovato][sap-ha-guide-figure-3010]

  _**Figura 21:** Nessun disco quorum trovato_

  ![Figura 22: La risorsa del cluster principale richiede un nuovo indirizzo IP][sap-ha-guide-figure-3011]

  _**Figura 22:** La risorsa del cluster principale richiede un nuovo indirizzo IP_

5.  Modificare l'indirizzo IP del servizio cluster principale. Il cluster non può essere avviato finché non si cambia l'indirizzo IP del servizio cluster principale perché l'indirizzo IP del server punta a uno dei nodi delle macchine virtuali. Eseguire questa operazione nella pagina **Proprietà** della risorsa IP del servizio cluster principale.

  Ad esempio, è necessario assegnare un indirizzo IP (in questo esempio **10.0.0.42**) per il nome host virtuale del cluster **pr1-ascs-vir**.

  ![Figura 23: Nella finestra di dialogo Proprietà modificare l'indirizzo IP][sap-ha-guide-figure-3012]

  _**Figura 23:** Nella finestra di dialogo **Proprietà** modificare l'indirizzo IP_

  ![Figura 24: Assegnare l'indirizzo IP riservato per il cluster][sap-ha-guide-figure-3013]

  _**Figura 24:** Assegnare l'indirizzo IP riservato per il cluster_

6.  Portare online il nome host virtuale del cluster.

  ![Figura 25: Il servizio principale del cluster è in esecuzione con l'indirizzo IP corretto][sap-ha-guide-figure-3014]

  _**Figura 25:** Il servizio principale del cluster è in esecuzione con l'indirizzo IP corretto_

7.  Aggiungere il secondo nodo del cluster.

  Ora che il servizio principale del cluster è attivo e in esecuzione, è possibile aggiungere il secondo nodo del cluster.

  ![Figura 26: Aggiungere il secondo nodo del cluster][sap-ha-guide-figure-3015]

  _**Figura 26:** Aggiungere il secondo nodo del cluster_

8.  Immettere un nome host del secondo nodo cluster.

  ![Figura 27: Immettere il nome host del secondo nodo cluster][sap-ha-guide-figure-3016]

  _**Figura 27:** Immettere il nome host del secondo nodo cluster_

  > [!IMPORTANT]
  > Assicurarsi che la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** **NON** sia selezionata.  
  >
  >

  ![Figura 28: Non selezionare la casella di controllo][sap-ha-guide-figure-3017]

  _**Figura 28:** **NON** selezionare la casella di controllo_

  È possibile ignorare gli avvisi relativi al quorum e ai dischi. Si imposterà il quorum e si condividerà il disco in un secondo momento, come descritto in [Installing SIOS DataKeeper Cluster Edition for SAP ASCS/SCS cluster share disk][sap-ha-guide-8.12.3] (Installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS).

  ![Figura 29: Ignorare gli avvisi sul quorum del disco][sap-ha-guide-figure-3018]

  _**Figura 29:** Ignorare gli avvisi sul quorum del disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurare il controllo di condivisione file del cluster

La configurazione di un controllo di condivisione file del cluster prevede queste attività:

- Creazione di una condivisione file
- Impostazione del quorum del controllo di condivisione file in Gestione cluster di failover

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Creare una condivisione file

1.  Selezionare un controllo di condivisione file invece di un disco quorum. SIOS DataKeeper supporta questa opzione.

  Negli esempi di questo articolo, il controllo di condivisione file è nel server Active Directory/DNS in esecuzione in Azure. Il controllo di condivisione file è denominato **domcontr-0**. Poiché sarà stata configurata una connessione VPN ad Azure (tramite la VPN da sito a sito o Azure ExpressRoute), il servizio Active Directory/DNS è locale e non è adatto all'esecuzione di un controllo di condivisione file.

  > [!NOTE]
  > Se il servizio Active Directory/DNS viene eseguito solo in locale, non configurare il controllo di condivisione file nel sistema operativo Windows con Active Directory/DNS eseguito in locale. La latenza di rete tra i nodi del cluster in esecuzione in Azure e Active Directory/DNS in locale potrebbe essere eccessiva e causare problemi di connettività. Assicurarsi di configurare il controllo di condivisione file in una macchina virtuale di Azure in esecuzione vicino al nodo del cluster.  
  >
  >

  L'unità del quorum richiede almeno 1.024 MB di spazio disponibile. Si consiglia un valore di 2.048 MB di spazio disponibile per l'unità quorum.

2.  Aggiungere l'oggetto del nome cluster.

  ![Figura 30: Assegnare le autorizzazioni nella condivisione per l'oggetto nome cluster][sap-ha-guide-figure-3019]

  _**Figura 30:** Assegnare le autorizzazioni nella condivisione per l'oggetto nome cluster_

  Assicurarsi che le autorizzazioni includano l'autorità di modificare i dati nella condivisione per l'oggetto nome cluster (in questo esempio **pr1-ascs-vir$**).

3.  Per aggiungere l'oggetto nome cluster all'elenco, selezionare **Aggiungi**. Modificare il filtro per cercare gli oggetti computer, oltre a quelli illustrati nella figura 31.

  ![Figura 31: Modificare i tipi di oggetto per includere i computer][sap-ha-guide-figure-3020]

  _**Figura 31:** Modificare i tipi di oggetto per includere i computer_

  ![Figura 32: Selezionare la casella di controllo Computer][sap-ha-guide-figure-3021]

  _**Figura 32:** Selezionare la casella di controllo **Computer**_

4.  Immettere l'oggetto nome del cluster, come illustrato nella figura 31. Poiché il record è già stato creato, è possibile modificare le autorizzazioni, come illustrato nella figura 30.

5.  Selezionare la scheda **Sicurezza** della condivisione e quindi impostare autorizzazioni più dettagliate per l'oggetto del nome cluster.

  ![Figura 33: Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file][sap-ha-guide-figure-3022]

  _**Figura 33:** Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Impostare il quorum del controllo di condivisione file in Gestione cluster di failover

1.  Aprire Configurazione guidata quorum del cluster.

  ![Figura 34: Avviare la procedura guidata Configura impostazioni quorum del cluster][sap-ha-guide-figure-3023]

  _**Figura 34:** Avviare la procedura guidata Configura impostazioni quorum del cluster_

2.  Nella pagina **Selezione configurazione quorum** selezionare **Seleziona il quorum di controllo**.

  ![Figura 35: Configurazioni del quorum tra cui è possibile scegliere][sap-ha-guide-figure-3024]

  _**Figura 35:** Configurazioni del quorum tra cui è possibile scegliere_

3.  Nella pagina **Seleziona il quorum di controllo** selezionare **Configura condivisione file di controllo**.

  ![Figura 36: Selezionare il controllo di condivisione file][sap-ha-guide-figure-3025]

  _**Figura 36:** Selezionare il controllo di condivisione file_

4.  Immettere il percorso UNC della condivisione file (in questo esempio \\domcontr-0\FSW) . Selezionare **Avanti** per visualizzare un elenco delle modifiche che è possibile apportare.

  ![Figura 37: Definire il percorso della condivisione file per la condivisione di controllo][sap-ha-guide-figure-3026]

  _**Figura 37:** Definire il percorso della condivisione file per la condivisione di controllo_

5.  Selezionare le modifiche desiderate e quindi selezionare **Avanti**. È necessario riconfigurare correttamente il cluster, come illustrato nella figura 38.  

  ![Figura 38: Conferma della riconfigurazione del cluster][sap-ha-guide-figure-3027]

  _**Figura 38:** Conferma della riconfigurazione del cluster_

Dopo aver installato correttamente il Cluster di failover Windows, è necessario modificare alcune soglie per adattare il rilevamento del failover alle condizioni in Azure. I parametri da modificare sono documentati in questo blog: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Supponendo che le due VM che compongono la configurazione del cluster Windows per ASCS/SCS siano nella stessa SubNet, è necessario modificare i parametri seguenti impostando i valori indicati:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Queste impostazioni sono state testate con i clienti e hanno fornito un buon compromesso in quanto da un lato sono sufficientemente resilienti e dall'altro offrono un failover sufficientemente rapido in condizioni di errore reale del software SAP o del nodo/VM. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installare SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS

Ora è disponibile una configurazione funzionante di Windows Server Failover Clustering in Azure ma, per installare un'istanza di SAP ASCS/SCS, è necessaria una risorsa disco condiviso. Non è possibile creare le risorse del disco condiviso necessarie in Azure. SIOS DataKeeper Cluster Edition è una soluzione di terze parti che è possibile usare per creare le risorse disco condiviso.

L'installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS prevede queste attività:

- Aggiunta di .NET Framework 3.5
- Installazione di SIOS DataKeeper
- Configurazione di SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Aggiungere .NET Framework 3.5
Microsoft .NET Framework 3.5 non viene attivato né installato automaticamente in Windows Server 2012 R2, Poiché SIOS DataKeeper richiede l'installazione di .NET Framework in tutti i nodi in cui si installa DataKeeper, è necessario installare .NET Framework 3.5 nel sistema operativo guest di tutte le macchine virtuali nel cluster.

È possibile aggiungere .NET Framework 3.5 in due modi:

- Usare l'Aggiunta guidata ruoli e funzionalità in Windows come illustrato nella figura 39.

  ![Figura 39: Installare .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità][sap-ha-guide-figure-3028]

  _**Figura 39:** Installare .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità_

  ![Figura 40: Indicatore di stato dell'installazione quando si installa .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità][sap-ha-guide-figure-3029]

  _**Figura 40:** Indicatore di stato dell'installazione quando si installa .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità_

- Usare lo strumento da riga di comando dism.exe. Per questo tipo di installazione è necessario accedere alla directory SxS nei supporti di installazione di Windows. A un prompt dei comandi con privilegi elevati digitare:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installare SIOS DataKeeper

Installare SIOS DataKeeper Cluster Edition in ogni nodo del cluster. Per creare una risorsa di archiviazione condivisa virtuale con SIOS DataKeeper, creare un mirror sincronizzato e quindi simulare la risorsa di archiviazione condivisa del cluster.

Prima di installare il software SIOS, creare l'utente di dominio **DataKeeperSvc**.

> [!NOTE]
> Aggiungere l'utente **DataKeeperSvc** al gruppo **Administrators locale** in entrambi i nodi del cluster.
>
>

Per installare SIOS DataKeeper:

1.  Installare il software SIOS in entrambi i nodi del cluster.

  ![Programma di installazione SIOS][sap-ha-guide-figure-3030]

  ![Figura 41: Prima schermata dell'installazione di SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Figura 41:** Prima schermata dell'installazione di SIOS DataKeeper_

2.  Nella finestra di dialogo illustrata nella figura 42 selezionare **Yes** (Sì).

  ![Figura 42: DataKeeper segnala che un servizio verrà disabilitato][sap-ha-guide-figure-3032]

  _**Figura 42:** DataKeeper segnala che un servizio verrà disabilitato_

3.  Nella finestra di dialogo illustrata nella figura 43, si consiglia di selezionare **Domain or Server account** (Account di dominio o server).

  ![Figura 43: Selezione dell'utente per SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Figura 43:** Selezione dell'utente per SIOS DataKeeper_

4.  Specificare il nome utente dell'account di dominio e le password creati per SIOS DataKeeper.

  ![Figura 44: Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Figura 44:** Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper_

5.  Installare la chiave di licenza per l'istanza di SIOS DataKeeper, come illustrato nella figura 45.

  ![Figura 45: Specificare la chiave di licenza di SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Figura 45:** Specificare la chiave di licenza di SIOS DataKeeper_

6.  Quando richiesto, riavviare la macchina virtuale.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurare SIOS DataKeeper

Dopo l'installazione di SIOS DataKeeper su entrambi i nodi è necessario avviare la configurazione. L'obiettivo della configurazione è eseguire la replica di dati sincrona tra i dischi aggiuntivi collegati a ogni macchina virtuale.

1.  Avviare lo strumento di configurazione e gestione di DataKeeper e selezionare il collegamento **Connect Server** (Connetti server). Nella figura 46, questa opzione è racchiusa in un cerchio rosso.

  ![Figura 46: Strumento di configurazione e gestione di SIOS DataKeeper][sap-ha-guide-figure-3036]

  _**Figura 46:** Strumento di configurazione e gestione di SIOS DataKeeper_

2.  Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di configurazione e gestione; eseguire l'operazione in un altro passaggio per il secondo nodo.

  ![Figura 47: Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di configurazione e gestione; eseguire l'operazione in un altro passaggio per il secondo nodo][sap-ha-guide-figure-3037]

  _**Figura 47:** Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di configurazione e gestione; eseguire l'operazione in un altro passaggio per il secondo nodo_

3.  Creare il processo di replica tra i due nodi.

  ![Figura 48: Creare un processo di replica][sap-ha-guide-figure-3038]

  _**Figura 48:** Creare un processo di replica_

  Per la creazione di un processo di replica è disponibile una procedura guidata.
4.  Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di origine.

  ![Figura 49: Definire il nome del processo di replica][sap-ha-guide-figure-3039]

  _**Figura 49:** Definire il nome del processo di replica_

  ![Figura 50: Definire i dati di base per il nodo che deve essere il nodo di origine corrente][sap-ha-guide-figure-3040]

  _**Figura 50:** Definire i dati di base per il nodo che deve essere il nodo di origine corrente_

5.  Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione.

  ![Figura 51: Definire i dati di base per il nodo che deve essere il nodo di destinazione corrente][sap-ha-guide-figure-3041]

  _**Figura 51:** Definire i dati di base per il nodo che deve essere il nodo di destinazione corrente_

6.  Definire gli algoritmi di compressione. Nell'esempio è consigliabile comprimere il flusso di replica. Soprattutto in caso di risincronizzazione, la compressione del flusso di replica riduce notevolmente il tempo necessario per l'operazione. Si noti che la compressione usa le risorse di CPU e RAM di una macchina virtuale. Con l'aumentare del tasso di compressione aumenta anche il volume delle risorse di CPU usate. È possibile anche modificare questa impostazione in un secondo momento.

7.  Un'altra impostazione da verificare è se la replica viene eseguita in modalità sincrona o asincrona. *Per proteggere le configurazioni di SAP ASCS/SCS, è necessario usare la replica sincrona*.  

  ![Figura 52: Definire i dettagli della replica][sap-ha-guide-figure-3042]

  _**Figura 52:** Definire i dettagli della replica_

8.  Definire se il volume replicato dal processo di replica deve essere rappresentato in una configurazione di cluster WSFC (Windows Server Failover Clustering) come disco condiviso. Per la configurazione di SAP ASCS/SCS è necessario scegliere **Yes** in modo che il cluster di Windows rilevi il volume replicato come disco condiviso che può essere usato come volume del cluster.

  ![Figura 53: Selezionare Yes (Sì) per impostare il volume replicato come volume del cluster][sap-ha-guide-figure-3043]

  _**Figura 53:** Selezionare **Yes** (Sì) per impostare il volume replicato come volume del cluster_

  Dopo aver creato il volume, lo strumento di configurazione e gestione di DataKeeper mostra che il processo di replica è attivo.

  ![Figura 54: Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo][sap-ha-guide-figure-3044]

  _**Figura 54:** Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo_

  Gestione cluster di failover visualizza ora il disco come disco di DataKeeper, come illustrato nella figura 55.

  ![Figura 55: Gestione cluster di failover visualizza il disco replicato da DataKeeper][sap-ha-guide-figure-3045]

  _**Figura 55:** Gestione cluster di failover visualizza il disco replicato da DataKeeper_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installare il sistema SAP NetWeaver

La configurazione del sistema DBMS non viene descritta perché varia a seconda del sistema DBMS usato. Si presuppone tuttavia che i problemi di disponibilità elevata del sistema DBMS vengano risolti con le funzionalità supportate dai diversi fornitori di sistemi DBMS per Azure, ad esempio AlwaysOn o mirroring del database per SQL Server e Oracle Data Guard per database Oracle. Nello scenario usato in questo articolo, non sono state aggiunte altre funzionalità di protezione per il sistema DBMS.

Non esistono particolari considerazioni per il caso in cui servizi DBMS differenti interagiscono con questa configurazione di SAP ASCS/SCS in cluster in Azure.

> [!NOTE]
> La procedura di installazione dei sistemi SAP NetWeaver ABAP, Java e ABAP + Java è praticamente identica. La differenza principale è che un sistema SAP ABAP ha un'istanza di ASCS. Il sistema SAP Java ha un'istanza di SCS. Il sistema SAP ABAP + Java ha un'istanza ASCS e un'istanza SCS in esecuzione nello stesso gruppo di cluster di failover Microsoft. Eventuali differenze di installazione per ogni stack di installazione di SAP NetWeaver verranno indicate in modo esplicito. Si presume che tutte le altre parti siano uguali.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installare SAP con un'istanza di ASCS/SCS a disponibilità elevata

> [!IMPORTANT]
> Non inserire il file di paging nei volumi con mirroring di DataKeeper. DataKeeper non supporta i volumi con mirroring. È possibile lasciare il file di paging nell'unità temporanea D di una macchina virtuale di Azure, ovvero l'impostazione predefinita. Se non è già presente, spostare il file di paging di Windows nell'unità D: della macchina virtuale di Azure.
>
>

L'installazione di SAP con un'istanza di ASCS/SCS a disponibilità elevata prevede queste attività:

- Creazione di un nome host virtuale per l'istanza di SAP ASCS/SCS in cluster
- Installazione del primo nodo del cluster SAP
- Modifica del profilo SAP dell'istanza di ASCS/SCS
- Aggiunta di un porta probe
- Apertura della porta probe di Windows Firewall

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Creare un nome host virtuale per l'istanza di SAP ASCS/SCS in cluster

1.  In Gestore DNS di Windows creare una voce DNS per il nome host virtuale dell'istanza di ASCS/SCS.

  > [!IMPORTANT]
  > L'indirizzo IP assegnato al nome host virtuale dell'istanza di ASCS/SCS deve essere lo stesso indirizzo IP assegnato ad Azure Load Balancer (**<*SID*>-lb-ascs**).  
  >
  >

  L'indirizzo IP del nome host virtuale di SAP ASCS/SCS (**pr1-ascs-sap**) è lo stesso indirizzo IP di Azure Load Balancer (**pr1-lb-ascs**).

  ![Figura 56: Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

  _**Figura 56:** Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS_

2.  Per definire l'indirizzo IP assegnato al nome host virtuale, selezionare **Gestore DNS** > **Dominio**.

  ![Figura 57: Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster di SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Figura 57:** Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster di SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installare il primo nodo del cluster SAP

1.  Eseguire l'opzione del primo nodo del cluster nel nodo A, ad esempio nell'host **pr1-ascs-0**.
2.  Per mantenere le porte predefinite per il bilanciamento del carico interno di Azure, selezionare:

  * Per il **sistema ABAP**: **ASCS** numero di istanza **00**
  * Per il **sistema Java**: **SCS** numero di istanza **01**
  * Per il **sistema ABAP + Java**: **ASCS** numero di istanza **00** e **SCS** numero di istanza **01**

  Per usare altri numeri di istanza rispetto a 00 per l'istanza di ABAP ASCS e rispetto a 01 per l'istanza di Java SCS, è prima necessario modificare le regole predefinite del servizio di bilanciamento del carico interno di Azure, come descritto in [Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-8.9].

Le attività successive non sono descritte nella documentazione di installazione generale di SAP.

> [!NOTE]
> La documentazione di installazione di SAP descrive come installare il primo nodo del cluster ASCS/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificare il profilo SAP dell'istanza di ASCS/SCS

È necessario aggiungere un nuovo parametro del profilo. Questo parametro impedisce la chiusura delle connessioni tra i processi di lavoro SAP e il server di accodamento quando sono inattivi per un tempo eccessivo. Lo scenario del problema è stato descritto nella sezione [Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster per l'istanza di SAP ASCS/SCS][sap-ha-guide-8.11]. In quella sezione sono anche illustrate due modifiche ad alcuni parametri della connessione TCP/IP di base. Nel secondo passaggio è necessario impostare il server di accodamento per l'invio di un segnale `keep_alive` per impedire alle connessioni di raggiungere la soglia di inattività del servizio di bilanciamento del carico interno di Azure.

Per modificare il profilo SAP dell'istanza di ASCS/SCS:

1.  Aggiungere questo parametro al profilo dell'istanza di SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  Nell'esempio il percorso è:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Ad esempio, al profilo dell'istanza di SAP SCS e al percorso corrispondente:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Per applicare le modifiche, riavviare l'istanza di SAP ASCS/SCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Aggiungere una porta probe

Usare la funzionalità probe del servizio di bilanciamento del carico interno per il corretto funzionamento della configurazione del cluster con Azure Load Balancer. Il servizio di bilanciamento del carico interno di Azure distribuisce in genere il carico di lavoro in ingresso in modo uniforme tra le macchine virtuali. Questa operazione non funziona tuttavia in alcune configurazioni di cluster perché è attiva una sola istanza. L'altra istanza è passiva e non può accettare carico di lavoro. La funzionalità probe è utile quando il servizio di bilanciamento del carico interno di Azure assegna il carico di lavoro solo a un'istanza attiva. Con la funzionalità probe, il servizio di bilanciamento del carico interno può rilevare l'istanza attiva e usare solo questa istanza come destinazione del carico di lavoro.

Per aggiungere una porta probe:

1.  Verificare l'impostazione di **ProbePort** corrente eseguendo il comando di PowerShell seguente. Eseguire il comando all'interno di una delle macchine virtuali della configurazione del cluster.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  Definire una porta probe. Il numero della porta probe predefinita è **0**. In questo esempio viene usata la porta probe **62000**.

  ![Figura 58: La porta probe della configurazione del cluster è 0 per impostazione predefinita][sap-ha-guide-figure-3048]

  _**Figura 58:** La porta probe predefinita della configurazione del cluster è 0_

  Il numero della porta è definito nei modelli di Azure Resource Manager per SAP. È possibile assegnare il numero della porta in PowerShell.

  Per impostare un nuovo valore di ProbePort per la risorsa cluster **SAP <*SID*> IP**, eseguire il seguente script di PowerShell. Aggiornare le variabili PowerShell per l'ambiente. Dopo l'esecuzione dello script, verrà richiesto di riavviare il gruppo di cluster SAP per attivare le modifiche.

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

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

  Dopo avere portato online il ruolo cluster **SAP <*SID*>**, verificare che **ProbePort** sia impostato sul nuovo valore.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![Figura 59: Eseguire il probe della porta del cluster dopo aver impostato il nuovo valore][sap-ha-guide-figure-3049]

  _**Figura 59:** Eseguire il probe della porta del cluster dopo aver impostato il nuovo valore_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Aprire la porta probe di Windows Firewall

È necessario aprire una porta probe di Windows Firewall in entrambi i nodi del cluster. Usare lo script seguente per aprire una porta probe di Windows Firewall. Aggiornare le variabili PowerShell per l'ambiente.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** è impostato su **62000**. Ora è possibile accedere alla condivisione file **\\\ascsha-clsap\sapmnt** da altri host come **ascsha-dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installare l'istanza di database

Per installare l'istanza di database, seguire la procedura descritta nella documentazione sull'installazione di SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installare il secondo nodo del cluster

Per installare il secondo nodo del cluster, seguire i passaggi descritti nella guida all'installazione di SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Cambiare il tipo di avvio dell'istanza del servizio Windows SAP ERS

Cambiare il tipo di avvio del servizio Windows SAP ERS su **(avvio ritardato) automatico** in entrambi i nodi del cluster.

![Figura 60: Cambiare il tipo di servizio per l'istanza di SAP ERS su ritardo automatico][sap-ha-guide-figure-3050]

_**Figura 60:** Cambiare il tipo di servizio per l'istanza di SAP ERS su ritardo automatico_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installare Primary Application Server (PAS) SAP

Installare l'istanza di Primary Application Server (PAS) <*SID*>-di-0 nella macchina virtuale designata per ospitare PAS. Non sono presenti dipendenze da specifiche impostazioni di Azure o DataKeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installare Additional Application Server (AAS) SAP

Installare un'istanza di Additional Application Server (AAS) SAP in tutte le macchine virtuali designate per ospitare un'istanza del server applicazioni SAP. Ad esempio, in <*SID*>-di-1 per <*SID*>-di-&lt;n&gt;.

> [!NOTE]
> Questa operazione completa l'installazione di un sistema SAP NetWeaver a disponibilità elevata. Procedere quindi con il test del failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testare il failover e la replica SIOS dell'istanza di SAP ASCS/SCS
È possibile testare e monitorare con facilità un failover dell'istanza di SAP ASCS/SCS e una replica del disco SIOS usando Gestione cluster di failover e lo strumento di configurazione e gestione di SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Istanza di SAP ASCS/SCS in esecuzione nel nodo A del cluster

Il gruppo di cluster **SAP PR1** è in esecuzione nel nodo A del cluster, ad esempio in **pr1-ascs-0**. Assegnare al nodo A del cluster l'unità disco condivisa S, appartenente al gruppo di cluster **SAP PR1** e usata dall'istanza di ASCS/SCS.

![Figura 61: Gestione cluster di failover: il gruppo di cluster <SID> SAP è in esecuzione nel nodo A del cluster][sap-ha-guide-figure-5000]

_**Figura 61:** Gestione cluster di failover: il gruppo di cluster SAP <*SID*> è in esecuzione nel nodo A del cluster_

Nello strumento di configurazione e gestione di SIOS DataKeeper è possibile verificare che i dati di dischi condivisi vengano replicati in modo sincrono dall'unità S del volume di origine nel nodo A del cluster all'unità S del volume di destinazione nel nodo B del cluster, ad esempio da **pr1-ascs-0 [10.0.0.40]** a **pr1-ascs-1 [10.0.0.41]**.

![Figura 62: In SIOS DataKeeper, replicare il volume locale dal nodo A al nodo B del cluster][sap-ha-guide-figure-5001]

_**Figura 62:** In SIOS DataKeeper, replicare il volume locale dal nodo A al nodo B del cluster_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover dal nodo A al nodo B

1.  Scegliere una di queste opzioni per avviare un failover del gruppo di cluster <*SID*> SAP dal nodo A al nodo B del cluster:
  - Usare Gestione cluster di failover  
  - Usare PowerShell per Clustering di failover

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Riavviare il nodo A del cluster nel sistema operativo guest di Windows. Verrà avviato un failover automatico del gruppo di cluster <*SID*> SAP dal nodo A al nodo B.  
3.  Riavviare il nodo A del cluster nel portale di Azure. Verrà avviato un failover automatico del gruppo di cluster <*SID*> SAP dal nodo A al nodo B.  
4.  Riavviare il nodo A del cluster usando Azure PowerShell. Verrà avviato un failover automatico del gruppo di cluster <*SID*> SAP dal nodo A al nodo B.

  Il gruppo di cluster SAP <*SID*> è in esecuzione nel nodo B del cluster, ad esempio in **pr1-ascs-1**.

  ![Figura 63: In Gestione cluster di failover, il gruppo di cluster <SID> SAP è in esecuzione nel nodo B del cluster][sap-ha-guide-figure-5002]

  _**Figura 63**: In Gestione cluster di failover, il gruppo di cluster SAP &lt;*SID*&gt; è in esecuzione nel nodo B del cluster_

  Il disco condiviso è ora montato nel nodo B del cluster. SIOS DataKeeper replica i dati dall'unità S del volume di origine nel nodo B del cluster all'unità S del volume di destinazione nel nodo A del cluster, ad esempio da **pr1-ascs-1 [10.0.0.41]** a **pr1-ascs-0 [10.0.0.40]**.

  ![Figura 64: SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster][sap-ha-guide-figure-5003]

  _**Figura 64:** SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster_
