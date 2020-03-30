---
title: Connettere i dati del Centro sicurezza di Azure ad Azure SentinelConnect Azure Security Center data to Azure Sentinel
description: Informazioni su come connettere i dati del Centro sicurezza di Azure ad Azure Sentinel.Learn how to connect Azure Security Center data to Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588536"
---
# <a name="connect-data-from-azure-security-center"></a>Connettere i dati dal Centro sicurezza di AzureConnect data from Azure Security Center





Azure Sentinel consente di connettere gli avvisi dal Centro sicurezza di Azure e di trasmetterli in Azure Sentinel.Azure Sentinel enables you to connect alerts from [Azure Security Center](../security-center/security-center-intro.md) and stream them into Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- Per esportare gli avvisi dal Centro sicurezza di Azure, è necessario disporre del ruolo Lettore sicurezza nella sottoscrizione dei log trasmessi.

- È necessario disporre del [livello Standard del Centro sicurezza di Azure](../security-center/security-center-pricing.md) in esecuzione nella sottoscrizione. In caso contrario, [aggiornare l'abbonamento allo standard](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Connettersi al Centro sicurezza di AzureConnect to Azure Security Center

1. In Azure Sentinel selezionare Connettori dati e quindi fare clic sul riquadro **Centro sicurezza di Azure.In Azure** Sentinel, select Data **connectors** and then click the Azure Security Center tile.

1. A destra fare clic su Connetti accanto a ogni sottoscrizione di cui si vuole trasmettere gli avvisi in Azure Sentinel.in the right, click **Connect** next to each subscription whose alerts you want to stream into Azure Sentinel. Assicurarsi di aggiornare ogni sottoscrizione al livello Standard del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.Make sure to upgrade each subscription to Azure Security Center Standard tier to stream alerts to Azure Sentinel.

1. È possibile selezionare se si vuole che gli avvisi dal Centro sicurezza di Azure generino automaticamente eventi imprevisti in Azure Sentinel.You can select whether you want the alerts from Azure Security Center to automatically generate s incidents in Azure Sentinel automatically. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

3. Fare clic su **Connetti**.

4. Per usare lo schema pertinente in Log Analytics per gli avvisi del Centro sicurezza di Azure, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Azure Security Center to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
