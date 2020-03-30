---
title: Matrice di supporto del backup di SAP HANA
description: In this article, learn about the supported scenarios and limitations when you use Azure backup to back up SAP HANA databases on Azure VMs.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252441"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice di supporto per il backup di database di SAP HANA in VM di Azure

Azure Backup supports the backup of SAP HANA databases to Azure. Questo articolo riepiloga gli scenari supportati e le limitazioni presenti quando si usa Backup di Azure per eseguire il backup dei database SAP HANA nelle macchine virtuali di Azure.This article summarizes the scenarios supported and limitations present when you use Azure Backup to back up SAP HANA databases on Azure VMs.

> [!NOTE]
> La frequenza del backup del log può ora essere impostata su un minimo di 15 minuti. I backup del log iniziano a scorrere solo dopo il completamento del backup completo del database.

## <a name="scenario-support"></a>Supporto degli scenari

| **Scenario**               | **Configurazioni supportate**                                | **Configurazioni non supportate**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA in esecuzione solo nelle macchine virtuali di Azure Linux                    | Istanze di grandi dimensioni HANA (HLI)                                   |
| **Geos**                   | **Ga:**<br> **Americhe** – Stati Uniti centrali, Stati Uniti orientali 2, Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti occidentali, Canada centrale, Canada orientale, Brasile sud <br> Asia Pacifico – Australia Centrale, Australia Centrale 2, Australia Est, Australia Sud-Est, Giappone Est, Giappone Ovest, Corea Centrale, Corea del Sud, Asia Orientale, Sud-Est **asiatico,** India centrale, India meridionale, India occidentale, Cina orientale, Cina nord, Cina orientale2, Cina settentrionale 2 <br> **Europa** – Europa occidentale, Europa occidentale, Francia centrale, Regno Unito Meridionale, Regno Unito Ovest, Germania Nord, Germania Centro Ovest, Svizzera Nord, Svizzera Ovest, Svizzera centrale Nord <br> **Africa / ME** - Sud Africa Nord, Sud Africa Ovest, Emirati Arabi Uniti Nord, Emirati Arabi Uniti Centrale  <BR>  **Aree di Azure per enti pubblici** | Francia Sud, Germania Centrale, Germania Nordest, USA Gov IOWA |
| **Versioni del sistema operativo**            | SLES 12 con SP2, SP3 o SP4; SLES 15 con SP1                              | RHEL                                                |
| **Versioni HANA**          | SDC su HANA 1.x, MDC su HANA 2.x <- SPS04 Rev 46       | -                                                            |
| **Distribuzioni HANA**       | SAP HANA in una singola macchina virtuale di Azure - Scalabilità solo. <br><br> Per le distribuzioni a disponibilità elevata, entrambi i nodi nei due computer diversi vengono considerati come singoli nodi con catene di dati separate.               | Scalabilità orizzontale <br><br> Nelle distribuzioni a disponibilità elevata, il backup non esegue automaticamente il failover nel nodo secondario. La configurazione del backup deve essere eseguita separatamente per ogni nodo.                                           |
| **Istanze HANA**         | Una singola istanza SAP HANA in una singola macchina virtuale di Azure: scalabilità verticale solo | Più istanze SAP HANA in una singola macchina virtualeMultiple SAP HANA instances on a single VM                  |
| **Tipi di database HANA**    | Singolo contenitore di database (SDC) ON 1.x, multi-database container (MDC) su 2.x | MDC in HANA 1.x                                              |
| **Dimensioni del database HANA**     | Dimensioni di backup completo di 2 TB come riportato da HANA)                   |                                                              |
| **Tipi di backup**           | Backup completi, differenziali e del log                          | Incrementale, Istantanee                                       |
| **Ripristinare i tipi**          | Fare riferimento alla nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) per informazioni sui tipi di ripristino supportati |                                                              |
| **Limiti relativi a Backup**          | Fino a 2 TB di dimensioni di backup complete per ogni istanza SAP HANA         |                                                              |
| **Configurazioni speciali** |                                                              | SAP HANA - Suddivisione in livelli dinamici <br>  Clonazione attraverso LaMa        |

------

> [!NOTE]
> Le operazioni di backup e ripristino da client nativi SAP HANA (SAP HANA Studio/ Cockpit/ DBA Cockpit) non sono attualmente supportate.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il backup dei database SAP HANA in esecuzione nelle macchine virtuali di AzureLearn how to [Backup SAP HANA databases running on Azure VMs](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Informazioni su come ripristinare i database SAP HANA in esecuzione nelle macchine virtuali di AzureLearn how to [Restore SAP HANA databases running on Azure VMs](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
