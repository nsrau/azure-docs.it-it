---
title: Connettere i dati secRMM di squadra Technologies ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di secRMM in squadra Technologies a Sentinel di Azure.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588111"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Connetti i dati di squadra Technologies secRMM ad Azure Sentinel 

> [!IMPORTANT]
> Il connettore dati squadra Technologies Security removibile Media Manager (secRMM) in Azure Sentinel è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Il connettore squadra Technologies secRMM consente di connettere facilmente i log della soluzione di sicurezza di squadra Technologies secRMM con Azure Sentinel. Consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo connettore fornisce informazioni dettagliate sugli eventi di archiviazione rimovibili USB. L'integrazione tra squadra Technologies secRMM e Azure Sentinel si avvale dell'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configurare e connettere le tecnologie squadra secRMM 

Squadra Technologies secRMM può integrare ed esportare i log direttamente in Sentinel di Azure.
1. Nel portale di Azure Sentinel fare clic su connettori dati e selezionare squadra Technologies secRMM e quindi aprire la pagina del connettore.

2. Seguire la procedura descritta nella [Guida all'onboarding delle tecnologie squadra per Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) per ottenere i dati SecRMM di squadra in Sentinel di Azure.   


## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in CustomLogs secRMM_CL.
Per utilizzare lo schema pertinente in Log Analytics per le tecnologie squadra secRMM, cercare secRMM_CL.

## <a name="validate-connectivity"></a>Convalidare la connettività
Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere squadra Technologies secRMM ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.

