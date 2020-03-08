---
title: Introduzione a SAP in macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle soluzioni SAP eseguite in macchine virtuali (VM) in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a02fb7ab7e42f2a56041638a3d883287da3eb42
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674495"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Usare Azure per ospitare ed eseguire scenari di carico di lavoro SAP

Quando si usa Microsoft Azure, è possibile eseguire in modo affidabile i carichi di lavoro e gli scenari SAP cruciali in una piattaforma scalabile, conforme e collaudata per le aziende. Ottieni la scalabilità, la flessibilità e il risparmio sui costi di Azure. Con la partnership estesa tra Microsoft e SAP, è possibile eseguire applicazioni SAP in scenari di sviluppo e test e di produzione in Azure ed essere completamente supportati. Da SAP NetWeaver a SAP S/4HANA, da SAP BI in Linux a Windows e da SAP HANA a SQL, abbiamo una copertura.

Oltre a ospitare scenari di SAP NetWeaver con i diversi sistemi DBMS in Azure, è possibile ospitare altri scenari di carico di lavoro SAP, ad esempio SAP BI in Azure. 

L'univocità di Azure per SAP HANA è un'offerta che consente di impostare Azure separatamente. Per consentire l'hosting di più risorse di memoria e CPU che richiedono scenari SAP che coinvolgono SAP HANA, Azure offre l'uso di hardware bare metal dedicato ai clienti. Usare questa soluzione per eseguire distribuzioni SAP HANA che richiedono fino a 24 TB (scalabilità orizzontale 120 TB) per S/4HANA o un altro carico di lavoro SAP HANA. 

L'hosting di scenari di carico di lavoro SAP in Azure può anche creare requisiti per l'integrazione di identità e Single Sign-On. Questa situazione può verificarsi quando si usa Azure Active Directory (Azure AD) per connettere componenti SAP diversi e offerte SaaS (software-as-a-Service) di SAP o piattaforma distribuita come servizio (PaaS). Un elenco di questi scenari di integrazione e Single Sign-On con le entità Azure AD e SAP viene descritto e documentato nella sezione "integrazione delle identità e Single Sign-On di AAD SAP".

## <a name="changes-to-the-sap-workload-section"></a>Modifiche alla sezione del carico di lavoro SAP
Alla fine di questo articolo sono elencate le modifiche apportate ai documenti nella sezione del carico di lavoro SAP in Azure. Le voci nel log delle modifiche vengono mantenute per circa 180 giorni.

## <a name="you-want-to-know"></a>Si vuole saperlo
In caso di domande specifiche, in questa sezione della pagina iniziale verrà fatto riferimento a documenti o flussi specifici. Si desidera essere a conoscenza di:

- Le macchine virtuali di Azure e le unità di istanze large di HANA sono supportate per le versioni del software SAP e per le versioni del sistema operativo. Leggere il documento relativo a [come il software SAP è supportato per la distribuzione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) per le risposte e il processo di individuazione delle informazioni
- Quali scenari di distribuzione SAP sono supportati con le macchine virtuali di Azure e le istanze large di HANA. Le informazioni sugli scenari supportati sono disponibili nei documenti:
    - [Carico di lavoro SAP negli scenari supportati da macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Scenari supportati per istanze large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Quali servizi di Azure, tipi di VM di Azure e archiviazione di Azure sono disponibili nelle diverse aree di Azure, controllare i prodotti del sito [disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA in Azure (istanze Large)

Una serie di documenti consente di SAP HANA in Azure (istanze large) o in caso di istanze large di HANA. Per informazioni sulle istanze large di HANA, iniziare con la panoramica del documento [e l'architettura di SAP Hana in Azure (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e passare attraverso la documentazione correlata nella sezione relativa alle istanze large di Hana.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA in macchine virtuali di Azure
Questa sezione della documentazione presenta diversi aspetti relativi a SAP HANA. Come prerequisito, è necessario avere familiarità con i servizi principali di Azure che forniscono servizi elementari di Azure IaaS. È quindi necessario conoscere il calcolo, l'archiviazione e la rete di Azure. Molti di questi argomenti sono gestiti nella [Guida alla pianificazione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)correlata a SAP NetWeaver. 

Per informazioni su HANA in Azure, vedere gli articoli seguenti e i relativi SottoArticoli:

- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilità elevata di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guida al backup per SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver distribuito in macchine virtuali di Azure
Questa sezione elenca la documentazione di pianificazione e distribuzione per SAP NetWeaver e Business One in Azure. La documentazione è incentrata sulle nozioni di base e sull'uso di database non HANA con un carico di lavoro SAP in Azure. I documenti e gli articoli per la disponibilità elevata sono anche le fondamenta per la disponibilità elevata di HANA in Azure, ad esempio:

- [Guida alla pianificazione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Connettore SAP LaMa per Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Per informazioni sui database non HANA in un carico di lavoro SAP in Azure, vedere:

- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Distribuzione DBMS per SQL Server di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Distribuzione di SAP MaxDB, Live cache e Content Server in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Per informazioni sui database SAP HANA in Azure, vedere la sezione "SAP HANA in macchine virtuali di Azure".

Per informazioni sulla disponibilità elevata di un carico di lavoro SAP in Azure, vedere:

- [Disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Questo documento fa riferimento a diversi altri documenti sull'architettura e sullo scenario. Nei documenti degli scenari successivi sono disponibili collegamenti a documenti tecnici dettagliati che illustrano la distribuzione e la configurazione dei diversi metodi di disponibilità elevata. I diversi documenti che illustrano come stabilire e configurare la disponibilità elevata per un carico di lavoro di SAP NetWeaver coprono i sistemi operativi Linux e Windows.


Per informazioni sull'integrazione tra Azure Active Directory (Azure AD) e i servizi SAP e Single Sign-On, vedere:

- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Ambiente S/4HANA: fiori Launchpad per SAML Single Sign-On con Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Per informazioni sull'integrazione dei servizi di Azure nei componenti SAP, vedere:

- [Usare SAP HANA in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usare il connettore SAP BW in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory offre l'integrazione di dati SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Registro modifiche

- 03/05/2020: modifiche della struttura e modifiche del contenuto per le aree di Azure e le macchine virtuali di Azure in [pianificazione e implementazione di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: modificare la [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES con e per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) per modificare il layout del volume e più efficiente
- 03/01/2020: Guida al backup rilavorato [per SAP Hana nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) per includere il servizio backup di Azure. Contenuto ridotto e condensato in [SAP Hana backup di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) ed eliminato un terzo documento che riguarda il backup tramite snapshot del disco. Il contenuto viene gestito nella Guida al backup per SAP HANA in macchine virtuali di Azure 
- 02/27/2020: modificare la [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES con e per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SLES guida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) a più SID per modificare il parametro del cluster "on fail"
- 02/26/2020: modificare le [configurazioni di archiviazione delle macchine virtuali SAP Hana di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) per chiarire file System scelta per Hana in Azure
- 02/26/2020: modifica nell' [architettura e negli scenari di disponibilità elevata per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) includere il collegamento alla disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure nella Guida a più SID di RHEL
- 02/26/2020: modifica della [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES con e per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [disponibilità elevata per SAP NetWeaver in RHEL con Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) per rimuovere l'istruzione che non supporta il cluster ASC/ERS a più SID
- 02/26/2020: versione di [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in RHEL guida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) a più SID per aggiungere un collegamento alla guida del cluster SUSE a più SID
- 02/25/2020: modificare l' [architettura e gli scenari di disponibilità elevata per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) per aggiungere collegamenti a articoli a disponibilità elevata più recenti
- 02/25/2020: modificare la [disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure in SUSE Linux Enterprise Server con pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) per puntare a documenti che descrivono l'accesso a un endpoint pubblico con Azure Load Balancer standard
- 02/21/2020: Revisione completa della [distribuzione DBMS di macchine virtuali di Azure per SAP ASE per il carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: modificare la [configurazione di archiviazione della macchina virtuale di Azure SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) per rappresentare la nuova raccomandazione in dimensioni stripe per/Hana/data e aggiungere l'impostazione dell'utilità di pianificazione I/O
- 02/21/2020: modifiche nei documenti di istanze large di HANA per rappresentare SKU appena certificati di S224 e S224m
- 02/21/2020: modificare la disponibilità [elevata delle macchine virtuali di Azure per SAP NetWeaver in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [la disponibilità elevata della macchina virtuale di Azure per SAP NetWeaver in RHEL con Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) per modificare i vincoli del cluster per l'architettura di Accodamento Server Replication 2 (ENSA2)
- 02/20/2020: modificare la [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure nella Guida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) a più SID di SLES per aggiungere un collegamento alla guida del cluster SUSE a più SID
- 02/13/2020: modifiche apportate alla [pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) per implementare collegamenti a nuovi documenti
- 02/13/2020: aggiunta del nuovo [carico di lavoro SAP del documento in uno scenario supportato dalla macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: aggiunto nuovo documento [quale software SAP è supportato per la distribuzione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: modificare la [disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure nel Server Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) per puntare a documenti che descrivono l'accesso a un endpoint pubblico con Azure Load Balancer standard
- 02/13/2020: aggiungere i nuovi tipi di VM alle [configurazioni e alle certificazioni SAP in esecuzione in Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: aggiungere nuove note di supporto SAP [carichi di lavoro SAP in Azure: elenco di controllo di pianificazione e distribuzione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: modificare la disponibilità [elevata delle macchine virtuali di Azure per SAP NetWeaver in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e la [disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in RHEL con Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) per allineare i timeout delle risorse del cluster alle raccomandazioni per il timeout di Red Hat
- 02/11/2020: rilascio di [SAP Hana sulla migrazione di istanze large di Azure alle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: modificare [la connettività degli endpoint pubblici per le macchine virtuali con ILB standard di Azure negli scenari di disponibilità elevata di SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) per aggiornare la schermata di esempio NSG
- 02/03/2020: modificare la [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) e [disponibilità elevata per SAP NW in macchine virtuali di Azure in SLES con e per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) per rimuovere l'avviso sull'uso di Dash nei nomi host dei nodi del cluster in SLES
- 01/28/2020: modificare la [disponibilità elevata delle SAP Hana nelle macchine virtuali di Azure in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) per allineare i timeout delle risorse del cluster SAP Hana alle raccomandazioni per il timeout di Red Hat
- 01/17/2020: modificare i [gruppi di posizionamento di prossimità di Azure per una latenza di rete ottimale con le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) per modificare la sezione dello stato di trasferimento di macchine virtuali esistenti in un gruppo di posizionamento vicino
- 01/17/2020: modificare [le configurazioni del carico di lavoro SAP con zone di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) per puntare alla procedura che consente di automatizzare le misurazioni della latenza tra zone di disponibilità
- 01/16/2020: modificare [le modalità di installazione e configurazione di SAP Hana (istanze large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) per adattare le versioni del sistema operativo alla directory hardware Hana IaaS
- 01/16/2020: modifiche alla [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure nella Guida a più SID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) per aggiungere istruzioni per i sistemi SAP, usando l'architettura di Accodamento server 2 (ENSA2)
- 01/10/2020: le modifiche apportate all' [SAP Hana con scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp files in SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) e in [SAP Hana con scalabilità orizzontale con nodo standby in macchine virtuali di Azure con Azure NetApp files su RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) per aggiungere istruzioni su come rendere permanenti le modifiche `nfs4_disable_idmapping`.
- 01/10/2020: modifiche alla [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SLES con Azure NetApp files per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e in [macchine virtuali di Azure disponibilità elevata per SAP NetWeaver in RHEL con Azure NetApp files per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) per aggiungere istruzioni su come montare Azure NetApp files volumi NFSv4.
- 12/23/2019: rilascio della [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure nella Guida a più SID di SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: rilascio di [SAP Hana con scalabilità orizzontale con nodo standby in macchine virtuali di Azure con Azure NetApp files in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: le modifiche apportate all' [SAP Hana con scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp files in SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) per semplificare la configurazione del mapping degli ID NFS e modificare l'interfaccia di rete primaria consigliata per semplificare il routing.
- 11/15/2019: modifiche minime apportate alla [disponibilità elevata per SAP NetWeaver in SUSE Linux Enterprise Server con Azure NetApp files per le applicazioni SAP](high-availability-guide-suse-netapp-files.md) e [disponibilità elevata per sap netweaver in Red Hat Enterprise Linux con Azure NetApp files per le applicazioni SAP](high-availability-guide-rhel-netapp-files.md) per chiarire le restrizioni delle dimensioni del pool di capacità e l'istruzione Remove che è supportata solo la versione NFSv3.
- 11/12/2019: versione di [disponibilità elevata per SAP NetWeaver in Windows con Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: modifiche alla [disponibilità elevata di SAP Hana nelle VM di Azure in SUSE Linux Enterprise Server](sap-hana-high-availability.md), [configurare SAP Hana la replica di sistema in macchine virtuali (VM)](sap-hana-high-availability-rhel.md)di Azure, [disponibilità elevata di macchine virtuali di azure per SAP NETWEAVER su SUSE Linux Enterprise Server per applicazioni SAP](high-availability-guide-suse.md), [disponibilità elevata di macchine virtuali di azure per SAP NetWeaver in SUSE Linux Enterprise Server con Azure NetApp files](high-availability-guide-suse-netapp-files.md), [disponibilità elevata di macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux ](high-availability-guide-rhel.md), [Disponibilità elevata di macchine virtuali di Azure per SAP netweaver in Red Hat Enterprise Linux con Azure NetApp files](high-availability-guide-rhel-netapp-files.md), [disponibilità elevata per NFS in macchine virtuali di Azure in SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), GlusterFS in macchine virtuali di [Azure in Red Hat Enterprise Linux per SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) per consigliare il servizio di bilanciamento del carico standard di Azure  
- 11/08/2019: modifiche nell' [elenco di controllo della pianificazione e distribuzione del carico di lavoro SAP](sap-deployment-checklist.md) per chiarire la raccomandazione  
- 11/04/2019: modifiche apportate alla configurazione di [pacemaker in SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) per creare il cluster direttamente con la configurazione unicast  
- 10/29/2019: rilascio della [connettività degli endpoint pubblici per le macchine virtuali con Load Balancer standard di Azure in scenari a disponibilità elevata di SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: modifiche in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) e [SAP Hana con scalabilità orizzontale con nodo standby in macchine virtuali di Azure con Azure NetApp files su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) per chiarire il protocollo NFS per il volume/Hana/Shared
- 10/22/2019: modifica della [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server con Azure NetApp files per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [disponibilità elevata per NFS in macchine virtuali di Azure in SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [configurazione di pacemaker in SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure in SUSE Linux Enterprise Server con pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) e [disponibilità elevata di SAP Hana in macchine virtuali di Azure in SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) per la protezione avanzata del rilevamento del servizio di bilanciamento del carico di Azure
- Modifica la sezione e l'intestazione di e in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: rilascio di [SAP Hana con scalabilità orizzontale con nodo standby in macchine virtuali di Azure con Azure NetApp files su SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: correzione dei collegamenti interrotti nel [backup e nel ripristino](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: modificare il sistema operativo consigliato minimo da SLES 12 SP3 a SLES 12 SP4 in [disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure in SUSE Linux Enterprise Server con pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: modifiche alle configurazioni di archiviazione su disco Ultra e introduzione di e in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: modificare la grafica dei [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) per ottenere maggiore chiarezza
- 10/01/2019: modificare [le configurazioni e le operazioni di SAP Hana infrastruttura in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) per correggere le istruzioni sulla condivisione NFS a disponibilità elevata per/Hana/Shared. 
- 09/28/2019: modificare la [configurazione di pacemaker in Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) per chiarire SBD come meccanismo di schermatura non supportato nei cluster RHEL  
- 09/17/2019: modificare la guida alla pianificazione e distribuzione di NetWeaver per unificare i termini relativi all'estensione della macchina virtuale per SAP  
- 08/22/2019: modifiche apportate alla [configurazione di pacemaker in SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) per aggiornare gli URL per la creazione di un ruolo personalizzato  
- 08/16/2019: modifiche apportate alla [configurazione di pacemaker in Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) per ricordare ai clienti di aggiornare le azioni nel ruolo personalizzato, se aggiornate alla nuova versione dell'agente di recinzione di Azure  
- 08/15/2019: modifiche in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) per riflettere la disponibilità generale del disco Ultra (in precedenza ultra SSD)
- 08/01/2019: modifiche apportate alla [configurazione di pacemaker in SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) per integrare le modifiche in modo specifico per SLES 15 


