---
title: Connettere i dati Cloud App Security ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Cloud App Security dati ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: 95e38cee8f6995e09dbbb2194cd5a9d0ebc301c1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498660"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 



È possibile trasmettere i log da [cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) in Sentinel di Azure con un solo clic. Questa connessione consente di trasmettere gli avvisi da Cloud App Security in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- Per eseguire lo streaming di log Cloud Discovery in Sentinel di Azure, [abilitare Azure Sentinel come Siem in Microsoft cloud app Security](aka.ms. https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> L'inserimento dei log di Cloud Discovery è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Connetti a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuita e si inseriscono i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati**, fare clic sul riquadro **cloud app Security** e selezionare **Apri pagina connettore**.

1. Selezionare i log di cui si vuole eseguire lo streaming in Sentinel di Azure. è possibile scegliere **avvisi** e **log cloud Discovery** (anteprima). 

1. Fare clic su **Connetti**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Cloud App Security, cercare **SecurityAlert**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Cloud App Security ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
