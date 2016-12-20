---
title: Database di Azure per enti pubblici | Microsoft Docs
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Database di Azure per enti pubblici
## <a name="sql-database"></a>Database SQL
Per informazioni aggiuntive sulla configurazione della visibilità dei metadati e per conoscere le procedure consigliate sulla protezione, fare riferimento a <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">Centro sicurezza Microsoft per il motore del database SQL </a> e [Documentazione pubblica sul database SQL di Azure ](../sql-database/index.md).

### <a name="variations"></a>Varianti
Il database SQL versione 12 è generalmente disponibile in Azure per enti pubblici.

L'indirizzo per i server SQL Azure in Azure per gli enti pubblici è differente:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Database SQL |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>Considerazioni
Le informazioni seguenti identificano il limite di Azure per enti pubblici per SQL Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| Tutti i dati archiviati ed elaborati in database SQL di Microsoft Azure possono contenere dati regolamentati di Azure per enti pubblici. È necessario usare gli strumenti di database per il trasferimento dei dati regolamentati di Azure per enti pubblici. |I metadati del database SQL di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione del prodotto di archiviazione.  Non immettere dati regolamentati o controllati nei campi relativi a nome del database, nome della sottoscrizione, gruppi di risorse, nome del server, account di accesso amministratore del server, nomi delle distribuzioni, nomi delle risorse e tag delle risorse |

## <a name="azure-redis-cache"></a>Cache Redis di Azure
Per informazioni dettagliate su questo servizio e su come usarlo, vedere la [documentazione pubblica sulla Cache Redis di Azure](../redis-cache/index.md).

### <a name="variations"></a>Varianti
Gli URL per l'accesso e la gestione di Cache Redis di Azure in Azure per gli enti pubblici sono differenti:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Endpoint della cache |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Portale di Azure |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> Tutti gli script e il codice devono tenere conto degli endpoint e degli ambienti appropriati. Per altre informazioni, vedere [Come connettersi al cloud di Azure per enti pubblici](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).
> 
> 

### <a name="considerations"></a>Considerazioni
Le informazioni seguenti identificano il limite di Azure per enti pubblici per Cache Redis di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| Tutti i dati archiviati ed elaborati in Cache Redis di Azure possono contenere dati regolamentati di Azure per enti pubblici. |I metadati di Cache Redis di Azure non possono contenere dati a esportazione controllata. Non immettere dati regolati/controllati nei campi seguenti: nome Cache, nome VNET, nome Sottoscrizione, gruppi di risorse, tag di risorse, proprietà di Redis. |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni e aggiornamenti, iscriversi al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici</a>.




<!--HONumber=Nov16_HO3-->


