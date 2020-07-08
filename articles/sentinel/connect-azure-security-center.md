---
title: Connettere i dati del Centro sicurezza di Azure ad Azure Sentinel
description: Informazioni su come connettere gli avvisi dal livello standard del Centro sicurezza di Azure e trasmettere tali avvisi in Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559155"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Connetti i dati dal centro sicurezza di Azure (ASC)

Sentinel di Azure consente di connettere gli avvisi dal [Centro sicurezza di Azure](../security-center/security-center-intro.md) e di eseguirne lo streaming in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Per esportare gli avvisi dal centro sicurezza di Azure, è necessario avere il ruolo di lettore di sicurezza nella sottoscrizione dei log di cui si esegue il flusso.

- È necessario che il [livello standard del Centro sicurezza di Azure](../security-center/security-center-pricing.md) sia in esecuzione nella sottoscrizione. In caso contrario, [aggiornare la sottoscrizione al livello standard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Connettersi al centro sicurezza di Azure

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta di connettori dati selezionare **Centro sicurezza di Azure**e fare clic sul pulsante **Apri pagina connettore** .

1. In **configurazione**fare clic su **Connetti** accanto a ogni sottoscrizione i cui avvisi si vuole trasmettere in Azure Sentinel. Il pulsante Connetti sarà disponibile solo se si dispone delle autorizzazioni necessarie e della sottoscrizione al livello standard ASC.

1. È possibile scegliere se si desidera che gli avvisi del Centro sicurezza di Azure generino automaticamente gli eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti**selezionare **abilitato** per attivare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi. È quindi possibile modificare questa regola in **Analytics**, nella scheda **regole attive** .

1. Per usare lo schema pertinente in Log Analytics per gli avvisi del Centro sicurezza di Azure, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il Centro sicurezza di Azure ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
