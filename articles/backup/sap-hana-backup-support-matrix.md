---
title: Matrice di supporto del backup di SAP HANA
description: In questo articolo vengono fornite informazioni sugli scenari e le limitazioni supportati quando si usa backup di Azure per eseguire il backup dei database SAP HANA nelle VM di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252441"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice di supporto per il backup di database di SAP HANA in VM di Azure

Backup di Azure supporta il backup dei database di SAP HANA in Azure. Questo articolo riepiloga gli scenari supportati e le limitazioni presenti quando si usa backup di Azure per eseguire il backup dei database SAP HANA nelle VM di Azure.

> [!NOTE]
> La frequenza di backup del log ora può essere impostata su un minimo di 15 minuti. Il flusso dei backup del log inizia solo dopo il completamento di un backup completo per il database.

## <a name="scenario-support"></a>Supporto degli scenari

| **Scenario**               | **Configurazioni supportate**                                | **Configurazioni non supportate**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA in esecuzione solo in macchine virtuali Linux di Azure                    | Istanze large di HANA (HLI)                                   |
| **GEOS**                   | **GA**<br> **Americhe** -Stati Uniti centrali, Stati Uniti orientali 2, Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti occidentali, Canada centrale, Canada orientale, Brasile <br> **Asia Pacifico** -Australia centrale, Australia centrale 2, Australia orientale, Australia sudorientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Asia orientale, Asia sudorientale, India centrale, India meridionale, India occidentale, Cina orientale, Cina settentrionale, Cina orientali 2, Cina settentrionale 2 <br> **Europa** -Europa occidentale, Europa settentrionale, Francia centrale, Regno Unito meridionale, Regno Unito occidentale, Germania settentrionale, Germania centro-occidentale, Svizzera settentrionale, Svizzera occidentale, Central Svizzera settentrionale <br> **Africa/me** -Sudafrica settentrionale, Sudafrica occidentale, Emirati Arabi Uniti settentrionali Emirati Arabi Uniti centrali  <BR>  **Aree di Azure per enti pubblici** | Francia meridionale, Germania centrale, Germania nord-orientale, US Gov IOWA |
| **Versioni del sistema operativo**            | SLES 12 con SP2, SP3 o SP4; SLES 15 con SP1                              | RHEL                                                |
| **Versioni di HANA**          | DSC su HANA 1. x, MDC su HANA 2. x <= SPS04 Rev 46       | -                                                            |
| **Distribuzioni HANA**       | SAP HANA su una singola macchina virtuale di Azure: solo scalabilità verticale. <br><br> Per le distribuzioni a disponibilità elevata, entrambi i nodi in due computer diversi vengono considerati come singoli nodi con catene di dati separate.               | Scalabilità orizzontale <br><br> Nelle distribuzioni a disponibilità elevata, il backup non esegue automaticamente il failover nel nodo secondario. La configurazione del backup deve essere eseguita separatamente per ogni nodo.                                           |
| **Istanze HANA**         | Una singola istanza di SAP HANA in una singola macchina virtuale di Azure: solo scalabilità verticale | Più istanze di SAP HANA in una singola macchina virtuale                  |
| **Tipi di database HANA**    | Contenitore di Database singolo (DSC) su 1. x, contenitore di più database (MDC) in 2. x | MDC in HANA 1. x                                              |
| **Dimensioni del database HANA**     | dimensioni del backup completo da 2 TB come indicato da HANA)                   |                                                              |
| **Tipi di backup**           | Backup completi, differenziali e del log                          | Incrementale, snapshot                                       |
| **Tipi di ripristino**          | Per informazioni sui tipi di ripristino supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |                                                              |
| **Limiti relativi a Backup**          | Fino a 2 TB di dimensioni del backup completo per ogni istanza di SAP HANA         |                                                              |
| **Configurazioni speciali** |                                                              | SAP HANA + suddivisione in livelli dinamica <br>  Clonazione tramite LaMa        |

------

> [!NOTE]
> Le operazioni di backup e ripristino da SAP HANA client nativi (SAP HANA Studio/pozzetto/DBA) non sono attualmente supportate.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire [il backup di database SAP Hana in esecuzione in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Informazioni su come [ripristinare SAP Hana database in esecuzione in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
