---
title: Configurare la soluzione di sicurezza per connettere i dati CEF ad Anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come configurare la soluzione di sicurezza per connettere i dati CEF ad Azure Sentinel.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588451"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASSAGGIO 2: configurare la soluzione di sicurezza per l'invio di messaggi CEF

In questo passaggio si eseguiranno le modifiche di configurazione necessarie per la soluzione di sicurezza stessa per inviare i log all'agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configurare una soluzione con un connettore

Se la soluzione di sicurezza ha già un connettore esistente, usare le istruzioni specifiche del connettore come indicato di seguito:

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
Se non esiste un connettore per la soluzione di sicurezza specifica, usare le istruzioni generiche seguenti per l'invio dei log all'agente CEF.

1. Per istruzioni su come configurare la soluzione per l'invio di messaggi CEF, vedere l'articolo di configurazione specifico. Se la soluzione non è elencata, nell'appliance è necessario impostare questi valori in modo che l'appliance invii i registri necessari nel formato necessario all'agente syslog di Azure Sentinel, in base all'agente Log Analytics. È possibile modificare questi parametri nel dispositivo, purché vengano modificati anche nel daemon syslog nell'agente Sentinel di Azure.
    - Protocollo = TCP
    - Porta = 514
    - Formato = CEF
    - Indirizzo IP: assicurarsi di inviare i messaggi CEF all'indirizzo IP della macchina virtuale dedicata a questo scopo.

   > [!NOTE]
   > Questa soluzione supporta syslog RFC 3164 o RFC 5424.


1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.

1. Continuare con il passaggio 3: [convalidare la connettività](connect-cef-verify.md).

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

