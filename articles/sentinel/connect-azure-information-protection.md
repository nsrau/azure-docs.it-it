---
title: La raccolta dei dati di Azure Information Protection nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere i dati di Azure Information Protection in Sentinel di Azure.
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
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c5866d3096823f91a70b28c7c5dd1790e1b3bf8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537168"
---
# <a name="collect-data-from-azure-information-protection"></a>Raccogliere i dati da Azure Information Protection

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log dal [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) in Azure Sentinel con un solo clic. Azure Information Protection consente di proteggere i dati, sia che si trovino nel cloud o in infrastrutture locali e controllo e la Guida proteggere la posta elettronica, documenti e dati sensibili condivisi di fuori della società. Dalla classificazione facile, alle autorizzazioni e alle etichette incorporate, migliorare la protezione dei dati in qualsiasi momento con Azure Information Protection. Quando si connette Azure Information Protection per Azure Sentinel, si flusso tutti gli avvisi di Azure Information Protection a Sentinel di Azure.


## <a name="prerequisites"></a>Prerequisiti

- Utente amministratore globale, amministratore della sicurezza o delle autorizzazioni di protezione delle informazioni


## <a name="connect-to-azure-information-protection"></a>Connettersi ad Azure Information Protection

Se hai già Azure Information Protection, assicurarsi che sia [abilitata nella rete](https://docs.microsoft.com/azure/information-protection/activate-service).
Se è stato distribuito Azure Information Protection e recupero di dati, i dati dell'avviso possono facilmente essere trasmessi a Sentinel di Azure.


1. In Azure Sentinel, selezionare **raccolta di dati** e quindi fare clic sui **Azure Information Protection** riquadro.

2. Andare alla [portale di Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. Sotto **Connection**, configurare lo streaming dei log di Azure Information Protection per Azure Sentinel facendo [configurare analitica](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Selezionare l'area di lavoro in cui è stato distribuito Azure Sentinel. 

5. Fare clic su **OK**.

6. Per usare lo schema appropriato nel Log Analitica per gli avvisi di Azure Information Protection, cercare **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Information Protection per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
