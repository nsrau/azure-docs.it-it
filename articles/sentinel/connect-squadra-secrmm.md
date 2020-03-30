---
title: Connect Squadra Technologies secRMM data in Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati secRMM di Squadra Technologies ad Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588111"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Connettere i dati secRMM di Squadra Technologies ad Azure Sentinel 

> [!IMPORTANT]
> Il connettore dati di Squadra Technologies Security Removable Media Manager (secRMM) in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Il connettore SecRMM di Squadra Technologies consente di connettere facilmente i log delle soluzioni di sicurezza secRMM di Squadra Technologies con Azure Sentinel. Consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo connettore fornisce informazioni dettagliate sugli eventi di archiviazione rimovibile USB. L'integrazione tra Squadra Technologies secRMM e Azure Sentinel utilizza l'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configurare e connettere Squadra Technologies secRMM 

Squadra Technologies secRMM può integrare ed esportare i log direttamente in Azure Sentinel.
1. Nel portale di Azure Sentinel fare clic su Connettori dati e selezionare Squadra Technologies secRMM e quindi pagina Apri connettore.

2. Seguire i passaggi descritti nella guida all'onboarding di [Squadra Technologies per Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) per ottenere i dati secRMM di Squadra in Azure Sentinel.   


## <a name="find-your-data"></a>Trova i tuoi dati

Dopo aver stabilito una connessione corretta, i dati vengono visualizzati in Log Analytics in CustomLogs secRMM_CL.
Per utilizzare lo schema pertinente in Log Analytics per Squadra Technologies secRMM, cercare secRMM_CL.

## <a name="validate-connectivity"></a>Convalidare la connettività
Potrebbero essere voluti fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 


## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Squadra Technologies secRMM to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

