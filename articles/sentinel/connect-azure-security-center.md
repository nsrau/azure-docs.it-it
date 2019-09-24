---
title: Connessione dei dati del Centro sicurezza di Azure ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati del Centro sicurezza di Azure a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240228"
---
# <a name="connect-data-from-azure-security-center"></a>Connetti i dati dal centro sicurezza di Azure





Sentinel di Azure consente di connettere gli avvisi dal [Centro sicurezza di Azure](../security-center/security-center-intro.md) e di eseguirne lo streaming in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Se si vuole esportare gli avvisi dal centro sicurezza di Azure, è necessario essere un collaboratore nella sottoscrizione di cui si esegue il flusso.

- È necessario che il [livello standard del Centro sicurezza di Azure](../security-center/security-center-pricing.md) sia in esecuzione nella sottoscrizione. In caso contrario, [aggiornare la sottoscrizione al livello standard](https://azure.microsoft.com/pricing/details/security-center/).

- È necessario accedere con un utente che disponga delle autorizzazioni di amministratore globale o di amministratore della sicurezza per ogni sottoscrizione a cui si desidera connettersi.


## <a name="connect-to-azure-security-center"></a>Connettersi al centro sicurezza di Azure

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Centro sicurezza di Azure** .

1. A destra fare clic su **Connetti** accanto a ogni sottoscrizione i cui avvisi si vuole trasmettere in Azure Sentinel. Assicurarsi di aggiornare ogni sottoscrizione al livello standard del Centro sicurezza di Azure per trasmettere avvisi ad Azure Sentinel.

1. È possibile scegliere se si desidera che gli avvisi del Centro sicurezza di Azure generino automaticamente gli eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **Abilita** per abilitare la regola analitica predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analytics** e quindi **le regole attive**.

3. Fare clic su **Connetti**.

4. Per usare lo schema pertinente in Log Analytics per gli avvisi del Centro sicurezza di Azure, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il Centro sicurezza di Azure ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
