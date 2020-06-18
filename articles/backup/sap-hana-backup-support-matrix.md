---
title: Matrice di supporto del backup di SAP HANA
description: In questo articolo sono incluse informazioni sugli scenari supportati e sui limiti relativi all'uso di Backup di Azure per eseguire il backup di database SAP HANA in VM di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 5dcbfa67ce69a3dad1c263427ea3a0e34e8cef18
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747344"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice di supporto per il backup di database di SAP HANA in VM di Azure

Backup di Azure supporta il backup dei database SAP HANA in Azure. In questo articolo vengono riepilogati gli scenari supportati e i limiti relativi all'uso di Backup di Azure per eseguire il backup di database SAP HANA in VM di Azure.

> [!NOTE]
> La frequenza di backup del log ora può essere impostata su un minimo di 15 minuti. I backup del log iniziano a fluire solo dopo il termine di un backup completo del database.

## <a name="scenario-support"></a>Supporto degli scenari

| **Scenario**               | **Configurazioni supportate**                                | **Configurazioni non supportate**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA in esecuzione solo in VM Linux di Azure                    | Istanze Large di HANA (HLI, Hana Large Instances)                                   |
| **Geo**                   | **Area geografica:**<br> **Americhe**: Stati Uniti centrali, Stati uniti orientali 2, Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti occidentali, Canada centrale, Canada orientale, Brasile meridionale <br> **Asia Pacifico**: Australia centrale, Australia centrale 2, Australia orientale, Australia sud-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Asia orientale, Asia sud-orientale, India centrale, India meridionale, India occidentale, Cina orientale, Cina settentrionale, Cina orientale 2, Cina settentrionale 2 <br> **Europa**: Europa occidentale, Europa settentrionale, Francia centrale, Regno Unito meridionale, Regno Unito occidentale, Germania settentrionale, Germania centro-occidentale, Svizzera settentrionale, Svizzera occidentale, Svizzera centro-settentrionale <br> **Africa/Medio Oriente**: Sudafrica settentrionale, Sudafrica occidentale, Emirati Arabi settentrionali, Emirati Arabi centrali  <BR>  **Aree di Azure per enti pubblici** | Francia meridionale, Germania centrale, Germania nord-orientale, US Gov IOWA |
| **Versioni del sistema operativo**            | SLES 12 con SP2, SP3 o SP4; SLES 15 con SP1                              | RHEL                                                |
| **Versioni di HANA**          | SDC su HANA 1.x, MDC su HANA 2.x < = SPS04 Rev 46       | -                                                            |
| **Distribuzioni HANA**       | SAP HANA su una singola VM di Azure: solo scalabilità verticale. <br><br> Per le distribuzioni a disponibilità elevata, entrambi i nodi in due computer diversi vengono considerati come nodi singoli con catene di dati separate.               | Scalabilità orizzontale <br><br> Nelle distribuzioni a disponibilità elevata, il backup non esegue automaticamente il failover nel nodo secondario. La configurazione del backup deve essere eseguita separatamente per ogni nodo.                                           |
| **Istanze HANA**         | Una singola istanza SAP HANA in una singola VM di Azure: solo scalabilità verticale | Più istanze SAP HANA in una singola VM                  |
| **Tipi di database HANA**    | Contenitore di database singolo (SDC) in 1.x, contenitore di più database (MDC) in 2.x | MDC in HANA 1.x                                              |
| **Dimensioni database HANA**     | Dimensioni del backup completo da 2 TB come indicato da HANA                   |                                                              |
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
> È ora possibile [monitorare i processi di backup e ripristino](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) nello stesso computer generati da client nativi HANA (SAP HANA Studio/pannello di controllo/pannello di controllo DBA) nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire il backup di database SAP HANA in esecuzione in VM di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Informazioni su come [ripristinare i database SAP HANA in esecuzione in VM di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
