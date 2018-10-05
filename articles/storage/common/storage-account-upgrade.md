---
title: Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2 - Archiviazione di Azure | Microsoft Docs
description: Eseguire l'aggiornamento agli account di archiviazione per utilizzo generico v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224495"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2

Gli account di archiviazione per utilizzo generico v2 supportano le funzionalità di archiviazione di Azure più recenti e incorporano tutte le funzionalità degli account di archiviazione BLOB e per utilizzo generico v1. Gli account per utilizzo generico v2 rappresentano la scelta consigliata per la maggior parte degli scenari. Gli account per utilizzo generico v2 offrono i prezzi per gigabyte più bassi per Archiviazione di Azure, oltre a prezzi per transazione competitivi a livello di settore.

L'aggiornamento a un account di archiviazione per utilizzo generico v2 da un account per utilizzo generico v1 o un account di archiviazione BLOB è semplice. È possibile eseguire l'aggiornamento tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. L'aggiornamento di un account non può essere annullato e può comportare l'addebito di costi.

## <a name="upgrade-using-the-azure-portal"></a>Aggiornamento con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione.
3. Nella sezione **Impostazioni** fare clic su **Configurazione**.
4. In **Tipologia account** fare clic su **Aggiorna**.
5. In **Conferma aggiornamento** digitare il nome dell'account. 
6. Fare clic su **Aggiorna** nella parte inferiore del pannello.

## <a name="upgrade-with-powershell"></a>Eseguire l'aggiornamento con PowerShell

Per aggiornare un account per utilizzo generico v1 a un account per utilizzo generico v2 con PowerShell, aggiornare prima PowerShell in modo che usi l'ultima versione del modulo **AzureRm.Storage**. Per informazioni su come installare PowerShell, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 

Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse e l'account di archiviazione:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Eseguire l'aggiornamento con l'interfaccia della riga di comando di Azure

Per aggiornare un account per utilizzo generico v1 a un account per utilizzo generico v2 con l'interfaccia della riga di comando di Azure, installare prima l'ultima versione dell'interfaccia della riga di comando di Azure. Per informazioni sull'installazione dell'interfaccia della riga di comando, vedere [Install the Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Installare l'interfaccia della riga di comando di Azure 2.0). 

Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse e l'account di archiviazione:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Specificare un livello di accesso per i dati BLOB

Gli account per utilizzo generico v2 supportano tutti i servizi di archiviazione e gli oggetti dati di Azure, ma i livelli di accesso sono disponibili solo per i BLOB in blocchi nell'archiviazione BLOB. Quando si esegue l'aggiornamento a un account di archiviazione per utilizzo generico v2, è possibile specificare un livello di accesso per i dati BLOB. 

I livelli di accesso consentono di scegliere l'archiviazione economicamente più conveniente in base i modelli di utilizzo previsti. I BLOB in blocchi possono essere archiviati in un livello ad accesso frequente, ad accesso sporadico o archivio. Per altre informazioni sui livelli di accesso, vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md).

Per impostazione predefinita, viene creato un nuovo account di archiviazione nel livello ad accesso frequente e un account di archiviazione per utilizzo generico v1 viene aggiornato al livello ad accesso frequente. Se si sta valutando il livello di accesso da usare per i dati dopo l'aggiornamento, prendere in considerazione lo specifico scenario. Esistono due tipici scenari utente per la migrazione a un account per utilizzo generico v2:

* È disponibile un account di archiviazione per utilizzo generico v1 esistente e si vuole valutare una modifica per passare a un account di archiviazione per utilizzo generico v2 con il livello di archiviazione corretto per i dati BLOB.
* Si è deciso di usare un account di archiviazione per utilizzo generico v2 o ne già disponibile uno e si vuole valutare se è opportuno usare il livello di archiviazione ad accesso frequente oppure quello ad accesso sporadico per i dati BLOB.

In entrambi i casi, è prioritario stimare il costo di archiviazione, accesso e gestione dei dati archiviati in un account di archiviazione per utilizzo generico v2 e confrontarlo con i costi attuali.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Stimare i costi per i modelli di utilizzo correnti

Per stimare il costo di archiviazione e accesso ai dati BLOB in un account di archiviazione per utilizzo generico v2 a un particolare livello, è necessario valutare il modello di utilizzo esistente o definire approssimativamente il modello di utilizzo previsto. In genere, è consigliabile conoscere quanto segue:

* Uso dell'archiviazione BLOB, in gigabyte, inclusi:
    - Quantità di dati archiviata nell'account di archiviazione
    - Variazione nel volume di dati su base mensile e se i dati precedenti vengono costantemente sostituiti da nuovi dati
* Modello di accesso primario per i dati di archiviazione BLOB, inclusi:
    - Quantità di dati letti e scritti nell'account di archiviazione 
    - Numero di operazioni di lettura e di scrittura eseguite sui dati nell'account di archiviazione

Per scegliere il miglior livello di accesso per le proprie esigenze, può essere utile determinare la capacità attualmente in uso per i dati BLOB e la modalità d'uso per tali dati. 

Per raccogliere i dati sull'utilizzo per l'account di archiviazione prima della migrazione, è possibile monitorare l'account di archiviazione tramite [Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Monitoraggio di Azure esegue la registrazione e fornisce dati sulle metriche per i servizi di Azure, incluso Archiviazione di Azure. 

Per monitorare i dati sull'utilizzo per i BLOB nell'account di archiviazione, abilitare le metriche per la capacità in Monitoraggio di Azure. Le metriche per la capacità registrano su base giornaliera i dati sullo spazio di archiviazione usato per i BLOB nell'account. Le metriche per la capacità possono essere usate per stimare i costi di archiviazione dei dati nell'account di archiviazione. Per informazioni sui prezzi della capacità di archiviazione BLOB per ogni tipo di account, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

Per monitorare i modelli di accesso ai dati per l'archiviazione BLOB, abilitare le metriche delle transazioni in Monitoraggio di Azure. È possibile applicare filtri in base alle diverse operazioni di Archiviazione di Azure per stimare la frequenza con cui ognuna viene chiamata. Per informazioni sui prezzi per i diversi tipi di transazioni per i BLOB in blocchi e di accodamento per ogni tipo di account, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Per altre informazioni sulla raccolta di metriche da Monitoraggio di Azure, vedere [Metriche di Archiviazione di Azure in Monitoraggio di Azure](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](storage-quickstart-create-account.md)
- [Gestire gli account di archiviazione di Azure](storage-account-manage.md)