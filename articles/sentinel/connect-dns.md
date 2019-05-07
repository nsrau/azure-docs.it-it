---
title: Connetti i dati DNS nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati DNS in Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6429568b33ece3ed4f26614e55e8c3069dd65d71
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204413"
---
# <a name="connect-your-domain-name-server"></a>Connettere il server di nome di dominio

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettersi a qualsiasi Server DNS (Domain Name) in esecuzione su Windows in Azure Sentinel. Questa operazione viene eseguita installando un agente nel computer DNS. Uso di DNS di log, è possibile ottenere sicurezza, prestazioni e informazioni dettagliate relative a operazioni nell'infrastruttura DNS dell'organizzazione tramite la raccolta, l'analisi, e correlare analisi e i log di controllo e altri dati correlati dai server DNS.

Quando si abilita la connessione al log DNS è possibile:
- Identificare i client che tentano di risolvere nomi di dominio dannosi
- Identificare i record di risorse non aggiornati
- Identificare i nomi di dominio sottoposti frequentemente a query e i client DNS loquaci
- Visualizzare il carico di richieste nei server DNS
- Visualizzare gli errori di registrazione di DNS dinamici

## <a name="how-it-works"></a>Funzionamento

Connessione DNS viene eseguita installando un agente nel computer DNS. L'agente effettua il pull degli eventi dal DNS e li passa al Log Analitica.

## <a name="connect-your-dns-appliance"></a>Connettersi all'appliance DNS

1. Nel portale di Azure Sentinel, selezionare **connettori di dati** e scegliere il **DNS** riquadro.
1. Se le macchine DNS sono in Azure:
    1. Fare clic su **scaricare e installare l'agente per le macchine virtuali Windows**.
    1. Nel **macchine virtuali** elencare, selezionare la macchina DNS si vuole trasmettere in Sentinel di Azure. Assicurarsi che si tratta di una VM Windows.
    1. Nella finestra che si apre per la macchina virtuale, fare clic su **Connect**.  
    1. Fare clic su **abilitare** nel **connettore DNS** finestra. 

2. Se il computer DNS non è una macchina virtuale di Azure:
    1. Fare clic su **scaricare e installare l'agente per i computer non Azure Windows**.
    1. Nel **agente diretto** finestra, selezionare **dell'agente di Download di Windows (64 bit)** oppure **dell'agente di Download di Windows (32 bit)**.
    1. Installare l'agente nel computer di DNS. Copia il **ID area di lavoro**, **chiave primaria**, e **chiave secondaria** e usarle quando richiesto durante l'installazione.

3. Per usare lo schema appropriato nel Log Analitica per i log DNS, cercare **: eventi DNS**.

## <a name="validate"></a>Convalida 

Nel Log Analitica, cercare lo schema **: eventi DNS** e assicurarsi che vi siano eventi.

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Appliance locali DNS di Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
