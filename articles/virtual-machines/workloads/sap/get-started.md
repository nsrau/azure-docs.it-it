---
title: Introduzione alle soluzioni SAP in macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle soluzioni SAP in esecuzione sulle macchine virtuali (VM) in Microsoft Azure
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
ms.date: 01/30/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0988a6367eb1a1bacaab61f8bc5924a2c9f0a58c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474718"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Utilizzo di Azure per l'hosting e l'esecuzione di scenari con carichi di lavoro SAP

Scegliendo Microsoft Azure, sarà possibile eseguire in modo affidabile gli scenari e i carichi di lavoro SAP mission critical in una piattaforma scalabile, conforme e comprovata per le aziende,  nonché ottenere la scalabilità, la flessibilità e la convenienza di Azure. Grazie alla partnership estesa tra Microsoft e SAP, è possibile eseguire applicazioni SAP in scenari di sviluppo/test e di produzione in Azure con il supporto completo. Da SAP NetWeaver a SAP S4/HANA, SAP BI, da Linux a Windows, da SAP HANA a SQL, viene offerto supporto per tutte le soluzioni.

Oltre all'hosting di scenari SAP NetWeaver con diversi DBMS in Azure, è possibile eseguire l'hosting di diversi altri scenari di carico di lavoro SAP, come SAP BI in Azure. 

L'univocità di Azure per SAP HANA è rappresentata da un'offerta che distingue Azure dalla concorrenza. Per abilitare l'hosting di scenari SAP complessi con SAP HANA che richiedono più risorse della CPU e della memoria, Azure offre l'utilizzo di hardware bare metal dedicato al cliente per eseguire distribuzioni SAP HANA che richiedono fino a 24 TB (scalabilità orizzontale di 120 TB) di memoria per S/4HANA o un altro carico di lavoro SAP HANA. Questa esclusiva soluzione Azure di SAP HANA in Azure (istanze di grandi dimensioni) consente di eseguire SAP HANA su hardware bare metal dedicato con il livello dell'applicazione SAP o il livello del middleware dei carichi di lavoro ospitato nelle macchine virtuali di Azure native. Questa soluzione è descritta in diversi documenti nella sezione "SAP HANA in Azure (istanze di grandi dimensioni)".   

L'hosting di scenari di carichi di lavoro SAP in Azure consente anche di creare i requisiti di integrazione della soluzione di gestione delle identità e Single Sign-On usando Azure Active Directory in vari componenti SAP e offerte SAP di tipo SaaS o PaaS. La sezione sull'integrazione della soluzione di gestione delle identità e Single Sign-On SAP AAD descrive e illustra un elenco di questi scenari di integrazione e Single Sign-On con Azure Active Directory (AAD) ed entità SAP.

## <a name="latest-changes"></a>Modifiche più recenti

Pubblicazione di [Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP](sap-deployment-checklist.md)

Chiarimento in merito all'utilizzo e alla configurazione di [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nei percorsi di comunicazione critici per la latenza dei sistemi SAP in:

- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)



## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA in SAP HANA in Azure (istanze di grandi dimensioni)

Una serie di documenti offre una presentazione di SAP HANA in Azure (istanze Large) o, più brevemente, HANA in istanze Large. I documenti trattano i seguenti argomenti relativi ad HANA in istanze Large:

- [Panoramica di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architettura di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruttura e connettività a SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installare SAP HANA in SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Disponibilità elevata e ripristino di emergenza di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Risoluzione dei problemi e monitoraggio di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Passaggi successivi:

- Leggere [Panoramica e architettura di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA nelle macchine virtuali di Azure
Questa sezione della documentazione presenta diversi aspetti relativi a SAP HANA. Come prerequisito, è necessario conoscere i principali servizi di Azure che forniscono servizi IaaS di base e quindi principalmente i servizi di calcolo, archiviazione e rete in Azure. Molti di questi argomenti vengono trattati nella [Guida alla pianificazione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) correlata a SAP NetWeaver. 

La documentazione specifica per HANA in Azure è costituita dagli articoli elencati di seguito e dagli articoli secondari correlati:

- [Avvio rapido: Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
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
Questa sezione include la documentazione relativa alla pianificazione e alla distribuzione per SAP NetWeaver e Business One in Azure. La documentazione in questo capitolo è incentrata principalmente sulle nozioni di base e sull'utilizzo di database non HANA con carico di lavoro SAP in Azure, mentre i documenti e gli articoli relativi alla disponibilità elevata offrono informazioni di base anche per la disponibilità elevata di HANA in Azure. Sono disponibili gli articoli seguenti:

- [SAP Business One nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Distribuire SAP IDES EHP7 SP3 per SAP ERP 6.0 in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Connettore SAP LaMa per Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Per i database non HANA sottoposti a un carico di lavoro SAP in Azure sono disponibili i documenti seguenti:

- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Distribuzione DBMS per SQL Server di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Distribuzione di SAP MaxDB, SAP liveCache e SAP Content Server in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Per i database SAP HANA in Azure, controllare la sezione SAP HANA in macchine virtuali di Azure.

Per la disponibilità elevata di un carico di lavoro SAP in Azure il documento di base è:

- [Disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Questo documento include collegamenti ad altri articoli relativi all'architettura e ai possibili scenari. Questi ultimi offrono a loro volta collegamenti a documenti tecnici dettagliati che illustrano la distribuzione e la configurazione dei diversi metodi per la disponibilità elevata. I vari documenti relativi alla definizione e alla configurazione della disponibilità elevata per un carico di lavoro SAP NetWeaver interessano i sistemi operativi sia Linux che Windows.


Per l'integrazione di Azure Active Directory (AAD) con i servizi SAP e l'accesso Single Sign-On, sono disponibili i documenti seguenti:

- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Esercitazione: Integrazione di Azure Active Directory con SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Your S/4HANA environment – Fiori Launchpad SAML Single Sign-On with Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/) (Ambiente S/4HANA: accesso Single Sign-On SAML a SAP Fiori Launchpad con Azure AD)

Per l'integrazione dei servizi di Azure con i componenti SAP, sono disponibili i documenti seguenti:

- [Usare SAP HANA in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usare il connettore SAP BW in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory offre l'integrazione di dati SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




