---
title: Raccogliere i dati di Cloud App Security nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere i dati di Cloud App Security in Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245489"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>Raccogliere i dati da Microsoft Cloud App Security 

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log dal [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) in Azure Sentinel con un solo clic. Questa connessione consente di trasmettere gli avvisi da Cloud App Security in Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- Utenti con autorizzazioni di sicurezza amministratore o amministratore globale

## <a name="connect-to-cloud-app-security"></a>Connettersi a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitata nella rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuito e l'inserimento dei dati, i dati di avviso possono facilmente essere trasmessi in Sentinel di Azure.


1. In Azure Sentinel, selezionare **raccolta di dati** e quindi fare clic sui **Cloud App Security** riquadro.

2. Fare clic su **Connetti**.

3. Per usare lo schema appropriato nel Log Analitica per gli avvisi di Cloud App Security, cercare **SecurityAlert**.


## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Microsoft Cloud App Security per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
