---
title: Panoramica del backup offline
description: Informazioni sui componenti del backup offline. Includono il backup offline basato sul backup Azure Data Box e offline basato sul servizio importazione/esportazione di Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196163"
---
# <a name="overview-of-offline-backup"></a>Panoramica del backup offline

Questo articolo fornisce una panoramica del backup offline.

I backup completi iniziali in Azure trasferiscono in genere grandi quantità di dati online e richiedono una maggiore larghezza di banda di rete rispetto ai backup successivi che trasferiscono solo le modifiche incrementali. Gli uffici remoti o i Data Center in determinate aree geografiche non hanno sempre una larghezza di banda di rete sufficiente. Per questo motivo, questi backup iniziali importano alcuni giorni. Durante questo periodo, i backup usano continuamente la stessa rete di cui è stato effettuato il provisioning per le applicazioni in esecuzione nel Data Center locale.

Backup di Azure supporta il backup offline, che trasferisce i dati di backup iniziali offline, senza usare la larghezza di banda di rete. Fornisce un meccanismo per copiare i dati di backup su dispositivi di archiviazione fisici. I dispositivi vengono quindi spediti a un Data Center di Azure vicino e caricati in un insieme di credenziali di servizi di ripristino. Questo processo garantisce un trasferimento affidabile dei dati di backup senza utilizzare una larghezza di banda di rete.

## <a name="offline-backup-options"></a>Opzioni di backup offline

Il backup offline è disponibile in due modalità in base alla proprietà dei dispositivi di archiviazione:

- Backup offline basato su Azure Data Box (anteprima)
- Backup offline basato sul servizio importazione/esportazione di Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline basato su Azure Data Box (anteprima)

Questa modalità è attualmente supportata con l'agente di Servizi di ripristino di Microsoft Azure (MARS), in anteprima. Questa opzione sfrutta i vantaggi offerti da [Azure Data Box](https://azure.microsoft.com/services/databox/) per fornire appliance di trasferimento Microsoft, sicure e resistenti alle manomissioni con connettori USB al proprio Data Center o a un ufficio remoto. I dati di backup vengono scritti direttamente su questi dispositivi. Questa opzione consente di risparmiare il lavoro richiesto per ottenere i dischi e i connettori compatibili con Azure o per eseguire il provisioning dell'archiviazione temporanea come percorso di gestione temporanea. Microsoft gestisce inoltre la logistica per il trasferimento end-to-end, che è possibile tenere traccia del portale di Azure. 

Di seguito è illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura Data Box di backup di Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Ecco un riepilogo dell'architettura:

1. Backup di Azure copia direttamente i dati di backup in questi dispositivi preconfigurati.
2. È quindi possibile rispedire questi dispositivi a un Data Center di Azure.
3. Azure Data Box copia i dati in un account di archiviazione di proprietà del cliente.
4. Backup di Azure copia automaticamente i dati di backup dall'account di archiviazione all'insieme di credenziali dei servizi di ripristino designato. I backup online incrementali sono pianificati.

Per usare il backup offline basato su Azure Data Box, vedere [backup offline con Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline basato sul servizio importazione/esportazione di Azure

Questa opzione è supportata da Backup di Microsoft Azure Server (MAB), da System Center Data Protection Manager (DPM) DPM-A e dall'agente MARS. Usa il [servizio importazione/esportazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service). È possibile trasferire i dati di backup iniziali in Azure usando dischi e connettori compatibili con Azure. Per questo approccio è necessario effettuare il provisioning dell'archiviazione temporanea nota come percorso di gestione temporanea e utilizzare le utilità predefinite per formattare e copiare i dati di backup nei dischi di proprietà del cliente. 

Di seguito è illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura del servizio importazione/esportazione di backup di Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Ecco un riepilogo dell'architettura:

1. Anziché inviare i dati di backup in rete, backup di Azure scrive i dati di backup in un percorso di gestione temporanea.
2. I dati nel percorso di gestione temporanea vengono scritti in uno o più dischi SATA usando un'utilità personalizzata.
3. Come parte del lavoro preparatorio, l'utilità crea un processo di importazione di Azure. Le unità SATA vengono spedite al Data Center di Azure più vicino e fanno riferimento al processo di importazione per connettere le attività.
4. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
5. Backup di Azure copia i dati di backup dall'account di archiviazione nell'insieme di credenziali di servizi di ripristino. I backup incrementali sono pianificati.

Per usare il backup offline basato sul servizio importazione/esportazione di Azure con l'agente MARS, vedere [flusso di lavoro di backup offline in backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Per usare lo stesso insieme a MAB o DPM-A, vedere [flusso di lavoro di backup offline per DPM e server di backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Riepilogo del supporto del backup offline

Nella tabella seguente vengono confrontate le due opzioni disponibili, in modo da poter effettuare le scelte appropriate in base allo scenario.

| **Considerazioni**                                            | **Backup offline basato su Azure Data Box**                     | **Backup offline basato sul servizio importazione/esportazione di Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelli di distribuzione di backup di Azure                              | Agente MARS (anteprima)                                              | Agente MARS, MAB, DPM-A                                           |
| Numero massimo di dati di backup per server (MARS) o per gruppo protezione dati (MAB, DPM-A) | [Disco Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (fino a 10 dischi da 8 TB ciascuno)                          |
| Sicurezza (dati, dispositivo e servizio)                           | [Dati](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES a 256 bit crittografati <br> Interfaccia basata sui [dispositivi](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) , proprietaria e basata su credenziali per la copia dei dati <br> Funzionalità di sicurezza di Azure protette dal [servizio](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | Dati-crittografia BitLocker                                 |
| Provisioning temporaneo del percorso di gestione temporanea                     | Facoltativo                                                | Maggiore o uguale alle dimensioni stimate dei dati di backup        |
| Aree supportate                                           | [Aree del disco Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Aree Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Aree del servizio importazione/esportazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Spedizioni tra paesi                                     | Non supportato  <br>    L'indirizzo di origine e il Data Center di Azure di destinazione devono trovarsi nello stesso paese * | Supportato                                                    |
| Trasferimento logistico (recapito, trasporto, ritiro)           | Completamente gestito da Microsoft                                     | Gestita dal cliente                                            |
| Prezzi                                                      | [Prezzi di Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Prezzi del disco Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prezzi del servizio importazione/esportazione di Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Se il paese non dispone di un Data Center di Azure, è necessario spedire i dischi a un Data Center di Azure in un altro paese.

## <a name="next-steps"></a>Passaggi successivi

* [Backup offline di backup di Azure tramite Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Flusso di lavoro di backup offline in backup di Azure](backup-azure-backup-import-export.md) 
* [Flusso di lavoro di backup offline per DPM e server di Backup di Azure](backup-azure-backup-server-import-export-.md)
