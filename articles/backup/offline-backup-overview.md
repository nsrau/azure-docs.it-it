---
title: Panoramica del backup offline
description: Informazioni sui componenti del backup offline. Includono il backup offline basato su Azure Data Box e il backup offline basato sul servizio Importazione/Esportazione di Azure.They include offline backup based on Azure Data Box and offline backup based on Azure Import/Export service.
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

In questo articolo viene fornita una panoramica del backup offline.

I backup completi iniziali in Azure in genere trasferiscono grandi quantità di dati online e richiedono una maggiore larghezza di banda di rete rispetto ai backup successivi che trasferiscono solo le modifiche incrementali. Gli uffici remoti o i data center in determinate aree geografiche non sempre dispongono di larghezza di banda di rete sufficiente. Per questo motivo, questi backup iniziali richiedono diversi giorni. Durante questo periodo, i backup utilizzano continuamente la stessa rete di cui è stato eseguito il provisioning per le applicazioni in esecuzione nel data center locale.

Backup di Azure supporta il backup offline, che trasferisce i dati di backup iniziali offline, senza l'uso della larghezza di banda di rete. Fornisce un meccanismo per copiare i dati di backup su dispositivi di archiviazione fisici. I dispositivi vengono quindi inviati a un data center di Azure nelle vicinanze e caricati in un insieme di credenziali di Servizi di ripristino. Questo processo garantisce un robusto trasferimento dei dati di backup senza utilizzare alcuna larghezza di banda di rete.

## <a name="offline-backup-options"></a>Opzioni di backup offline

Il backup offline viene offerto in due modalità in base alla proprietà dei dispositivi di archiviazione:

- Backup offline basato su Azure Data Box (anteprima)Offline backup based on Azure Data Box (preview)
- Backup offline basato sul servizio Importazione/Esportazione di Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline basato su Azure Data Box (anteprima)Offline backup based on Azure Data Box (preview)

Questa modalità è attualmente supportata con l'agente di Microsoft Azure Recovery Services (MARS) in anteprima. Questa opzione sfrutta [Azure Data Box](https://azure.microsoft.com/services/databox/) per spedire appliance di trasferimento proprietari, sicure e resistenti a manomissioni microsoft con connettori USB al data center o all'ufficio remoto. I dati di backup vengono scritti direttamente su questi dispositivi. Questa opzione consente di risparmiare lo sforzo necessario per procurarsi i propri dischi e connettori compatibili con Azure o per eseguire il provisioning dell'archiviazione temporanea come posizione di gestione temporanea. Microsoft gestisce anche la logistica di trasferimento end-to-end, che è possibile monitorare tramite il portale di Azure.Microsoft also handles the end-to-end transfer logistics, which you can track through the Azure portal. 

Di seguito viene illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura della casella dei dati di Backup di AzureAzure Backup Data Box architecture](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Ecco un riepilogo dell'architettura:

1. Backup di Azure copia direttamente i dati di backup in questi dispositivi preconfigurati.
2. È quindi possibile spedire questi dispositivi a un data center di Azure.You can then ship these devices back to an Azure datacenter.
3. Azure Data Box copia i dati in un account di archiviazione di proprietà del cliente.
4. Backup di Azure copia automaticamente i dati di backup dall'account di archiviazione all'insieme di credenziali dei servizi di ripristino designato. I backup incrementali in linea sono pianificati.

Per usare il backup offline basato su Azure Data Box, vedere [Backup offline con Azure Data Box.](offline-backup-azure-data-box.md)

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline basato sul servizio Importazione/Esportazione di Azure

Questa opzione è supportata da DPM-A di Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) e dall'agente MARS. Usa il [servizio Importazione/Esportazione di Azure.](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) È possibile trasferire i dati di backup iniziali in Azure usando i dischi e i connettori compatibili con Azure.You can transfer initial backup data to Azure by using your own Azure-compatible disks and connectors. Questo approccio richiede il provisioning dell'archiviazione temporanea nota come posizione di gestione temporanea e l'utilizzo di utilità predefinite per formattare e copiare i dati di backup nei dischi di proprietà del cliente. 

Di seguito viene illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura del servizio Importazione/Esportazione di Backup di AzureAzure Backup Import/Export service architecture](./media/offline-backup-overview/azure-backup-import-export.png)

Ecco un riepilogo dell'architettura:

1. Anziché inviare i dati di backup in rete, Backup di Azure scrive i dati di backup in un percorso di gestione temporanea.
2. I dati nel percorso di gestione temporanea vengono scritti in uno o più dischi SATA utilizzando un'utilità personalizzata.
3. Come parte del lavoro preparatorio, l'utilità crea un processo di importazione di Azure.As part of the preparatory work, the utility creates an Azure import job. Le unità SATA vengono spedite al data center di Azure più vicino e fanno riferimento al processo di importazione per connettere le attività.
4. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
5. Backup di Azure copia i dati di backup dall'account di archiviazione all'insieme di credenziali di Servizi di ripristino. I backup incrementali sono pianificati.

Per usare il backup offline basato sul servizio Importazione/Esportazione di Azure con l'agente MARS, vedere Flusso di lavoro di backup offline in Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

Per utilizzare lo stesso insieme a MABS o DPM-A, vedere Flusso di lavoro di [backup offline per DPM e server di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Riepilogo del supporto per i backup offline

Nella tabella seguente vengono confrontate le due opzioni disponibili in modo che sia possibile effettuare le scelte appropriate in base allo scenario.

| **Considerazioni**                                            | **Backup offline basato su Azure Data Box**                     | **Backup offline basato sul servizio Importazione/Esportazione di Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelli di distribuzione di Backup di AzureAzure Backup deployment models                              | Agente MARS (anteprima)                                              | Agente MARS, MABS, DPM-A                                           |
| Dati di backup massimi per server (MARS) o per gruppo protezione dati (MABS, DPM-A) | [Disco Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (fino a 10 dischi da 8 TB ciascuno)                          |
| Sicurezza (dati, dispositivo e servizio)                           | [Dati](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES a 256 bit crittografati <br> [Dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Caso incasivero, proprietario, interfaccia basata sulle credenziali per copiare i dati <br> Servizio - Protetto dalle funzionalità di sicurezza [di AzureService](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Protected by Azure security features | Dati - BitLocker crittografato                                 |
| Provisioning temporaneo del percorso di gestione temporanea                     | Facoltativo                                                | Maggiore o uguale alla dimensione stimata dei dati di backup        |
| Aree supportate                                           | [Aree disco di Azure Data BoxAzure Data Box disk regions](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Aree di Azure Data BoxAzure Data Box regions](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Aree del servizio Importazione/Esportazione di AzureAzure Import/Export service regions](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Spedizione tra paesi                                     | Non supportate  <br>    L'indirizzo di origine e il data center di Azure di destinazione devono trovarsi nello stesso paese. | Supportato                                                    |
| Logistica di trasferimento (consegna, trasporto, ritiro)           | Completamente gestito da Microsoft                                     | Gestita dal cliente                                            |
| Prezzi                                                      | [Prezzi di Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Prezzi dei dischi di Azure Data BoxAzure Data Box disk pricing](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prezzi del servizio Importazione/Esportazione di AzureAzure Import/Export service pricing](https://azure.microsoft.com/pricing/details/storage-import-export/) |

Se il paese non dispone di un data center di Azure, è necessario spedire i dischi a un data center di Azure in un altro paese.

## <a name="next-steps"></a>Passaggi successivi

* [Backup di Azure backup offline con Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) 
* [Flusso di lavoro di backup offline per DPM e il server di backup di Azure](backup-azure-backup-server-import-export-.md)
