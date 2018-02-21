---
title: Archiviare e visualizzare dati di diagnostica in Archiviazione di Azure | Documentazione Microsoft
description: Trasferire i dati di diagnostica di Azure in un account di archiviazione di Azure e visualizzarli
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
ms.openlocfilehash: 374cc179e13c00e439415e3df16e0c6d5ccba5e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Archiviare e visualizzare i dati di diagnostica nell'account di archiviazione Azure
I dati di diagnostica non vengono archiviati definitivamente a meno che non vengano trasferiti nell'Emulatore di archiviazione di Microsoft Azure o nell'account di archiviazione di Azure. Una volta trasferiti nella risorsa di archiviazione, sono disponibili diversi strumenti per visualizzarli.

## <a name="specify-a-storage-account"></a>Specificare un account di archiviazione
Specificare l'account di archiviazione da usare nel file ServiceConfiguration.cscfg. Le informazioni account vengono definite come stringa di connessione in un'impostazione di configurazione. L'esempio seguente mostra la stringa di connessione predefinita creata per un nuovo progetto di servizio cloud in Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

È possibile modificare questa stringa di connessione per poter specificare le informazioni per un account di archiviazione di Azure.

A seconda del tipo di dati di diagnostica da raccogliere, Diagnostica di Azure usa il servizio BLOB o il servizio tabelle. La tabella seguente mostra le origini dati persistenti e il relativo formato.

| Origine dati | Formato di archiviazione |
| --- | --- |
| Log di Azure |Tabella |
| Log di IIS 7.0 |BLOB |
| Log dell'infrastruttura Diagnostica di Azure |Tabella |
| Log di analisi delle richieste non riuscite |BLOB |
| Registri eventi di Windows |Tabella |
| Contatori delle prestazioni |Tabella |
| Dump di arresto anomalo del sistema |BLOB |
| Log degli errori personalizzati |BLOB |

## <a name="transfer-diagnostic-data"></a>Trasferire i dati di diagnostica
Per SDK 2.5 e versioni successive, la richiesta di trasferimento dei dati di diagnostica può verificarsi nel file di configurazione. È possibile trasferire i dati di diagnostica a intervalli pianificati, come specificato nella configurazione.

Per SDK 2.4 e versioni precedenti, è possibile richiedere di trasferire i dati di diagnostica nel file di configurazione oltre che a livello di programmazione. L'approccio programmatico consente anche di eseguire trasferimenti su richiesta.

> [!IMPORTANT]
> Quando si trasferiscono i dati di diagnostica in un account di archiviazione di Azure, si devono sostenere i costi per le risorse di archiviazione usate dai dati di diagnostica.
> 
> 

## <a name="store-diagnostic-data"></a>Archiviare i dati di diagnostica
I dati dei log vengono archiviati nell'archivio BLOB o tabelle con i nomi seguenti:

**Tabelle**

* **WadLogsTable** : log scritti nel codice con il listener di traccia.
* **WADDiagnosticInfrastructureLogsTable** : monitor di diagnostica e modifiche di configurazione.
* **WADDirectoriesTable** : directory monitorate dal monitor di diagnostica.  Sono inclusi i log di IIS, i log delle richieste non riuscite di IIS e le directory personalizzate.  La posizione del file di log dei BLOB è specificata nel campo Container e il nome del BLOB si trova nel campo RelativePath.  Il campo AbsolutePath indica la posizione e il nome del file esistente nella macchina virtuale di Azure.
* **WADPerformanceCountersTable** : contatori delle prestazioni.
* **WADWindowsEventLogsTable** : registri eventi di Windows.

**BLOB**

* **wad-control-container** : (solo per SDK 2.4 e versioni precedenti) contiene i file di configurazione XML che controllano Diagnostica di Azure.
* **wad-iis-failedreqlogfiles** : contiene informazioni provenienti dai log delle richieste non riuscite di IIS.
* **wad-iis-logfiles** : contiene informazioni sui log di IIS.
* **"custom"** : contenitore personalizzato basato sulle directory di configurazione monitorate dal monitor di diagnostica.  Il nome di questo contenitore BLOB verrà specificato in WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Strumenti per visualizzare i dati di diagnostica
Sono disponibili diversi strumenti per visualizzare i dati una volta trasferiti nell'account di archiviazione. Ad esempio: 

* Esplora server in Visual Studio: se sono stati installati gli Strumenti di Azure per Microsoft Visual Studio, è possibile usare il nodo Archiviazione di Azure in Esplora server per visualizzare i dati di tabelle e BLOB di sola lettura dagli account di archiviazione di Azure. È possibile visualizzare i dati dall'account dell'emulatore di archiviazione locale e anche dagli account di archiviazione creati per Azure. Per altre informazioni, vedere [Esplorazione e gestione delle risorse di archiviazione con Esplora server](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, OSX e Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) include Azure Diagnostics Manager che consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
[Tracciare il flusso in un'applicazione di Servizi cloud con Diagnostica di Azure](cloud-services-dotnet-diagnostics-trace-flow.md)

