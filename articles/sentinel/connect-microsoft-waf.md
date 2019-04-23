---
title: Connettere i dati di Microsoft web application firewall all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Microsoft web application firewall per Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5316fa7e3aa4465349b762b99bec9171f821062f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798998"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Connetti dati dal firewall applicazione web di Microsoft

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log Microsoft web application firewall del Gateway applicazione di Azure (WAF). Il WAF protegge le applicazioni da vulnerabilità web comuni, ad esempio attacchi SQL injection e di scripting intersito e consente di personalizzare le regole per ridurre i falsi positivi. Seguire queste istruzioni per trasmettere i log di Microsoft Web application firewall in Azure Sentinel.


## <a name="prerequisites"></a>Prerequisiti

- Una risorsa del gateway applicazione esistente

## <a name="connect-to-microsoft-web-application-firewall"></a>Connettersi a Microsoft web application firewall

Se si dispone già di firewall applicazione web di Microsoft, assicurarsi di disporre di una risorsa di gateway esistente.
Una volta che viene distribuito il web application firewall di Microsoft e recupero di dati, i dati di avviso possono facilmente essere trasmessi in Sentinel di Azure.
    
1. Nel portale di Azure Sentinel, selezionare **connettori dati**.
1. Nella pagina connettori di dati, selezionare la **WAF** riquadro.
1. Passare a [risorsa Gateway applicazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) e scegliere il firewall WAF.
    1. Selezionare **le impostazioni di diagnostica**.
    1. Selezionare **+ Aggiungi impostazione di diagnostica** sotto la tabella.
    1. Nel **le impostazioni di diagnostica** , digitare un **Name** e selezionare **Invia a Log Analitica**.
    1. Sotto **dell'area di lavoro di Log Analitica** selezionare l'area di lavoro Azure Sentinel.
    1. Selezionare i tipi di log che si desidera analizzare. È consigliabile: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Per usare lo schema appropriato nel Log Analitica per gli avvisi di Microsoft web application firewall, cercare **AzureDiagnostics**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di firewall applicazione web di Microsoft. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
