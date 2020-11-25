---
title: Connettere i dati standard dell'endpoint di VMware Carbon Black Cloud a Sentinel di Azure | Microsoft Docs
description: Informazioni su come connettere i dati standard dell'endpoint cloud nero di VMware a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023875"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Connettere lo standard di endpoint cloud nero di VMware Carbon ad Azure Sentinel con la funzione di Azure

> [!IMPORTANT]
> Il connettore dati standard dell'endpoint di VMware Carbon Black Cloud in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore VMware Carbon Black Cloud Endpoint standard consente di connettere facilmente tutti i log della soluzione di sicurezza [standard di VMware Carbon Black endpoint](https://www.carbonblack.com/products/endpoint-standard/) con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra VMware Carbon Black e Azure Sentinel usa funzioni di Azure per eseguire il pull dei dati di log tramite l'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Configurare e connettere VMware Carbon Black Cloud Endpoint standard

Funzioni di Azure è in grado di integrare ed effettuare il pull di eventi e log direttamente da VMware Carbon Black Cloud Endpoint standard e di inviarli ad Azure Sentinel.

1. Nel portale di Azure Sentinel, fare clic su **connettori dati** e selezionare connettore **VMware Carbon Black** .
2. Selezionare **Apri connettore pagina**.
3. Seguire le istruzioni riportate nella pagina **nero di carbone di VMware** .


## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics nelle tabelle **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** e * * * * CarbonBlackEvents_CL * * * *.

## <a name="validate-connectivity"></a>Convalidare la connettività
Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere l'endpoint di VMware Carbon Black Cloud standard ad Azure Sentinel usando le app per le funzioni di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

