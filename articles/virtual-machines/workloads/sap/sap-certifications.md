---
title: Certificazioni di Microsoft Azure per SAP | Microsoft Docs
description: Elenco aggiornato delle configurazioni e delle certificazioni SAP attualmente disponibili nella piattaforma Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: c19471fee9235faffba12a12d9f92de77f60fd4d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770527"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configurazioni e certificazioni SAP in esecuzione su Microsoft Azure

SAP e Microsoft hanno una lunga storia di collaborazione che produce vantaggi per i reciproci clienti. Microsoft aggiorna costantemente la propria piattaforma e invia i dettagli di nuove certificazioni a SAP al fine di garantire la leadership di Microsoft Azure come piattaforma in cui eseguire carichi di lavoro SAP. Le tabelle seguenti illustrano le configurazioni supportate da Azure e un elenco sempre crescente di certificazioni SAP. Questo elenco è un elenco generale che potrebbe deviare qua e là dagli elenchi ufficiali SAP. Come accedere ai dati dettagliati è documentato nell'articolo [Quale software SAP è supportato per le distribuzioni](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) di AzureHow to get to the detailed data is documented in the article What SAP software is supported for Azure deployments

## <a name="sap-hana-certifications"></a>Certificazioni SAP HANA
Riferimenti:

- [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) per il supporto di SAP HANA per le VM di Azure native e le istanze di grandi dimensioni HANA.

| Prodotto SAP | Sistema operativo supportato | Offerte Azure |
| --- | --- | --- |
| SAP HANA Developer Edition, che include il software HANA Client e i driver SQLODBC, ODBO (solo Windows), ODBC e JDBC, HANA Studio e il database HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Famiglia di VM serie D |
| Business One in HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) (Piattaforme IaaS certificate per SAP HANA) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilità controllata per GS5. Pieno supporto per M64, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA in Azure (istanze Large) [Piattaforme IaaS certificate di SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite su HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA su Azure (grandi istanze) [Piattaforme IaaS certificate SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise per BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA su Azure (grandi istanze) [Piattaforme IaaS certificate SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA in Azure (istanze di grandi dimensioni)For, M416ms_v2, SAP HANA on Azure (Large instances) <br /> [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) |

Tenere presente che SAP usa il termine 'clustering' in [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) come sinonimo di 'scalabilità orizzontale' e NON come riferimento al 'clustering' a disponibilità elevata

## <a name="sap-netweaver-certifications"></a>Certificazioni SAP NetWeaver
Microsoft Azure è certificato per i seguenti prodotti SAP, con pieno supporto di Microsoft e SAP.
Riferimenti:

- [1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di Azure) per tutte le applicazioni basate su SAP NetWeaver, incluse SAP TREX, SAP LiveCache e SAP Content Server. Tutti i database, escluso SAP HANA.


| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, D2as_v4 per D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64ms, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416ms_v2 M416s_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, D2as_v4 per D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64ms, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416ms_v2 M416s_v2 |
| SAP BusinessObjects BI | WINDOWS |N/D |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, D2as_v4 per D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64ms, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416ms_v2 M416s_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, D2as_v4 per D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64ms, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416ms_v2 M416s_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Altri carichi di lavoro SAP supportati in Azure

| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| SAP Business One in SQL Server | Windows  | SQL Server | Tutti i tipi di VM certificati per NetWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Tutti i tipi di VM certificati per NetWeaver<br /> Nota SAP n. 2451795 |
| Piattaforma di business intelligence SAP Business Objects | Windows e Linux | | Nota SAP n. 2145537 |
| SAP Data Services 4.2 | | | Nota SAP n. 2288344 |
| Piattaforma SAP Hybris Commerce  | WINDOWS | SQL Server, Oracle | Tutti i tipi di VM certificati per NetWeaver <br /> [Wiki di Hybris](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| Piattaforma SAP Hybris Commerce  | SLES 12 o più recenti | SAP HANA | Tutti i tipi di VM certificati per NetWeaver <br /> [Wiki di Hybris](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| Piattaforma SAP Hybris Commerce  | RHEL 7 o più recente | SAP HANA | Tutti i tipi di VM certificati per NetWeaver <br /> [Wiki di Hybris](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| SAP (Hybris) Commerce Platform 1811 e versioni successive  | Windows, SLES o RHEL | DATABASE di SQL Azure | Tutti i tipi di VM certificati per NetWeaver <br /> [Documentazione di Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
