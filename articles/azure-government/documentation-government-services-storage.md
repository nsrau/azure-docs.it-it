---
title: Archiviazione di Azure per enti pubblici | Microsoft Docs
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Archiviazione di Azure per enti pubblici
## <a name="azure-storage"></a>Archiviazione di Azure
Per informazioni dettagliate su questo servizio e su come usarlo, vedere la [documentazione pubblica sull'Archiviazione di Azure](../storage/index.md).

### <a name="variations"></a>Varianti
Gli URL degli account di archiviazione in Azure per enti pubblici sono diversi:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Archiviazione BLOB |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Archiviazione di accodamento |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Archiviazione tabelle |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> Tutti gli script e il codice devono tenere conto dei dovuti endpoint.  Vedere [Configurare le stringhe di connessione di archiviazione di Azure](../storage/storage-configure-connection-string.md). 
>
>

Per ulteriori informazioni sulle API, vedere <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Cloud Storage Account Constructor</a> (Costruttore degli account di Archiviazione cloud).

Il suffisso dell'endpoint da usare in questi overload è core.usgovcloudapi.net

### <a name="considerations"></a>Considerazioni
Le informazioni seguenti identificano il limite di Azure per enti pubblici per Archiviazione di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| I dati immessi, archiviati ed elaborati in un prodotto di Archiviazione di Azure possono contenere dati soggetti al controllo all'esportazione. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure. |I metadati di Archiviazione di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione del prodotto di archiviazione.  Non immettere dati regolamentati o controllati nei campi relativi a gruppi di risorse, nomi delle distribuzioni, nomi delle risorse e tag delle risorse. |

## <a name="premium-storage"></a>Archiviazione Premium
Per ulteriori informazioni su questo servizio e su come usarlo, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md).

### <a name="variations"></a>Varianti
L'archiviazione Premium disponibile a livello generale nell'area Governo degli Stati Uniti - Virginia. Ciò vale anche per le macchine virtuali serie DS.

### <a name="considerations"></a>Considerazioni
Per gli account di archiviazione Premium valgono le stesse considerazioni sull'archiviazione dei dati di cui sopra.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni e aggiornamenti, iscriversi al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici</a>.



<!--HONumber=Nov16_HO3-->


