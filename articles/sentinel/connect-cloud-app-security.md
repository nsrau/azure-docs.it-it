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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588366"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 



È possibile trasmettere i log da [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) in Azure Sentinel con un solo clic. Questa connessione consente di trasmettere gli avvisi da Cloud App Security in Azure Sentinel.This connection enables you to stream the alerts from Cloud App Security into Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- Per trasmettere i log di Cloud Discovery in Azure Sentinel, [abilitare Azure Sentinel come SIEM in Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> L'inserimento dei log di Cloud Discovery è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Connettersi a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuito e inserisce i dati, i dati degli avvisi possono essere facilmente trasmessi in Azure Sentinel.


1. In Azure Sentinel selezionare **Connettori dati**, fare clic sul riquadro **Cloud App Security** e selezionare Apri pagina **connettore**.

1. Selezionare i log da trasmettere in Azure Sentinel, è possibile scegliere **Avvisi** e **log di Cloud Discovery** (anteprima). 

1. Fare clic su **Connetti**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Cloud App Security, cercare **SecurityAlert**.




## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Microsoft Cloud App Security to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
