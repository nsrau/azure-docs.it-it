---
title: Certificazioni di Microsoft Azure per SAP | Microsoft Docs
description: Elenco aggiornato delle configurazioni e delle certificazioni SAP attualmente disponibili nella piattaforma Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/12/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: d1d2bd74a5118b48f7ed79a77ca2d7b6545607c6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072381"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configurazioni e certificazioni SAP in esecuzione su Microsoft Azure

SAP e Microsoft hanno una lunga storia di collaborazione che produce vantaggi per i reciproci clienti. Microsoft aggiorna costantemente la propria piattaforma e invia i dettagli di nuove certificazioni a SAP al fine di garantire la leadership di Microsoft Azure come piattaforma in cui eseguire carichi di lavoro SAP. Le tabelle seguenti illustrano le configurazioni supportate da Azure e un elenco sempre crescente di certificazioni SAP. 

## <a name="sap-hana-certifications"></a>Certificazioni SAP HANA
Riferimenti:

- [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) per il supporto di SAP HANA per le VM di Azure native e le istanze di grandi dimensioni HANA.

| Prodotto SAP | Sistema operativo supportato | Offerte Azure |
| --- | --- | --- |
| SAP HANA Developer Edition, che include il software HANA Client e i driver SQLODBC, ODBO (solo Windows), ODBC e JDBC, HANA Studio e il database HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Famiglia di VM serie D |
| Business One in HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilità controllata per GS5, M64s, M64ms, M128s, M128ms, SAP HANA in Azure (istanze di grandi dimensioni) |
| Suite su HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 per scenari non di produzione, M64s, M64ms, M128s, M128ms, SAP HANA in Azure (istanze di grandi dimensioni) |
| HANA Enterprise per BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA in Azure (istanze di grandi dimensioni) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA in Azure (istanze di grandi dimensioni) |

Finora tutte le macchine virtuali di Azure sono certificate per la scalabilità verticale SAP HANA.

## <a name="sap-netweaver-certifications"></a>Certificazioni SAP NetWeaver
Microsoft Azure è certificato per i seguenti prodotti SAP, con pieno supporto di Microsoft e SAP.
Riferimenti:

- [1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di Azure) per tutte le applicazioni basate su SAP NetWeaver, incluse SAP TREX, SAP LiveCache e SAP Content Server. Tutti i database, escluso SAP HANA.


| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, da M64s a M128ms |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, da M64s a M128ms |
| SAP BusinessObjects BI | Windows |N/D |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, da M64s a M128ms |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, da M64s a M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Altri carichi di lavoro SAP supportati in Azure

| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| SAP Business One in SQL Server | Windows  | SQL Server | Tutti i tipi di VM certificati per NetWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Tutti i tipi di VM certificati per NetWeaver<br /> Nota SAP n. 2451795 |
| Piattaforma di business intelligence SAP Business Objects | Windows e Linux | | Nota SAP n. 2145537 |
| SAP Data Services 4.2 | | | Nota SAP n. 2288344 |
| SAP Hybris Commerce Platform 5.x e 6.x | Windows | SQL Server, Oracle | Tutti i tipi di VM certificati per NetWeaver<br /> [Wiki di Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
