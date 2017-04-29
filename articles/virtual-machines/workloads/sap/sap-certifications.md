---
title: Certificazioni di Microsoft Azure per SAP | Microsoft Docs
description: Elenco aggiornato delle configurazioni e delle certificazioni SAP attualmente disponibili nella piattaforma Azure.
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8925de446b9dc7e17a2f2033426eb380329c8249
ms.lasthandoff: 04/03/2017


---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configurazioni e certificazioni SAP in esecuzione su Microsoft Azure

SAP e Microsoft hanno una lunga storia di collaborazione che produce vantaggi per i reciproci clienti. Microsoft aggiorna costantemente la propria piattaforma e invia i dettagli di nuove certificazioni a SAP al fine di garantire la leadership di Microsoft Azure come piattaforma in cui eseguire carichi di lavoro SAP. Le tabelle seguenti illustrano le configurazioni supportate e un elenco sempre crescente di certificazioni. 

## <a name="sap-hana-certifications"></a>Certificazioni SAP HANA

| Prodotto SAP | Sistema operativo supportato | Offerte Azure |
| --- | --- | --- |
| SAP HANA Developer Edition, che include il software HANA Client e i driver SQLODBC, ODBO (solo Windows), ODBC e JDBC, HANA Studio e il database HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise | Famiglia di VM serie D |
| HANA One |Red Hat Enterprise Linux, SUSE Linux Enterprise |DS14_v2 (in seguito alla disponibilità generale) |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |Disponibilità controllata per GS5, SAP HANA in Azure (istanze di grandi dimensioni) |
| Suite su HANA, OLTP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 per distribuzioni a nodo singolo, per scenari non di produzione, SAP HANA in Azure (istanze di grandi dimensioni) |
| HANA Enterprise per BW, OLAP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 per distribuzioni a nodo singolo, SAP HANA in Azure (istanze di grandi dimensioni) |
| SAP BW/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 per distribuzioni a nodo singolo, SAP HANA in Azure (istanze di grandi dimensioni) |

## <a name="sap-netweaver-certifications"></a>Certificazioni SAP NetWeaver
Microsoft Azure è certificato per i seguenti prodotti SAP, con pieno supporto di Microsoft e SAP.

| Prodotto SAP | Sistema operativo guest | RDBMS | Tipi di macchine virtuali |
| --- | --- | --- | --- |
| Software SAP Business Suite |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows), DB2, SAP ASE |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5 |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows), DB2, SAP ASE |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5 |
| SAP BusinessObjects BI |Windows |N/D |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5 |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows), DB2, SAP ASE |Da A5 ad A11, da D11 a D14, da DS11 a DS14, da DS11_v2 a DS15_v2, da GS1 a GS5 |

