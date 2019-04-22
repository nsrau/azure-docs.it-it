---
title: Introduzione a SAP nelle VM di Azure | Microsoft Docs
description: Informazioni sulle soluzioni SAP in esecuzione su macchine virtuali (VM) in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698510"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Usare Azure per ospitare ed eseguire scenari di carico di lavoro SAP

Quando si usa Microsoft Azure, è possibile eseguire in modo affidabile i carichi di lavoro SAP e gli scenari critici su una piattaforma scalabile, conforme e comprovata. Ottenere la scalabilità, flessibilità e risparmi di Azure. Grazie alla partnership estesa tra Microsoft e SAP, è possibile eseguire applicazioni SAP in scenari di sviluppo e test e produzione in Azure e completamente supportato. Da SAP NetWeaver a SAP S/4 Hana, SAP BI in Linux per Windows e SAP HANA a SQL, abbiamo per te.

Oltre all'hosting di scenari SAP NetWeaver con diversi DBMS in Azure, è possibile ospitare altri scenari di carico di lavoro SAP, come SAP BI in Azure. 

L'univocità di Azure per SAP HANA è un'offerta che imposta Azure distanti tra loro. Per abilitare l'hosting di più memoria e CPU impegnativi in termini di risorse SAP scenari che coinvolgono SAP HANA, Azure offre l'uso dell'hardware bare metal dedicato al cliente. Usare questa soluzione per eseguire le distribuzioni di SAP HANA che richiedono fino a 24 TB (120 TB scale-out) di memoria per S/4 Hana o altro carico di lavoro SAP HANA. 

Hosting di scenari di carico di lavoro SAP in Azure anche possibile creare i requisiti di integrazione delle identità e accesso single sign-on. Questa situazione può verificarsi quando si usa Azure Active Directory (Azure AD) per connettere vari componenti SAP e SAP software-as-a-service (SaaS) o offerte di platform-as-a-service (PaaS). Un elenco di scenari single sign-on con Azure AD e l'integrazione entità SAP viene descritto e illustrato nella sezione "integrazione delle identità AAD SAP e single sign-on"

## <a name="latest-changes"></a>Modifiche più recenti

- Versione di [controllare istanze Large di HANA di Azure tramite il portale di Azure](hana-li-portal.md)

- Versione di [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure su SUSE Linux Enterprise Server con file di NetApp Azure for SAP Applications](high-availability-guide-suse-netapp-files.md)

- Chiarimenti sui **del sistema operativo Linux parametro net.ipv4.tcp_timestamps** bilanciamento del carico di impostazioni insieme a un'istanza di Azure

- Versione di [configurazioni del carico di lavoro SAP con le zone di disponibilità di Azure](sap-ha-availability-zones.md)

- Pubblicazione di [Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA in Azure (istanze Large)

Una serie di documenti illustra SAP HANA in Azure (istanze Large) o in breve, istanze Large di HANA. Per informazioni sulle aree seguenti di istanze Large di HANA, vedere:

- [Panoramica di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architettura di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruttura e connettività a SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installare SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Ripristino di emergenza e disponibilità elevato di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Risolvere i problemi e monitorare SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Passaggi successivi:

- Lettura [panoramica e architettura di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA in macchine virtuali di Azure
Questa sezione della documentazione presenta diversi aspetti relativi a SAP HANA. Come prerequisito, è necessario conoscere i principali servizi di Azure che forniscono servizi elementari di IaaS di Azure. È quindi necessario conoscenza di calcolo di Azure, archiviazione e rete. Molti di questi argomenti vengono gestiti in correlati di SAP NetWeaver [Guida alla pianificazione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Per informazioni su HANA in Azure, vedere gli articoli seguenti e loro subarticles:

- [Guida introduttiva: Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Distribuire SAP S/4HANA o BW/4HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilità di SAP HANA in un'area di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilità di SAP HANA tra aree di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Disponibilità elevata di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup di SAP HANA di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Backup di SAP HANA basato su snapshot di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver distribuito in macchine virtuali di Azure
Questa sezione elenca la documentazione di pianificazione e distribuzione per SAP NetWeaver e Business One in Azure. La documentazione illustra le nozioni di base e l'utilizzo di database HANA non con un carico di lavoro SAP in Azure. Gli articoli per la disponibilità elevata e i documenti sono anche la base per la disponibilità elevata di HANA in Azure, ad esempio:

- [SAP Business One in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Distribuire SAP IDES EHP7 SP3 per SAP ERP 6.0 in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Eseguire SAP NetWeaver in VM SUSE Linux di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Connettore SAP LaMa per Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Per informazioni sui database di HANA non sotto un carico di lavoro SAP in Azure, vedere:

- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Distribuzione DBMS per SQL Server di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Distribuzione di SAP MaxDB, la Cache in tempo reale e Server di contenuti nelle VM di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Per informazioni sui database di SAP HANA in Azure, vedere la sezione "SAP HANA in macchine virtuali di Azure".

Per informazioni sulla disponibilità elevata di un carico di lavoro SAP in Azure, vedere:

- [Disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Questo documento fa riferimento a vari altri documenti di scenario e architettura. Nei documenti di scenario più avanti vengono forniti collegamenti a documenti tecnici dettagliati che spiegano la distribuzione e la configurazione dei diversi metodi a disponibilità elevata. I vari documenti che illustrano come creare e configurare la disponibilità elevata per un carico di lavoro di SAP NetWeaver riguardano i sistemi operativi Linux e Windows.


Per informazioni sull'integrazione tra Azure Active Directory (Azure AD) e servizi SAP e single sign-on, vedere:

- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [L'ambiente di S/4 Hana: Fiori Launchpad SAML single sign-on con Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Per informazioni sull'integrazione dei servizi di Azure in componenti SAP, vedere:

- [Usare SAP HANA in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usare il connettore SAP BW in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory offre l'integrazione di dati SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




