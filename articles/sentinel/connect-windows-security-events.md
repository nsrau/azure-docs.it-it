---
title: Connettere i dati evento di sicurezza di Windows all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati evento di sicurezza di Windows a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 88b066818d53fd92e8238e270b9bc785d4275186
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233976"
---
# <a name="connect-windows-security-events"></a>Connettere gli eventi di sicurezza di Windows 

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere tutti gli eventi di sicurezza dai server Windows connessi all'area di lavoro Azure Sentinel. Questa connessione consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo ti offre informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza.  È possibile selezionare gli eventi per lo streaming:

- **Tutti gli eventi** -protezione di tutti i Windows e gli eventi di AppLocker.
- **Common** -un set standard di eventi a scopo di controllo.
- **Minimo** -un piccolo set di eventi che possono indicare potenziali minacce. Abilitando questa opzione, sarà possibile avere un audit trail completo.
- **Nessuno** -alcuna sicurezza o gli eventi di AppLocker.

> [!NOTE]
> 
> - I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui in esecuzione Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurare il connettore di eventi di sicurezza di Windows

Integrare completamente gli eventi di sicurezza di Windows con Azure Sentinel:

1. Nel portale di Azure Sentinel, selezionare **connettori di dati** e quindi fare clic sui **gli eventi di sicurezza di Windows** riquadro. 
1. Selezionare i tipi di dati da trasmettere in streaming.
1. Fare clic su **Update**.
6. Per usare lo schema appropriato nel Log Analitica per gli eventi di sicurezza di Windows, cercare **SecurityEvent**.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbe richiedere circa 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere gli eventi di sicurezza di Windows per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

