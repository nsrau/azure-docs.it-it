---
title: La raccolta di dati del Centro sicurezza di Azure nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere i dati di Centro sicurezza di Azure in Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242274"
---
# <a name="collect-data-from-azure-security-center"></a>Raccogliere dati dal Centro sicurezza di Azure

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Sentinel Azure puoi raccogliere avvisi da [Centro sicurezza di Azure](../security-center/security-center-intro.md) e trasmetterli in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Se si desidera esportare gli avvisi dal Centro sicurezza di Azure, è necessario essere un collaboratore della sottoscrizione di cui si esegue lo streaming di log.

- È necessario disporre di [livello Standard di Centro sicurezza di Azure](../security-center/security-center-pricing.md) in esecuzione nella sottoscrizione. In caso contrario, [aggiornare la sottoscrizione al livello standard](https://azure.microsoft.com/pricing/details/security-center/).

- È necessario accedere con un account utente con autorizzazioni di sicurezza amministratore o amministratore globale per ogni sottoscrizione che si desidera connettersi.


## <a name="connect-to-azure-security-center"></a>Connettersi al Centro sicurezza di Azure

1. In Azure Sentinel, selezionare **raccolta di dati** e quindi fare clic sui **Centro sicurezza di Azure** riquadro.
1. Nella finestra di destra, fare clic su **Connect** accanto a ogni sottoscrizione di cui si vuole trasmettere in Azure Sentinel gli avvisi. Assicurarsi di eseguire l'aggiornamento al livello Standard di Centro sicurezza di Azure per gli avvisi di flusso di Azure Sentinel ogni sottoscrizione.

3. Fare clic su **Connetti**.

4. Per usare lo schema appropriato nel Log Analitica per gli avvisi del Centro sicurezza di Azure, cercare **SecurityEvent**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di Centro sicurezza di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
