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
ms.openlocfilehash: 32e7e79465b68caca9a636019f65b9f08284fc72
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928223"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 



È possibile trasmettere i log da [cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) in Sentinel di Azure con un solo clic. Questa connessione consente di trasmettere gli avvisi da Cloud App Security in Sentinel di Azure. 

## <a name="prerequisites"></a>prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- Per eseguire lo streaming di log Cloud Discovery in Sentinel di Azure, [abilitare Azure Sentinel come Siem in Microsoft cloud app Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> L'inserimento dei log di Cloud Discovery è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Connetti a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuita e si inseriscono i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati**, fare clic sul riquadro **cloud app Security** e selezionare **Apri pagina connettore**.

1. Selezionare i log di cui si vuole eseguire lo streaming in Sentinel di Azure. è possibile scegliere **avvisi** e **log cloud Discovery** (anteprima). 

1. Fare clic su **Connect**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Cloud App Security, cercare **SecurityAlert**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Cloud App Security ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
