---
title: Panoramica del backup offline
description: Informazioni sui componenti del backup offline. Includono il backup offline basato su Azure Data Box e sul servizio Importazione/Esportazione di Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: c5e0f4e722e2dd15b7277a484af2a101844344e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503626"
---
# <a name="overview-of-offline-backup"></a>Panoramica del backup offline

Questo articolo fornisce una panoramica del backup offline.

I backup completi iniziali in Azure comportano in genere il trasferimento di grandi quantità di dati online e richiedono una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferite solo le modifiche incrementali. Gli uffici o i data center remoti in determinate aree geografiche non dispongono sempre di una larghezza di banda di rete sufficiente. Per questo motivo, il completamento di questi backup iniziali richiede alcuni giorni. Durante questo periodo, i backup usano continuamente la stessa rete di cui è stato effettuato il provisioning per le applicazioni in esecuzione nel data center locale.

Backup di Azure supporta il backup offline, che trasferisce i dati dei backup iniziali offline, senza usare la larghezza di banda di rete. Esso fornisce un meccanismo per copiare i dati di backup su dispositivi di archiviazione fisica. I dispositivi vengono quindi spediti a un data center di Azure vicino e caricati in un insieme di credenziali di Servizi di ripristino. Questo processo garantisce un trasferimento solido dei dati di backup senza usare alcuna larghezza di banda di rete.

## <a name="offline-backup-options"></a>Opzioni di backup offline

Il backup offline è disponibile in due modalità, in base alla proprietà dei dispositivi di archiviazione:

- Backup offline basato su Azure Data Box (anteprima)
- Backup offline basato sul servizio Importazione/Esportazione di Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline basato su Azure Data Box (anteprima)

Questa modalità è attualmente supportata con l'agente di Servizi di ripristino di Microsoft Azure (MARS), in anteprima. Questa opzione sfrutta i vantaggi offerti da [Azure Data Box](https://azure.microsoft.com/services/databox/) per offrire appliance di trasferimento proprietarie di Microsoft sicure e resistenti alle manomissioni con connettori USB al proprio data center o ad un ufficio remoto. I dati di backup vengono scritti direttamente su questi dispositivi. Questa opzione evita di dover ottenere dischi e connettori compatibili con Azure o eseguire il provisioning dell'archiviazione temporanea come percorso di staging. Microsoft gestisce anche la logistica per il trasferimento end-to-end, di cui è possibile tenere traccia tramite il portale di Azure.

Di seguito è illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura Data Box di Backup di Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Di seguito è riportato un riepilogo dell'architettura:

1. Backup di Azure copia direttamente i dati di backup in questi dispositivi preconfigurati.
2. È quindi possibile rispedire questi dispositivi a un data center di Azure.
3. Azure Data Box copia i dati in un account di archiviazione di proprietà del cliente.
4. Backup di Azure copia automaticamente i dati di backup dall'account di archiviazione all'insieme di credenziali di Servizi di ripristino designato. I backup incrementali online sono pianificati.

Per usare il backup offline basato su Azure Data Box, vedere [Backup offline con Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline basato sul servizio Importazione/Esportazione di Azure

Questa opzione è supportata da Server di Backup di Microsoft Azure (MABS), da System Center Data Protection Manager (DPM) DPM-A e dall'agente MARS. Essa si avvale del [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md). È possibile trasferire i dati di backup iniziali in Azure usando dischi e connettori compatibili con Azure. Per questo approccio è necessario effettuare il provisioning dell'archiviazione temporanea nota come percorso di staging e usare le utilità predefinite per formattare e copiare i dati di backup su dischi di proprietà del cliente.

Di seguito è illustrata un'architettura che descrive lo spostamento dei dati di backup con questa opzione.

![Architettura del servizio Importazione/Esportazione di backup di Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Di seguito è riportato un riepilogo dell'architettura:

1. Anziché inviare i dati di backup in rete, Backup di Azure li scrive in un percorso di staging.
2. I dati nel percorso di staging vengono scritti in uno o più dischi SATA usando un'utilità personalizzata.
3. Nel quadro delle operazioni preparatorie, l'utilità crea un processo di Importazione di Azure. Le unità SATA vengono inviate al data center di Azure più vicino, con riferimento al processo di importazione per connettere le attività.
4. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
5. Backup di Azure copia i dati di backup dall'account di archiviazione nell'insieme di credenziali di Servizi di ripristino. I backup incrementali online sono pianificati.

Per usare il backup offline basato sul servizio Importazione/Esportazione di Azure con l'agente MARS, vedere [Backup offline basato sul servizio Importazione/Esportazione di Azure](./backup-azure-backup-import-export.md).

Per usare lo stesso backup assieme a MABS o DPM-A, vedere [Flusso di lavoro di backup offline per DPM e server di Backup di Azure](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Riepilogo del supporto per il backup offline

Nella tabella seguente vengono confrontate le due opzioni disponibili, in modo da poter effettuare le scelte appropriate in base allo scenario.

| **Considerazioni**                                            | **Backup offline basato su Azure Data Box**                     | **Backup offline basato sul servizio Importazione/Esportazione di Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelli di distribuzione di Backup di Azure                              | Agente MARS (anteprima)                                              | Agente MARS, MABS, DPM-A                                           |
| Numero massimo di dati di backup per server (MARS) o per gruppo protezione dati (MABS, DPM-A) | [Disco Azure Data Box](../databox/data-box-disk-overview.md): 7,2 TB <br> [Azure Data Box](../databox/data-box-overview.md): 80 TB       | 80 TB (fino a 10 dischi da 8 TB ciascuno)                          |
| Sicurezza (dati, dispositivo e servizio)                           | [Dati](../databox/data-box-security.md#data-box-data-protection): crittografia AES a 256 bit <br> [Dispositivo](../databox/data-box-security.md#data-box-device-protection): interfaccia proprietaria, resistente e basata su credenziali per la copia dei dati <br> [Servizio](../databox/data-box-security.md#data-box-service-protection): protetto dalle funzioni di sicurezza di Azure | Dati: crittografati con BitLocker                                 |
| Provisioning temporaneo del percorso di staging                     | Facoltativo                                                | Maggiore o uguale alle dimensioni stimate dei dati di backup        |
| Aree supportate                                           | [Aree del disco di Azure Data Box](../databox/data-box-disk-overview.md#region-availability) <br> [Aree di Azure Data Box](../databox/data-box-disk-overview.md#region-availability) | [Aree del servizio di Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md#region-availability) |
| Spedizioni tra Paesi                                     | Non supportate  <br>    L'indirizzo di origine e il data center di Azure di destinazione devono trovarsi nello stesso Paese/nellòa stessa area* | Supportato                                                    |
| Trasferimento logistico (consegna, trasporto, ritiro)           | Interamente gestito da Microsoft                                     | Gestita dal cliente                                            |
| Prezzi                                                      | [Prezzi di Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Prezzi dei dischi di Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prezzi del servizio di Importazione/Esportazione di Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Se il Paese/l'area geografica non dispone di un data center di Azure, è necessario spedire i dischi a un data center di Azure in un altro Paese/un'altra area geografica.

## <a name="next-steps"></a>Passaggi successivi

- [Backup offline di Backup di Azure tramite Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Flusso di lavoro del backup offline in Backup di Azure](backup-azure-backup-import-export.md)
- [Flusso di lavoro di backup offline per DPM e server di Backup di Azure](backup-azure-backup-server-import-export.md)
