---
title: Matrice di supporto del backup di SAP HANA
description: In questo articolo vengono fornite informazioni sugli scenari e le limitazioni supportati quando si usa backup di Azure per eseguire il backup dei database SAP HANA nelle VM di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: e3bfc5ab9a91ae3aee73d7ed24161acae60211ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022327"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice di supporto per il backup di database di SAP HANA in VM di Azure

Backup di Azure supporta il backup dei database SAP HANA in Azure. In questo articolo vengono riepilogati gli scenari supportati e i limiti relativi all'uso di Backup di Azure per eseguire il backup di database SAP HANA in VM di Azure.

> [!NOTE]
> La frequenza di backup del log ora può essere impostata su un minimo di 15 minuti. I backup del log iniziano a fluire solo dopo il termine di un backup completo del database.

## <a name="scenario-support"></a>Supporto degli scenari

| **Scenario**               | **Configurazioni supportate**                                | **Configurazioni non supportate**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA in esecuzione solo in VM Linux di Azure                    | Istanze Large di HANA (HLI, Hana Large Instances)                                   |
| **Aree**                   | **Area geografica:**<br> **Americhe**: Stati Uniti centrali, Stati uniti orientali 2, Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti occidentali, Canada centrale, Canada orientale, Brasile meridionale <br> **Asia Pacifico**: Australia centrale, Australia centrale 2, Australia orientale, Australia sud-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Asia orientale, Asia sud-orientale, India centrale, India meridionale, India occidentale, Cina orientale, Cina settentrionale, Cina orientale 2, Cina settentrionale 2 <br> **Europa** -Europa occidentale, Europa settentrionale, Francia centrale, Regno Unito meridionale, Regno Unito occidentale, Germania settentrionale, Germania centro-occidentale, Svizzera settentrionale Svizzera occidentale, Svizzera settentrionale centrale, Norvegia orientale, Norvegia occidentale <br> **Africa/Medio Oriente**: Sudafrica settentrionale, Sudafrica occidentale, Emirati Arabi settentrionali, Emirati Arabi centrali  <BR>  **Aree di Azure per enti pubblici** | Francia meridionale, Germania centrale, Germania nord-orientale, US Gov IOWA |
| **Versioni del sistema operativo**            | SLES 12 con SP2, SP3 e SP4; SLES 15 con SP0 e SP1 <br><br>  A partire dal 1° agosto 2020, il backup di SAP HANA per RHEL (7.4, 7.6, 7.7 e 8.1) è disponibile a livello generale.                |                                             |
| **Versioni di HANA**          | DSC su HANA 1. x, MDC su HANA 2. x <= SPS04 Rev 48, SPS05 (ancora da convalidare per gli scenari di crittografia abilitata)      |                                                            |
| **Distribuzioni HANA**       | SAP HANA su una singola VM di Azure: solo scalabilità verticale. <br><br> Per le distribuzioni a disponibilità elevata, entrambi i nodi in due computer diversi vengono considerati come nodi singoli con catene di dati separate.               | Scalabilità orizzontale <br><br> Nelle distribuzioni a disponibilità elevata, il backup non esegue automaticamente il failover nel nodo secondario. La configurazione del backup deve essere eseguita separatamente per ogni nodo.                                           |
| **Istanze HANA**         | Una singola istanza SAP HANA in una singola VM di Azure: solo scalabilità verticale | Più istanze SAP HANA in una singola VM                  |
| **Tipi di database HANA**    | Contenitore di database singolo (SDC) in 1.x, contenitore di più database (MDC) in 2.x | MDC in HANA 1.x                                              |
| **Dimensioni database HANA**     | Database HANA di dimensioni <= 2 TB (questa non è la dimensione della memoria del sistema HANA)               |                                                              |
| **Tipi di backup**           | Backup completi, differenziali e del log                          | Incrementale, snapshot                                       |
| **Tipi di ripristino**          | Per informazioni sui tipi di ripristino supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) |                                                              |
| **Limiti di backup**          | Fino a 2 TB di dimensioni del backup completo per ogni istanza di SAP HANA         |                                                              |
| **Configurazioni speciali** |                                                              | SAP HANA + suddivisione in livelli dinamica <br>  Clonazione tramite LaMa        |

------

>[!NOTE]
>Backup di Azure non si adatta automaticamente al cambiamento dell'ora legale per il backup di un database SAP HANA in esecuzione in una VM di Azure.
>
>Modificare manualmente i criteri in base alle esigenze.

> [!NOTE]
> È ora possibile [monitorare i processi di backup e ripristino](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) (nello stesso computer) generati da Hana native clients (SAP Hana Studio/Cockpit/DBA Cockpit) nell'portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire [il backup di database SAP Hana in esecuzione in macchine virtuali di Azure](./backup-azure-sap-hana-database.md)
* Informazioni su come [ripristinare i database SAP HANA in esecuzione nelle VM di Azure](./sap-hana-db-restore.md)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md)
