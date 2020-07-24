---
title: Connettere i dati di gestione della vulnerabilità di Qualys ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di gestione delle vulnerabilità di Qualys a Sentinel di Azure.
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
ms.openlocfilehash: 34f2cfa06cbdbb75b8fd610cd1f76fb33dde4cde
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059842"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Connettere la macchina virtuale Qualys ad Azure Sentinel con la funzione di Azure

> [!IMPORTANT]
> Il connettore dati della VM Qualys in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore di gestione della vulnerabilità Qualys consente di connettere facilmente tutti i log della soluzione di sicurezza della [VM Qualys](https://www.qualys.com/apps/vulnerability-management/) con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra la macchina virtuale Qualys e Azure Sentinel usa funzioni di Azure per eseguire il pull dei dati di log tramite l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-qualys-vm"></a>Configurare e connettere la VM Qualys

Funzioni di Azure può integrare ed effettuare il pull di eventi e log direttamente dalla macchina virtuale Qualys e inviarli a Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **Qualys vulnerabilità Management** Connector.

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina di **gestione della vulnerabilità di Qualys** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics nella tabella **QualysHostDetection_CL** .

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere la VM Qualys ad Azure Sentinel usando le app per le funzioni di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
