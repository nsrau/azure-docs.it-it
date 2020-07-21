---
title: Connettere i dati Proofpoint Targeted Attack Protection (TAP) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Proofpoint Targeted Attack Protection (TAP) ad Azure Sentinel.
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
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531468"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Connettere il Proofpoint TAP ad Azure Sentinel con la funzione di Azure

Il connettore TAP (Proofpoint Targeted Attack Protection) consente di connettere facilmente tutti i log della soluzione [PROOFPOINT Tap](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Proofpoint TAP e Azure Sentinel usa funzioni di Azure per eseguire il pull dei dati di log tramite l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-proofpoint-tap"></a>Configurare e connettere Proofpoint TAP

Funzioni di Azure è in grado di integrare ed effettuare il pull di eventi e log direttamente da Proofpoint TAP e di inviarli ad Azure Sentinel.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare connettore **Proofpoint Tap** .

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina **Tap di Proofpoint** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics nelle tabelle **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** e **ProofpointTAPClicksBlocked_CL** .

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Proofpoint TAP ad Azure Sentinel usando le app per le funzioni di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
