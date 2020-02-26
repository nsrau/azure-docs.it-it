---
title: Connettere i dati del Centro sicurezza di Azure ad Azure Sentinel
description: Informazioni su come connettere i dati del Centro sicurezza di Azure a Sentinel di Azure.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588536"
---
# <a name="connect-data-from-azure-security-center"></a>Connetti i dati dal centro sicurezza di Azure





Sentinel di Azure consente di connettere gli avvisi dal [Centro sicurezza di Azure](../security-center/security-center-intro.md) e di eseguirne lo streaming in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisites

- Per esportare gli avvisi dal centro sicurezza di Azure, è necessario avere il ruolo di lettore di sicurezza nella sottoscrizione dei log di cui si esegue il flusso.

- È necessario che il [livello standard del Centro sicurezza di Azure](../security-center/security-center-pricing.md) sia in esecuzione nella sottoscrizione. In caso contrario, [aggiornare la sottoscrizione al livello standard](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Connettersi al centro sicurezza di Azure

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Centro sicurezza di Azure** .

1. A destra fare clic su **Connetti** accanto a ogni sottoscrizione i cui avvisi si vuole trasmettere in Azure Sentinel. Assicurarsi di aggiornare ogni sottoscrizione al livello standard del Centro sicurezza di Azure per trasmettere avvisi ad Azure Sentinel.

1. È possibile scegliere se si desidera che gli avvisi del Centro sicurezza di Azure generino automaticamente gli eventi imprevisti in Sentinel di Azure. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

3. Fare clic su **Connetti**.

4. Per usare lo schema pertinente in Log Analytics per gli avvisi del Centro sicurezza di Azure, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il Centro sicurezza di Azure ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
