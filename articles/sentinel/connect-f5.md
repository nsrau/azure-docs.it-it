---
title: Connettere i dati di F5 ASM ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare F5 ASM Data Connector per eseguire il pull di F5 log ASM in Sentinel di Azure. Visualizzare i dati di F5 ASM nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655698"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Connettere F5 ASM ad Azure Sentinel

Questo articolo illustra come usare il connettore dati F5 ASM per eseguire facilmente il pull dei log di F5 ASM in Azure Sentinel. Questo consente di visualizzare i dati di F5 ASM all'interno di cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'indagine. La presenza dei dati F5 ASM in Azure Sentinel offre maggiori informazioni sulla sicurezza delle applicazioni Web dell'organizzazione e migliora le tue funzionalità operative di sicurezza. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Configurare F5 ASM per l'invio di messaggi CEF

1. Seguire le istruzioni riportate in [F5 Configuring Application Security Event Logging](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) (F5 - Configurazione della registrazione degli eventi di sicurezza delle applicazioni) per configurare la registrazione remota, usando le linee guida seguenti:
   - Impostare **Remote storage type** (Tipo di archiviazione remota) su **CEF**.
   - Impostare **Protocol** (Protocollo) su **TCP**.
   - Impostare **IP address** (Indirizzo IP) sull'indirizzo IP del server Syslog.
   - Impostare il **numero di porta** su **514** oppure sulla porta impostata per l'uso da parte dell'agente.
   - È possibile impostare **Maximum Query String Size** (Dimensione massima della stringa di query) sulle dimensioni impostate nell'agente.

1. Per usare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.

1. Continuare con il [PASSAGGIO 3: Convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere F5 ASM ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.