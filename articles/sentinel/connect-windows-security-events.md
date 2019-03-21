---
title: Raccogliere dati di eventi di sicurezza di Windows nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati di eventi di sicurezza di Windows in Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3c79747bf33e1769af5f8d3589904ba15105f216
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087602"
---
# <a name="connect-windows-security-events"></a>Connettere gli eventi di sicurezza di Windows 

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
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

1. Nel portale di Azure Sentinel, selezionare **raccolta di dati** e quindi fare clic sui **gli eventi di sicurezza di Windows** riquadro. 
1. Selezionare i tipi di dati da trasmettere in streaming.
1. Fare clic su **Update**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbe richiedere circa 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere gli eventi di sicurezza di Windows per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

