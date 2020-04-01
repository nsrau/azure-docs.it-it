---
title: Connettere i dati di Cloud App Security ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di Cloud App Security ad Azure Sentinel.
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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422149"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 



Il connettore [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) consente di trasmettere avvisi e log di Cloud [Discovery](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) da MCAS in Azure Sentinel. Questo ti permetterà di ottenere visibilità sulle tue app cloud, ottenere analisi sofisticate per identificare e combattere le minacce informatiche e controllare il modo in cui viaggiano i tuoi dati.

## <a name="prerequisites"></a>Prerequisiti

- L'utente deve disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro.
- L'utente deve disporre delle autorizzazioni amministratore globale o amministratore della sicurezza nel tenant dell'area di lavoro.
- Per trasmettere i log di Cloud Discovery in Azure Sentinel, [abilitare Azure Sentinel come SIEM in Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> L'inserimento dei log di Cloud Discovery è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Connettersi a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuito e inserisce i dati, i dati degli avvisi possono essere facilmente trasmessi in Azure Sentinel.


1. Nel menu di spostamento di Azure Sentinel selezionare **Connettori dati**. Nell'elenco dei connettori fare clic sul riquadro **Microsoft Cloud App Security** e quindi sul pulsante Apri pagina **connettore** in basso a destra.

1. Selezionare i log che si desidera trasmettere in Azure Sentinel; è possibile scegliere **Avvisi** e **Registri di individuazione cloud** (anteprima). 

1. Fare clic su **Applica modifiche**.

1. Per usare lo schema pertinente negli `SecurityAlert` avvisi di Log Analytics for Cloud App Security, digitare nella finestra della query. Per lo schema dei `McasShadowItReporting`log di Cloud Discovery digitare .

> [!NOTE]
> Cloud Discovery consente di rilevare e identificare le tendenze aggregando le connessioni degli utenti sottostanti alle app cloud.
>
> Poiché i dati di Cloud Discovery vengono aggregati al giorno, tenere presente che fino a 24 ore dei dati più recenti non si rifletteranno in Azure Sentinel. Nel caso in cui un'indagine di basso livello richieda dati più immediati, deve essere eseguita direttamente nell'appliance o nel servizio di origine in cui risiedono i dati non elaborati.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Microsoft Cloud App Security to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats.md) o [personalizzate.](tutorial-detect-threats-custom.md)
