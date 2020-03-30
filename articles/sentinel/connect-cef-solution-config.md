---
title: Configurare la soluzione di sicurezza per connettere i dati CEF ad Azure Sentinel Preview Documenti Microsoft
description: Informazioni su come configurare la soluzione di sicurezza per connettere i dati CEF ad Azure Sentinel.Learn how to configure your security solution to connect CEF data to Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588451"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>Passaggio 2: Configurare la soluzione di sicurezza per l'invio di messaggi CEF

In questo passaggio verranno eseguite le modifiche di configurazione necessarie per la soluzione di protezione stessa per inviare i log all'agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configurare una soluzione con un connettore

Se la soluzione di sicurezza dispone già di un connettore esistente, utilizzare le istruzioni specifiche del connettore come segue:

- [Punto di controllo](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Configurare qualsiasi altra soluzione
Se non esiste un connettore per la soluzione di protezione specifica, utilizzare le istruzioni generiche seguenti per l'inoltro dei log all'agente CEF.

1. Passare all'articolo sulla configurazione specifica per la procedura di configurazione della soluzione per l'invio di messaggi CEF. Se la soluzione non è elencata, nell'appliance è necessario impostare questi valori in modo che l'appliance invii i log necessari nel formato necessario all'agente Syslog di Azure Sentinel, in base all'agente Log Analytics. È possibile modificare questi parametri nell'appliance, purché vengano modificati anche nel daemon Syslog nell'agente di Azure Sentinel.You can modify these parameters in your appliance, as long as you also modify them in the Syslog daemon on the Azure Sentinel agent.
    - Protocollo - TCP
    - Porta - 514
    - Formato: CEF
    - Indirizzo IP: assicurarsi di inviare i messaggi CEF all'indirizzo IP della macchina virtuale dedicata a questo scopo.

   > [!NOTE]
   > Questa soluzione supporta Syslog RFC 3164 o RFC 5424.


1. Per utilizzare lo schema pertinente in Log `CommonSecurityLog`Analytics per gli eventi CEF, cercare .

1. Procedere al passo 3: [convalidare la connettività](connect-cef-verify.md).

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect CEF appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

