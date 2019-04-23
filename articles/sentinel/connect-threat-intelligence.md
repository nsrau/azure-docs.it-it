---
title: Connettere i dati di intelligence sulle minacce all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di intelligence sulle minacce per Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a33b00e4e553824f1f719be2c8518487241f0a11
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786185"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connetti dati dal provider di threat intelligence 

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dopo che il flusso di dati in Azure Sentinel, è possibile arricchire con i feed di intelligence per le minacce che usano all'interno dell'organizzazione. 

Per poter incrociato controllare gli avvisi e le regole con intelligence per le minacce true, ad esempio se viene visualizzato un avviso da un indirizzo IP specifico, l'integrazione di provider di threat intelligence sarà in grado di consentono di sapere se quell'indirizzo IP di recente è stato trovato per essere dannose , Consente l'integrazione con azure Sentinel [provider di intelligence per le minacce](https://aka.ms/graphsecuritytips). 

È possibile trasmettere i log dai provider di Threat intelligence in Azure Sentinel con un solo clic. Questa connessione consente di incorporare gli indicatori contenente vari tipi di oggetti Observable, ad esempio indirizzo IP, dominio, URL e hash file per la ricerca e creare avvisi delle regole in Azure Sentinel.  
> [!NOTE]
> È possibile immettere gli indicatori di minaccia personalizzato in Azure Sentinel per l'uso in scenari di ricerca, dashboard e le regole di avviso grazie all'integrazione con il [Microsoft Graph Security tiIndicator](https://aka.ms/graphsecuritytiindicators) entità oppure usando un [Microsoft Grafico sicurezza integrata Threat Intelligence Platform](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Prerequisiti  

- Utenti con autorizzazioni di sicurezza amministratore o amministratore globale 

- Applicazione di Threat intelligence integrato con Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Connettersi all'intelligence per le minacce 

1. Se si usa già un provider di intelligence delle minacce, assicurarsi di passare all'applicazione suggerimento e concedere l'autorizzazione per inviare gli indicatori a Microsoft e specificare il servizio come Sentinel di Azure.  

2. In Azure Sentinel, selezionare **connettori di dati** e quindi fare clic sui **Threat Intelligence** riquadro.

3. Fare clic su **Connetti**. 

4. Per usare lo schema appropriato nel Log Analitica per i feed di intelligence delle minacce, cercare **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come connettere il provider di Intelligence sulle minacce per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti.

- Per iniziare a usare Azure Sentinel, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Informazioni su come [caricare i dati in Azure Sentinel](quickstart-onboard.md) e [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
