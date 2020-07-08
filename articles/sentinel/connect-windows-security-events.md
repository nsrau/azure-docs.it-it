---
title: Connettere i dati degli eventi di sicurezza di Windows ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore degli eventi di sicurezza per trasmettere tutti gli eventi di sicurezza dai sistemi Windows all'area di lavoro di Azure Sentinel. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8da05da1b4aa9966b7743bc38b09d1c4b34c8cc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555716"
---
# <a name="connect-windows-security-events"></a>Connettere gli eventi di sicurezza di Windows 

Il connettore eventi di sicurezza consente di trasmettere tutti gli eventi di sicurezza dai sistemi Windows (server e workstation, fisici e virtuali) all'area di lavoro di Azure Sentinel. Questo consente di visualizzare gli eventi di sicurezza di Windows nei dashboard, di usarli per la creazione di avvisi personalizzati e di basarsi su di essi per migliorare le indagini, offrendo un'analisi più approfondita sulla rete dell'organizzazione e espandendo le funzionalità operative di sicurezza. È possibile selezionare gli eventi da trasmettere tra i seguenti set:<a name="event-sets"></a>

- **Tutti gli eventi** : tutti gli eventi di sicurezza di Windows e AppLocker.
- **Comune** : set standard di eventi a scopo di controllo. Un audit trail utente completo è incluso in questo set. Ad esempio, contiene gli eventi di accesso utente e di disconnessione utente (ID evento 4624, 4634). Sono inoltre disponibili azioni di controllo, ad esempio modifiche ai gruppi di sicurezza, operazioni Kerberos principali del controller di dominio e altri tipi di eventi in linea con le procedure consigliate accettate.

    Il set di eventi **comuni** può contenere alcuni tipi di eventi che non sono così comuni.  Questo è dovuto al fatto che il punto principale del set **comune** è quello di ridurre il volume degli eventi a un livello più gestibile, mantenendo al tempo stesso la capacità di audit trail completa.

- **Minimo** : un piccolo set di eventi che potrebbero indicare potenziali minacce. Questo set non contiene un audit trail completo. Vengono illustrati solo gli eventi che potrebbero indicare una violazione corretta e altri eventi importanti con una frequenza di ricorrenza molto bassa. Ad esempio, contiene gli accessi utente riusciti e non riusciti (ID evento 4624, 4625), ma non contiene informazioni di disconnessione (4634), che, mentre importante per il controllo, non sono significative per il rilevamento delle violazioni e hanno un volume relativamente elevato. La maggior parte del volume di dati di questo set è costituito da eventi di accesso ed eventi di creazione di processi (ID evento 4688).

- **None** : nessun evento di sicurezza o AppLocker. Questa impostazione viene usata per disabilitare il connettore.

    L'elenco seguente fornisce una suddivisione completa degli ID evento di sicurezza e app Locker per ogni set:

    | Set di eventi | ID evento raccolti |
    | --- | --- |
    | **Minima** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Comuni** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> La raccolta di eventi di sicurezza nel contesto di una singola area di lavoro può essere configurata dal centro sicurezza di Azure o da Azure Sentinel, ma non da entrambi. Se si sta caricando Azure Sentinel in un'area di lavoro in cui è già in esecuzione il Centro sicurezza di Azure ed è impostato per raccogliere gli eventi di sicurezza, sono disponibili due opzioni:
> - Lasciare invariata la raccolta di eventi di sicurezza nel centro sicurezza di Azure. Sarà possibile eseguire query e analizzare questi eventi in Sentinel di Azure, oltre che nel centro sicurezza di Azure. Tuttavia, non sarà possibile monitorare lo stato di connettività del connettore o modificarne la configurazione in Sentinel di Azure. Se è importante, prendere in considerazione la seconda opzione.
>
> - [Disabilitare la raccolta di eventi di sicurezza](../security-center/security-center-enable-data-collection.md) nel centro sicurezza di Azure e aggiungere il connettore degli eventi di sicurezza in Sentinel di Azure. Come per la prima opzione, sarà possibile eseguire una query e analizzare gli eventi sia in Sentinel di Azure che nel centro sicurezza di Azure, ma ora sarà possibile monitorare lo stato di connettività del connettore o modificarne la configurazione in e solo in Sentinel di Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Configurare il connettore per gli eventi di sicurezza di Windows

Per raccogliere gli eventi di sicurezza di Windows in Sentinel di Azure:

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**. Dall'elenco dei connettori, fare clic su **eventi di sicurezza**e quindi sul pulsante **Apri pagina connettore** in basso a destra. Seguire quindi le istruzioni visualizzate nella scheda **istruzioni** , come descritto nella parte restante di questa sezione.

1. Verificare di disporre delle autorizzazioni appropriate, come descritto in **prerequisiti**.

1. Scaricare e installare l' [agente di log Analytics](../azure-monitor/platform/log-analytics-agent.md) (noto anche come Microsoft Monitoring Agent o MMA) nei computer per i quali si vogliono trasmettere gli eventi di sicurezza in Sentinel di Azure.

    Per macchine virtuali di Azure:
    
    1. Fare clic su **Installa agente in una macchina virtuale Windows di Azure**, quindi sul collegamento visualizzato di seguito.
    1. Per ogni macchina virtuale che si desidera connettere, fare clic sul relativo nome nell'elenco visualizzato a destra, quindi fare clic su **Connetti**.

    Per i computer Windows non Azure (fisici, locali virtuali locali o virtuali in un altro cloud):

    1. Fare clic su **Installa agente in un computer Windows non Azure**, quindi sul collegamento visualizzato di seguito.
    1. Fare clic sui collegamenti di download appropriati visualizzati a destra in **computer Windows**.
    1. Utilizzando il file eseguibile scaricato, installare l'agente nei sistemi Windows scelti e configurarlo utilizzando l' **ID e le chiavi dell'area di lavoro** visualizzati sotto i collegamenti di download indicati in precedenza.

    > [!NOTE]
    >
    > Per consentire ai sistemi Windows senza la connettività Internet necessaria di trasmettere gli eventi ad Azure Sentinel, scaricare e installare il **gateway OMS** in un computer separato, usando il collegamento in basso a destra, per fungere da proxy.  Sarà comunque necessario installare l'agente Log Analytics in ogni sistema Windows di cui si desidera raccogliere gli eventi.
    >
    > Per ulteriori informazioni su questo scenario, vedere la [documentazione del **gateway log Analytics** ](../azure-monitor/platform/gateway.md).

    Per altre opzioni di installazione e ulteriori informazioni, vedere la documentazione di [ **log Analytics Agent** ](../azure-monitor/platform/agent-windows.md).

1. Selezionare il set di eventi ([tutti, comune o minimo](#event-sets)) che si desidera trasmettere in streaming.

1. Fare clic su **Update**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi di sicurezza di Windows, digitare `SecurityEvent` nella finestra query.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari circa 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere gli eventi di sicurezza di Windows ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate](tutorial-detect-threats-custom.md) .

