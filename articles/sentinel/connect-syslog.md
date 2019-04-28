---
title: Connetti i dati Syslog a Sentinel anteprima di Azure | Microsoft Docs
description: Informazioni su come connettere dati Syslog a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9144f20eae5a925ad88054e972c99ae024bcefe3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714660"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connettere la soluzione esterna tramite Syslog

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettere qualsiasi appliance in locale che supporta il Syslog per Sentinel di Azure. Questa operazione viene eseguita tramite un agente basato su una macchina Linux tra il dispositivo e Sentinel di Azure. Se il computer Linux in Azure, è possibile trasmettere i log dal dispositivo o applicazione in un'area di lavoro dedicato creare in Azure e connetterla. Se la macchina Linux non è disponibile in Azure, è possibile trasmettere i log da dispositivo a un oggetto dedicato in locale della macchina virtuale o computer in cui si installa l'agente per Linux. 

> [!NOTE]
> Se l'appliance supporta CEF Syslog, è più completa, la connessione ed è consigliabile scegliere questa opzione e seguire le istruzioni in [la connessione dati da CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Funzionamento

Connessione di Syslog viene eseguita utilizzando un agente per Linux. Per impostazione predefinita, l'agente per Linux riceve gli eventi dal daemon Syslog su UDP, ma nei casi in cui è previsto un computer Linux per raccogliere un numero elevato di eventi Syslog, ad esempio quando un agente Linux si riceve eventi da altri dispositivi, la configurazione viene modificata in Utilizzare il trasporto TCP tra il daemon Syslog e l'agente.

## <a name="connect-your-syslog-appliance"></a>Connettersi all'appliance di Syslog

1. Nel portale di Azure Sentinel, selezionare **connettori di dati** e scegliere il **Syslog** riquadro.
2. Se la macchina Linux non è presente all'interno di Azure, scaricare e installare il Azure Sentinel **agente per Linux** nell'appliance. 
1. Se Usa Azure, selezionare o creare una macchina virtuale che nell'area di lavoro Sentinel Azure dedicato alla ricezione dei messaggi Syslog. Selezionare la macchina virtuale in Azure Sentinel aree di lavoro e fare clic su **Connect** nella parte superiore del riquadro di sinistra.
3. Fare clic su **configurare i log da connettere** nuovamente nel programma di installazione di connector Syslog. 
4. Fare clic su **fare clic qui per aprire il pannello configurazione**.
1. Selezionare **Data** e quindi **Syslog**.
   - Assicurarsi che ogni struttura che si sta inviando da Syslog sia nella tabella. Per ogni impianto si desidera monitorare, impostare un livello di gravità. Fare clic su **Apply**.
1. Nella macchina di Syslog, assicurarsi che si sta inviando tali strutture. 

3. Per usare lo schema appropriato nel Log Analitica per i log di Syslog, cercare **Syslog**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di Appliance locali Syslog. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
