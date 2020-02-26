---
title: Connettere i dati del Web Application Firewall ad Azure Sentinel
description: Informazioni su come connettere i dati di Microsoft web application firewall a Sentinel di Azure.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588179"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Connetti i dati da Microsoft web application firewall



È possibile trasmettere i log da Microsoft web application firewall (WAF) del gateway applicazione Azure. Questo WAF protegge le applicazioni da vulnerabilità Web comuni come SQL injection e cross-site scripting e consente di personalizzare le regole per ridurre i falsi positivi. Seguire queste istruzioni per eseguire lo streaming dei log di Microsoft Web Application Firewall in Sentinel di Azure.


## <a name="prerequisites"></a>Prerequisites

- Una risorsa del gateway applicazione esistente

## <a name="connect-to-microsoft-web-application-firewall"></a>Connettersi a Microsoft web application firewall

Se si dispone già di Microsoft web application firewall, assicurarsi di disporre di una risorsa gateway esistente.
Dopo aver distribuito il web application firewall Microsoft e aver ottenuto i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.
    
1. Nel portale di Azure Sentinel selezionare **connettori dati**.
1. Nella pagina connettori dati selezionare il riquadro **WAF** .
1. Passare a [risorse del gateway applicazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) e scegliere la WAF.
    1. Selezionare **Impostazioni di diagnostica**.
    1. Selezionare **+ Aggiungi impostazione diagnostica** nella tabella.
    1. Nella pagina **impostazioni di diagnostica** Digitare un **nome** e selezionare **Invia a log Analytics**.
    1. In **log Analytics area di lavoro** selezionare l'area di lavoro di Azure Sentinel.
    1. Selezionare i tipi di log che si desidera analizzare. Si consiglia: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Microsoft web application firewall, cercare **AzureDiagnostics**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft web application firewall ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
