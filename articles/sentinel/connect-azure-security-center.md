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
ms.openlocfilehash: b1188e533039b0137cebb22652d9921418c41deb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659653"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Connetti i dati di avviso di Azure Defender dal centro sicurezza di Azure

Usare il connettore avvisi di Azure Defender per inserire gli avvisi di Azure Defender dal [Centro sicurezza di Azure](../security-center/security-center-intro.md) ed eseguirne lo streaming in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- L'utente deve avere il ruolo di lettore di sicurezza nella sottoscrizione dei log di cui si esegue il flusso.

- È necessario abilitare Azure Defender nel centro sicurezza di Azure. (Il livello standard non esiste più e non è più un requisito di licenza).

## <a name="connect-to-azure-defender"></a>Connettersi ad Azure Defender

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta di connettori dati selezionare **Azure Defender alerts from ASC** (potrebbe essere ancora chiamato Centro sicurezza di Azure) e fare clic sul pulsante **Apri pagina connettore** .

1. In **configurazione**fare clic su **Connetti** accanto a ogni sottoscrizione i cui avvisi si vuole trasmettere in Azure Sentinel. Il pulsante Connetti sarà disponibile solo se si dispone delle autorizzazioni necessarie.

1. È possibile scegliere se si desidera che gli avvisi di Azure Defender generino automaticamente gli eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti**selezionare **abilitato** per attivare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi. È quindi possibile modificare questa regola in **Analytics**, nella scheda  **regole attive** .

1. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure Defender, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Defender ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
