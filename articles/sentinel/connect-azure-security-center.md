---
title: Connettere i dati di Azure Defender ad Azure Sentinel
description: Informazioni su come connettere gli avvisi di Azure Defender dal centro sicurezza di Azure ed eseguirne lo streaming in Sentinel di Azure.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: b37675d46d501fc43446eafc83d6c0a02f238177
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655953"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Connetti i dati di avviso di Azure Defender dal centro sicurezza di Azure

Usare il connettore avvisi di Azure Defender per inserire gli avvisi di Azure Defender dal [Centro sicurezza di Azure](../security-center/security-center-introduction.md) ed eseguirne lo streaming in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- L'utente deve avere il ruolo di lettore di sicurezza nella sottoscrizione dei log di cui si esegue il flusso.

- È necessario abilitare Azure Defender nel centro sicurezza di Azure. (Il livello standard non esiste più e non è più un requisito di licenza).

## <a name="connect-to-azure-defender"></a>Connettersi ad Azure Defender

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta di connettori dati selezionare **Azure Defender alerts from ASC** (potrebbe essere ancora chiamato Centro sicurezza di Azure) e fare clic sul pulsante **Apri pagina connettore** .

1. In **configurazione** fare clic su **Connetti** accanto a ogni sottoscrizione i cui avvisi si vuole trasmettere in Azure Sentinel. Il pulsante Connetti sarà disponibile solo se si dispone delle autorizzazioni necessarie.

1. È possibile scegliere se si desidera che gli avvisi di Azure Defender generino automaticamente gli eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **abilitato** per attivare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi. È quindi possibile modificare questa regola in **Analytics**, nella scheda  **regole attive** .

1. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure Defender, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Defender ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).