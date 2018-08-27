---
title: Metriche supportate con monitoraggio di Azure in Azure Stack | Microsoft Docs
description: Informazioni sulle metriche supportate per monitoraggio di Azure in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 109ac1b0d9f52211f05e5fc1b177aa775ba66aae
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889961"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Metriche supportate con monitoraggio di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile recuperare le metriche da Azure monitor in Azure Stack nello stesso oggetto Azure globale. È possibile misure nel portale di scaricarli dall'API REST o eseguirvi query con PowerShell o CLI.

Le tabelle seguenti elencano le metriche disponibili con pipeline delle metriche di monitoraggio di Azure in Azure Stack. Per eseguire query e accedere a queste metriche, è necessario il **2018-01-01** versione api-version del profilo di API. Per altre informazioni sui profili di API e Azure Stack, vedere [profili della versione di gestione API in Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | DESCRIZIONE | Dimensioni |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| CPU percentuale | CPU percentuale | Percentuale | Media | La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali | Nessuna dimensione |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | DESCRIZIONE | Dimensioni |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Capacità usata | Byte | Media | Capacità usata account | Nessuna dimensione |
| Transazioni | Transazioni | Conteggio | Totale | Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi. | ResponseType, GeoType, ApiName |
| Ingress | Dati in ingresso | Byte | Totale | Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. | GeoType, ApiName |
| Egress | Egress | Byte | Totale | Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. | GeoType, ApiName |
| SuccessServerLatency | Latenza server per richieste con esito positivo | Millisecondi | Media | Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latenza end-to-end per richieste con esito positivo | Millisecondi | Media | Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta. | GeoType, ApiName |
| Disponibilità | Disponibilità | Percentuale | Media | Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | DESCRIZIONE | Dimensioni |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Capacità BLOB | Byte | Totale | Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte. | BlobType |
| BlobCount | Numero di BLOB | Conteggio | Totale | Numero di BLOB nel servizio BLOB dell'account di archiviazione. | BlobType |
| ContainerCount | Numero di contenitori BLOB | Conteggio | Media | Numero di contenitori nel servizio BLOB dell'account di archiviazione. | Nessuna dimensione |
| Transazioni | Transazioni | Conteggio | Totale | Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi. | ResponseType, GeoType, ApiName |
| Ingress | Dati in ingresso | Byte | Totale | Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. | GeoType, ApiName |
| Egress | Egress | Byte | Totale | Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. | GeoType, ApiName |
| SuccessServerLatency | Latenza server per richieste con esito positivo | Millisecondi | Media | Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latenza end-to-end per richieste con esito positivo | Millisecondi | Media | Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta. | GeoType, ApiName |
| Disponibilità | Disponibilità | Percentuale | Media | Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | DESCRIZIONE | Dimensioni |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Capacità tabella | Byte | Media | Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte. | Nessuna dimensione |
| TableCount | Numero di tabella | Conteggio | Media | Numero di tabella nel servizio tabelle dell'account di archiviazione. | Nessuna dimensione |
| TableEntityCount | Numero di entità di tabella | Conteggio | Media | Numero di entità di tabella nel servizio tabelle dell'account di archiviazione. | Nessuna dimensione |
| Transazioni | Transazioni | Conteggio | Totale | Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi. | ResponseType, GeoType, ApiName |
| Ingress | Dati in ingresso | Byte | Totale | Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. | GeoType, ApiName |
| Egress | Egress | Byte | Totale | Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. | GeoType, ApiName |
| SuccessServerLatency | Latenza server per richieste con esito positivo | Millisecondi | Media | Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latenza end-to-end per richieste con esito positivo | Millisecondi | Media | Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta. | GeoType, ApiName |
| Disponibilità | Disponibilità | Percentuale | Media | Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | DESCRIZIONE | Dimensioni |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Capacità coda | Byte | Media | Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte. | Nessuna dimensione |
| QueueCount | Numero di coda | Conteggio | Media | Numero di coda in Servizio di accodamento dell'account di archiviazione. | Nessuna dimensione |
| QueueMessageCount | Numero di messaggi in coda | Conteggio | Media | Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione. | Nessuna dimensione |
| Transazioni | Transazioni | Conteggio | Totale | Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi. | ResponseType, GeoType, ApiName |
| Ingress | Dati in ingresso | Byte | Totale | Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. | GeoType, ApiName |
| Egress | Egress | Byte | Totale | Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. | GeoType, ApiName |
| SuccessServerLatency | Latenza server per richieste con esito positivo | Millisecondi | Media | Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latenza end-to-end per richieste con esito positivo | Millisecondi | Media | Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta. | GeoType, ApiName |
| Disponibilità | Disponibilità | Percentuale | Media | Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata. | GeoType, ApiName |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [il monitoraggio di Azure in Azure Stack](azure-stack-metrics-azure-data.md).