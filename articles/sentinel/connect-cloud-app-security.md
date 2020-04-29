---
title: Connettere i dati Cloud App Security ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Cloud App Security dati ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422149"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 



Il connettore [Microsoft cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) consente di trasmettere avvisi e [cloud Discovery log](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) da MCAS in Sentinel di Azure. Questo consentirà di ottenere visibilità sulle app Cloud, ottenere analisi sofisticate per identificare e combattere Cyberthreats e controllare il modo in cui i dati vengono trasmessi.

## <a name="prerequisites"></a>Prerequisiti

- L'utente deve disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro.
- L'utente deve disporre delle autorizzazioni di amministratore globale o di amministratore della sicurezza sul tenant dell'area di lavoro.
- Per eseguire lo streaming di log Cloud Discovery in Sentinel di Azure, [abilitare Azure Sentinel come Siem in Microsoft cloud app Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> L'inserimento dei log di Cloud Discovery è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Connettersi a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuita e si inseriscono i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**. Nell'elenco dei connettori fare clic sul riquadro **Microsoft cloud app Security** e quindi sul pulsante **Apri pagina connettore** in basso a destra.

1. Selezionare i log di cui si vuole eseguire lo streaming in Sentinel di Azure. è possibile scegliere **avvisi** e **log cloud Discovery** (anteprima). 

1. Fare clic su **Applica modifiche**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di `SecurityAlert` cloud app Security, digitare nella finestra query. Per lo schema dei log di Cloud Discovery `McasShadowItReporting`, digitare.

> [!NOTE]
> Cloud Discovery consente di rilevare e identificare le tendenze aggregando le connessioni degli utenti sottostanti ai dati alle app cloud.
>
> Poiché i dati Cloud Discovery sono aggregati in base al giorno, tenere presente che il valore massimo di 24 ore per i dati più recenti non verrà riflesso in Sentinel di Azure. Nel caso in cui un'indagine di basso livello richieda dati più immediati, è consigliabile eseguire questa operazione direttamente nell'appliance o nel servizio di origine in cui si trovano i dati non elaborati.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Cloud App Security ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats.md) o [personalizzate](tutorial-detect-threats-custom.md) .
