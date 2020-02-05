---
title: Panoramica del backup offline
description: Informazioni sui vari componenti del backup non in linea, tra cui il backup offline basato su Azure Data Box e il backup offline basato su importazione/esportazione di Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027017"
---
# <a name="overview-of-offline-backup"></a>Panoramica del backup offline

Questo articolo fornisce una panoramica del backup offline.

I backup completi iniziali in Azure trasferiscono in genere grandi quantità di dati online e richiedono una maggiore larghezza di banda di rete rispetto ai backup successivi che trasferiscono solo le modifiche incrementali. Gli uffici remoti o i Data Center in determinate aree geografiche non hanno sempre una larghezza di banda di rete sufficiente. Pertanto, questi backup iniziali impiegano diversi giorni e, durante questo periodo di tempo, usano continuamente la stessa rete di cui è stato eseguito il provisioning per le applicazioni in esecuzione nel Data Center locale.

Backup di Azure supporta il "backup non in linea", che consente di trasferire i dati di backup iniziali offline senza usare la larghezza di banda di rete. Fornisce un meccanismo per copiare i dati di backup in dispositivi di archiviazione fisici che vengono poi spediti a un Data Center di Azure nelle vicinanze e caricati in un insieme di credenziali di servizi di ripristino. Questo processo garantisce un trasferimento affidabile dei dati di backup senza utilizzare una larghezza di banda di rete.

## <a name="offline-backup-options"></a>Opzioni di backup offline

Offline: il backup è disponibile in due modalità in base alla proprietà dei dispositivi di archiviazione.

1. Backup offline basato su Azure Data Box (anteprima)
2. Backup offline basato su importazione/esportazione di Azure

## <a name="azure-data-box-based-offline-backup-preview"></a>Backup offline basato su Azure Data Box (anteprima)

Questa modalità è attualmente supportata con l'agente MARS, in anteprima. Questa opzione consente di sfruttare i vantaggi offerti dal [servizio Azure Data Box](https://azure.microsoft.com/services/databox/) per fornire appliance di trasferimento Microsoft, sicure e resistenti alle manomissioni con connettori USB, al Data Center o all'ufficio remoto e i dati di backup vengono scritti direttamente su questi dispositivi. **Questa opzione consente di risparmiare il lavoro richiesto per ottenere i propri dischi e connettori compatibili con Azure o per eseguire il provisioning di archiviazione temporanea come percorso di gestione temporanea.** Microsoft gestisce inoltre la logistica per il trasferimento end-to-end, che è possibile monitorare tramite il portale di Azure. Di seguito è illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura Data Box di backup di Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Di seguito è riportato un riepilogo dell'architettura:

1. Backup di Azure copia direttamente i dati di backup in questi dispositivi preconfigurati.
2. È quindi possibile rispedire questi dispositivi a un Data Center di Azure.
3. Il servizio Azure Data Box copia i dati in un account di archiviazione di proprietà del cliente.
4. Backup di Azure copia automaticamente i dati di backup dall'account di archiviazione all'insieme di credenziali dei servizi di ripristino designato e i backup online incrementali vengono pianificati.

Per usare Azure Data Box backup offline basato su, fare riferimento a [questo articolo](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Backup offline basato su importazione/esportazione di Azure

Questa opzione è supportata dall'agente di server di Backup di Azure (MAB)/DPM-A/MARS. Sfrutta il [servizio importazione/esportazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) che consente di trasferire i dati di backup iniziali in Azure usando dischi e connettori compatibili con Azure. Questo approccio richiede il provisioning di un'archiviazione temporanea nota come **posizione di gestione temporanea** e l'utilizzo di utilità predefinite per formattare e copiare i dati di backup nei dischi di proprietà del cliente. Di seguito è illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione:

![Architettura di importazione/esportazione di backup di Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Di seguito è riportato un riepilogo dell'architettura:

1. Anziché inviare i dati di backup in rete, backup di Azure scrive i dati di backup in un percorso di gestione temporanea.
2. I dati nel percorso di gestione temporanea vengono scritti in uno o più dischi SATA usando un'utilità personalizzata.
3. Come parte del lavoro preparatorio, l'utilità crea un processo di importazione di Azure. Le unità SATA vengono spedite al Data Center di Azure più vicino e fanno riferimento al processo di importazione per connettere le attività.
4. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
5. Backup di Azure copia i dati di backup dall'account di archiviazione nell'insieme di credenziali di Servizi di ripristino e pianifica i backup incrementali.

Per usare il backup offline basato su importazione/esportazione di Azure con l'agente MARS, vedere [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Per usare lo stesso insieme a MAB/DPM-A, vedere [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Riepilogo del supporto del backup offline

Nella tabella seguente vengono confrontate le due opzioni disponibili, in modo da poter effettuare le scelte appropriate in base allo scenario.

| **Considerazioni**                                            | **Backup offline basato su Azure Data Box**                     | **Backup offline basato su importazione/esportazione di Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelli di distribuzione di backup di Azure                              | Agente MARS (anteprima)                                              | Agente MARS, server di Backup di Azure (MAB), DPM-A                                           |
| Numero massimo di dati di backup per server (MARS) o per gruppo protezione dati (MAB, DPM-A) | [Azure Data Box disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (fino a 10 dischi da 8 TB ciascuno)                          |
| Sicurezza (dati, servizio & di dispositivi)                           | [Dati](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256 bit crittografati <br> Caso robusto del [dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) , interfaccia basata su credenziali proprietaria per la copia dei dati <br> Funzionalità di sicurezza di Azure protette dal [servizio](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | Dati-crittografia BitLocker                                 |
| Provisioning temporaneo del percorso di gestione temporanea                     | Non obbligatorio                                                | Maggiore o uguale alle dimensioni stimate dei dati di backup        |
| Aree supportate                                           | [Aree Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Aree Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Aree di importazione/esportazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Spedizioni tra paesi *                                     | **Non supportato**  <br>    *Indirizzo di origine & il Data Center di destinazione di Azure deve trovarsi nello stesso paese* | Supportato                                                    |
| Trasferimento logistico (recapito, trasporto, selezione)           | Completamente gestito da Microsoft                                     | Gestito dal cliente                                            |
| Prezzi                                                      | [Prezzi di Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Prezzi di Azure Data Box Disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prezzi di importazione/esportazione di Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Se il proprio paese non dispone di un Data Center di Azure, è necessario spedire i dischi a un Data Center di Azure in un altro paese.*

## <a name="next-steps"></a>Passaggi successivi

* [Backup offline basato su Azure Data Box per l'agente MARS](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Backup offline basato su importazione/esportazione di Azure per l'agente MARS](backup-azure-backup-import-export.md)  
* [Backup offline basato su importazione/esportazione di Azure per MAB/DPM-A](backup-azure-backup-server-import-export-.md)
