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
ms.date: 11/06/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: de282041066e18a52d596f051def598eef3d458d
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287218"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configurazioni e certificazioni SAP in esecuzione su Microsoft Azure

SAP e Microsoft hanno una lunga storia di collaborazione che produce vantaggi per i reciproci clienti. Microsoft aggiorna costantemente la propria piattaforma e invia i dettagli di nuove certificazioni a SAP al fine di garantire la leadership di Microsoft Azure come piattaforma in cui eseguire carichi di lavoro SAP. Le tabelle seguenti illustrano le configurazioni supportate da Azure e un elenco sempre crescente di certificazioni SAP. 

## <a name="sap-hana-certifications"></a>Certificazioni SAP HANA
Riferimenti:

- [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) per il supporto di SAP HANA per le VM di Azure native e le istanze di grandi dimensioni HANA.

| Prodotto SAP | Sistema operativo supportato | Offerte Azure |
| --- | --- | --- |
| SAP HANA Developer Edition, che include il software HANA Client e i driver SQLODBC, ODBO (solo Windows), ODBC e JDBC, HANA Studio e il database HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Famiglia di VM serie D |
| Business One in HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) (Piattaforme IaaS certificate per SAP HANA) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilità controllata per GS5. Supporto completo per M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> SAP HANA in Azure (istanze Large) [Piattaforme IaaS certificate di SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite su HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, SAP HANA in Azure (istanze Large) <br /> [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) |
| HANA Enterprise per BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> SAP HANA in Azure (istanze Large) [Piattaforme IaaS certificate di SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> SAP HANA in Azure (istanze di grandi dimensioni) <br /> [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) |

Tenere presente che SAP usa il termine 'clustering' in [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA) come sinonimo di 'scalabilità orizzontale' e NON come riferimento al 'clustering' a disponibilità elevata

## <a name="sap-netweaver-certifications"></a>Certificazioni SAP NetWeaver
Microsoft Azure è certificato per i seguenti prodotti SAP, con pieno supporto di Microsoft e SAP.
Riferimenti:

- [1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di Azure) per tutte le applicazioni basate su SAP NetWeaver, incluse SAP TREX, SAP LiveCache e SAP Content Server. Tutti i database, escluso SAP HANA.


| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP BusinessObjects BI |  Windows |N/D |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows e Oracle Linux), DB2, SAP ASE |Da A5 a A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5, da D2s_v3 a D64s_v3, da E2s_v3 a E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |

## <a name="other-sap-workload-supported-on-azure"></a>Altri carichi di lavoro SAP supportati in Azure

| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| SAP Business One in SQL Server |  Windows  | SQL Server | Tutti i tipi di VM certificati per NetWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Tutti i tipi di VM certificati per NetWeaver<br /> Nota SAP n. 2451795 |
| Piattaforma di business intelligence SAP Business Objects | Windows e Linux | | Nota SAP n. 2145537 |
| SAP Data Services 4.2 | | | Nota SAP n. 2288344 |
| SAP Hybris Commerce Platform 5.x e 6.x |  Windows | SQL Server, Oracle | Tutti i tipi di VM certificati per NetWeaver<br /> [Wiki di Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
