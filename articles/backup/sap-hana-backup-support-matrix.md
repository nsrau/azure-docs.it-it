---
title: Matrice di supporto del backup di SAP HANA
description: In questo articolo vengono fornite informazioni sugli scenari e le limitazioni supportati quando si usa backup di Azure per eseguire il backup dei database SAP HANA nelle VM di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 0d847d2131a9a9c21fde14cae40a184de4195223
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288230"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice di supporto per il backup di database di SAP HANA in macchine virtuali di Azure

Backup di Azure supporta il backup dei database di SAP HANA in Azure. Questo articolo riepiloga gli scenari supportati e le limitazioni presenti quando si usa backup di Azure per eseguire il backup dei database SAP HANA nelle VM di Azure.

## <a name="onboard-to-the-public-preview"></a>Eseguire l'onboarding nell'anteprima pubblica

Eseguire l'onboarding nell'anteprima pubblica come indicato di seguito:

* Nel portale, registrare l'ID sottoscrizione al provider di servizi di servizi di ripristino [seguendo questo articolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Per PowerShell, eseguire questo cmdlet. Deve essere completato come "registrato".

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> La frequenza di backup del log ora può essere impostata su un minimo di 15 minuti. Il flusso dei backup del log inizia solo dopo il completamento di un backup completo per il database.

## <a name="scenario-support"></a>Supporto degli scenari

| **Scenario**               | **Configurazioni supportate**                                | **Configurazioni non supportate**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA in esecuzione solo in macchine virtuali Linux di Azure                    | Istanze large di HANA (HLI)                                   |
| **GEOS**                   | Australia sud-orientale, Australia orientale, Brasile meridionale, Canada centrale, Canada orientale Asia orientale, Asia orientale Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-occidentali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-settentrionali, Stati Uniti centrali, Stati Uniti centro-meridionali, India meridionale, Giappone orientale, Giappone St Korea Central, Corea del Sud settentrionale, Europa occidentale Regno Unito meridionale, Regno Unito occidentale | Australia centrale, Australia centrale 2 Cina orientale, Cina settentrionale, Cina orientali 2, Cina settentrionale 2 India occidentale Francia centrale, Francia meridionale Germania settentrionale, Germania centro-occidentale Svizzera settentrionale, Svizzera occidentale Sud Africa settentrionale, Sudafrica occidentale, Emirati Arabi Uniti settentrionali, UAE centra l aree di Azure per enti pubblici |
| **Versioni del sistema operativo**            | SLES 12 con SP2, SP3 o SP4           | SLES 15, RHEL                                                |
| **Versioni di HANA**          | DSC su HANA 1. x, MDC su HANA 2. x < = SPS04 Rev 44           | -                                                            |
| **Distribuzioni HANA**       | SAP HANA su una singola macchina virtuale di Azure: solo scalabilità verticale               | Scalabilità orizzontale                                                    |
| **Istanze HANA**         | Una singola istanza di SAP HANA in una singola macchina virtuale di Azure: solo scalabilità verticale | Più istanze di SAP HANA in una singola macchina virtuale                  |
| **Tipi di database HANA**    | Contenitore di Database singolo (DSC) su 1. x, contenitore di più database (MDC) in 2. x | MDC in HANA 1. x                                              |
| **Dimensioni del database HANA**     | dimensioni del backup completo da 2 TB dopo la compressione (serie M 2 TB, 4 TB di RAM) |                                                              |
| **Tipi di backup**           | Backup completi, differenziali e del log                           | Incrementale, snapshot                                       |
| **Tipi di ripristino**          | Per informazioni sui tipi di ripristino supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |                                                              |
| **Limiti di backup**          | Fino a 2 TB di dimensioni del backup completo per ogni istanza di SAP HANA  |                                                              |
| **Configurazioni speciali** |                                                              | SAP HANA + suddivisione in livelli dinamica <br>  Clonazione tramite LaMa            |

------

> [!NOTE]
> Le operazioni di backup e ripristino da SAP HANA client nativi (SAP HANA Studio/pozzetto/DBA) non sono attualmente supportate.



## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire [il backup di database SAP Hana in esecuzione in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Informazioni su come [ripristinare SAP Hana database in esecuzione in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Informazioni su come [gestire SAP Hana database di cui viene eseguito il backup con backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni durante il backup SAP Hana database](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
