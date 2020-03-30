---
title: Connettere i dati di Web Application Firewall ad Azure Sentinel
description: Informazioni su come connettere i dati del firewall delle applicazioni Web Microsoft ad Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588179"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Connettere i dati dal firewall delle applicazioni Web Microsoft



È possibile trasmettere i log dal firewall dell'applicazione Web Microsoft di Azure Application Gateway. Questo WAF protegge le applicazioni da vulnerabilità Web comuni come SQL injection e cross-site scripting e consente di personalizzare le regole per ridurre i falsi positivi. Seguire queste istruzioni per trasmettere i log del firewall dell'applicazione Web Microsoft in Azure Sentinel.Follow these instructions to stream your Microsoft Web application firewall logs into Azure Sentinel.


## <a name="prerequisites"></a>Prerequisiti

- Una risorsa gateway applicazione esistente

## <a name="connect-to-microsoft-web-application-firewall"></a>Connettersi al firewall delle applicazioni Web Microsoft

Se si dispone già di Microsoft Web Application Firewall, assicurarsi di disporre di una risorsa gateway esistente.
Dopo aver distribuito il firewall dell'applicazione Web Microsoft e ottenuto i dati, i dati degli avvisi possono essere facilmente trasmessi in Azure Sentinel.
    
1. Nel portale di Azure Sentinel selezionare **Connettori dati**.
1. Nella pagina Connettori dati selezionare il riquadro **WAF.**
1. Passare alla [risorsa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) gateway applicazione e scegliere il WAF.
    1. Selezionare **Impostazioni di diagnostica**.
    1. Selezionare **: Aggiungi impostazione diagnostica** sotto la tabella.
    1. Nella pagina **Impostazioni di diagnostica** digitare un **Nome** e selezionare Invia a **Log Analytics**.
    1. In Log Analytics Workspace selezionare l'area di lavoro di Azure Sentinel.Under **Log Analytics Workspace** select the Azure Sentinel workspace.
    1. Selezionare i tipi di log che si desidera analizzare. È consigliabile: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.We recommended: ApplicationGatewayAccessLog and ApplicationGatewayFirewallLog.
1. Per usare lo schema pertinente in Log Analytics per gli avvisi del firewall dell'applicazione Web Microsoft, cercare **AzureDiagnostics**.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Microsoft web application firewall to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
