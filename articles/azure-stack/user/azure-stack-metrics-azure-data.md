---
title: Monitoraggio di Azure in Azure Stack | Microsoft Docs
description: Informazioni su monitoraggio di Azure in Azure Stack.
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
ms.date: 12/01/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: c16f218903557a6d3a10f3ef201587d2885fffa9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239104"
---
# <a name="azure-monitor-on-azure-stack"></a>Monitoraggio di Azure in Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo offre una panoramica del servizio monitoraggio di Azure in Azure Stack. Viene descritto il funzionamento del monitoraggio di Azure e informazioni aggiuntive su come usare monitoraggio di Azure in Azure Stack. 

Per un'introduzione, panoramica e su come iniziare a usare monitoraggio di Azure, vedere l'articolo di Azure globale [Introduzione a monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Pannello Stack monitoraggio di Azure](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Monitoraggio di Azure è il servizio di piattaforma che mette a disposizione un'unica origine per il monitoraggio delle risorse di Azure. Con Monitoraggio di Azure è possibile visualizzare, fare query, indirizzare, archiviare ed effettuare operazioni sulle metriche e sui log provenienti dalle risorse di Azure. Per lavorare con i dati usando il portale di amministrazione di Azure Stack, i cmdlet di PowerShell di monitoraggio, Cross-Platform della riga di comando o le API REST di monitoraggio di Azure. Per la connettività specifica supportata da Azure Stack, vedere [come utilizzare i dati di monitoraggio di Azure Stack](azure-stack-metrics-monitor.md)

> [!Note]  
Le metriche e log di diagnostica non sono disponibili per Azure Stack Development Kit.

## <a name="prerequisites"></a>Prerequisiti

Registrare il **Microsoft. Insights** provider di risorse in impostazioni dei provider di risorse offerta della sottoscrizione. È possibile verificare che il provider di risorse è disponibile nell'offerta associata alla sottoscrizione:

1. Aprire il portale di amministrazione di Azure Stack.
2. Selezionare **offre**.
3. Selezionare l'offerta associato alla sottoscrizione.
4. Selezionare **provider di risorse** sotto **impostazioni.** 
5. Trovare **Microsoft. Insights** nell'elenco e verificare che lo stato sia **Registered.**.

## <a name="overview"></a>Panoramica

Ad esempio monitoraggio di Azure in Azure, monitoraggio di Azure in Azure Stack offre log e metriche dell'infrastruttura di livello di base per la maggior parte dei servizi.

## <a name="azure-monitor-sources-compute-subset"></a>Origini di monitoraggio di Azure: subset di calcolo

![Monitoraggio di Azure origini - subset di calcolo](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Il **Microsoft. COMPUTE** include provider di risorse in Azure Stack:
 - Macchine virtuali 
 - Set di scalabilità di macchine virtuali

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Applicazione - log di diagnostica, log applicazioni e metriche

Le applicazioni possono eseguire nel sistema operativo di una macchina virtuale in esecuzione con il **Microsoft. COMPUTE** provider di risorse. Queste applicazioni e le macchine virtuali generano set di metriche e log personalizzati. Monitoraggio di Azure si basa sull'estensione Diagnostica di Azure (Windows o Linux) per raccogliere la maggior parte delle metriche e dei log a livello di applicazione. 

I tipi di misure includono:
 - Contatori delle prestazioni
 - Log applicazioni
 - Registri eventi di Windows
 - Origine degli eventi .NET
 - Log di IIS
 - ETW basato su manifesto
 - Dump di arresto anomalo del sistema
 - Log degli errori dei clienti

> [!Note]  
> Estensione di diagnostica per Linux in Azure Stack non sono supportati.

### <a name="host-and-guest-vm-metrics"></a>Metriche delle VM host e guest

Le risorse di calcolo elencate sopra hanno una VM host dedicata e sistema operativo guest. L'host della macchina virtuale e sistema operativo guest sono l'equivalente della VM radice e VM guest nell'hypervisor Hyper-V. È possibile raccogliere le metriche per l'host della macchina virtuale e il sistema operativo guest. Inoltre, è possibile raccogliere i log di diagnostica per il sistema operativo guest. Sono disponibili in un elenco delle metriche da collezione per le metriche Host e macchine Virtuali Guest in Azure Stack [metriche supportate con monitoraggio di Azure in Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Log attività

È possibile cercare i log attività per informazioni sulle risorse di calcolo, come illustrato dall'infrastruttura di Azure Stack. I log contengono informazioni quali gli orari di creazione ed eliminazione delle risorse. I log attività in Azure Stack è coerente con Azure. Per altre informazioni, vedere la descrizione della [Panoramica del log attività in Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Origini di monitoraggio di Azure: tutto il resto

![Origini di monitoraggio di Azure: altro](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Registra le risorse - metriche e diagnostica

Log di diagnostica e metriche ritirabile variano in base al tipo di risorsa. Un elenco delle metriche da collezione per ogni risorsa in Azure Stack è disponibile all'indirizzo metriche supportate. Per altre informazioni, vedere [metriche supportate con monitoraggio di Azure in Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Log attività

Il log attività è che lo stesso per le risorse di calcolo. 

### <a name="uses-for-monitoring-data"></a>Viene utilizzato per i dati di monitoraggio

**Store e archiviazione**  

Alcuni dati di monitoraggio sono già archiviati e disponibili in Monitoraggio di Azure per un periodo di tempo specificato. 
 - Le metriche vengono archiviate per 90 giorni. 
 - Le voci di log attività vengono archiviate per 90 giorni. 
 - Non sono archiviati i log di diagnostica.
 - Archiviare i dati da un account di archiviazione per un lungo periodo.

**Query**  

È possibile usare l'API REST di monitoraggio di Azure, i comandi dell'interfaccia della riga di comando multipiattaforma, i cmdlet di PowerShell o .NET SDK per accedere ai dati nel sistema o in archiviazione di Azure. 

**Visualization**

La visualizzazione in grafici dei dati di monitoraggio permette di identificare le tendenze più rapidamente della ricerca sui dati. 

Ecco alcuni metodi di visualizzazione:
 - Usare il portale utenti e amministratori di Azure Stack
 - Dati della route da Microsoft Power BI
 - Instradare i dati a uno strumento di visualizzazione di terze parti usando lo streaming live o facendo in modo che lo strumento esegua la lettura da un archivio in Archiviazione di Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Monitorare i metodi di accesso di Azure in Azure Stack

In generale, è possibile modificare il rilevamento, il routing e il recupero dei dati usando uno dei metodi seguenti. Non tutti i metodi sono disponibili per tutte le azioni o tutti i tipi di dati.

 - [Portale di Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface(CLI) cross-platform della riga di comando](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [API REST](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle opzioni di monitoraggio utilizzo dati in Azure Stack nell'articolo di [Consume il monitoraggio dei dati da Azure Stack](azure-stack-metrics-monitor.md).
