---
title: Connettere i dati degli eventi di sicurezza di Windows ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati degli eventi di sicurezza di Windows ad Azure Sentinel.Learn how to connect Windows security event data to Azure Sentinel.
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
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124975"
---
# <a name="connect-windows-security-events"></a>Connettere gli eventi di sicurezza di Windows 

Il connettore Eventi di sicurezza consente di trasmettere tutti gli eventi di sicurezza dai sistemi Windows (server e workstation, fisici e virtuali) all'area di lavoro di Azure Sentinel. In questo modo è possibile visualizzare gli eventi di sicurezza di Windows nei dashboard, utilizzarli nella creazione di avvisi personalizzati e fare affidamento su di essi per migliorare le indagini, offrendo maggiori informazioni sulla rete dell'organizzazione ed espandendo le operazioni di sicurezza Funzionalità. È possibile selezionare gli eventi da trasmettere tra i seguenti set:<a name="event-sets"></a>

- **Tutti gli eventi:** tutti gli eventi di sicurezza di Windows e AppLocker.All events - All Windows security and AppLocker events.
- **Comune:** un set standard di eventi a scopo di controllo. In questo set è incluso un audit trail completo dell'utente. Ad esempio, contiene sia gli eventi di accesso dell'utente che gli eventi di disconnessione dell'utente (ID evento 4624, 4634). Sono inoltre disponibili azioni di controllo, ad esempio modifiche ai gruppi di sicurezza, operazioni Kerberos del controller di dominio chiave e altri tipi di eventi in linea con le procedure consigliate accettate.

    Il set di eventi **comuni** può contenere alcuni tipi di eventi che non sono così comuni.  Questo perché il punto principale del set **comune** è quello di ridurre il volume di eventi a un livello più gestibile, pur mantenendo la piena funzionalità di audit trail.

- **Minimo:** un piccolo set di eventi che potrebbero indicare potenziali minacce. Questo set non contiene un audit trail completo. Copre solo gli eventi che potrebbero indicare una violazione riuscita e altri eventi importanti che hanno tassi di occorrenza molto bassi. Ad esempio, contiene accessi utente riusciti e non riusciti (ID evento 4624, 4625), ma non contiene informazioni di disconnessione (4634) che, sebbene importanti per il controllo, non sono significative per il rilevamento delle violazioni e hanno un volume relativamente elevato. La maggior parte del volume di dati di questo set è costituita da eventi di accesso ed eventi di creazione del processo (ID evento 4688).

- **Nessuno:** nessun evento di sicurezza o AppLocker. Questa impostazione viene utilizzata per disabilitare il connettore.

    L'elenco seguente fornisce una suddivisione completa degli ID evento Security e App Locker per ogni set:

    | Set di eventi | ID evento raccolti |
    | --- | --- |
    | **Minime** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Comune** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> La raccolta di eventi di sicurezza nel contesto di una singola area di lavoro può essere configurata dal Centro sicurezza di Azure o da Azure Sentinel, ma non da entrambe. Se si esegue l'onboarding di Azure Sentinel in un'area di lavoro che esegue già Il Centro sicurezza di Azure ed è impostato per raccogliere gli eventi di sicurezza, sono disponibili due opzioni:If you are onboarding Azure Sentinel in a workspace that is already running Azure Security Center, and is set to collect Security Events, you have two options:
> - Lasciare la raccolta Eventi di sicurezza nel Centro sicurezza di Azure così com'è. Sarà possibile eseguire query e analizzare questi eventi in Azure Sentinel e nel Centro sicurezza di Azure.You will be able to query and analyze these events in Azure Sentinel as well as in Azure Security Center. Tuttavia, non sarà possibile monitorare lo stato di connettività del connettore o modificarne la configurazione in Azure Sentinel.You will not, however, be able to monitor the connector's connectivity status or change its configuration in Azure Sentinel. Se questo è importante per voi, prendere in considerazione la seconda opzione.
>
> - [Disabilitare la raccolta di eventi](../security-center/security-center-enable-data-collection.md) di sicurezza nel Centro sicurezza di Azure e solo in seguito aggiungere il connettore Eventi di sicurezza in Azure Sentinel.Disable Security Events collection in Azure Security Center, and only then add the Security Events connector in Azure Sentinel. Come per la prima opzione, sarà possibile eseguire query e analizzare gli eventi sia in Azure Sentinel che nel Centro sicurezza di Azure, ma ora sarà possibile monitorare lo stato di connettività del connettore o modificarne la configurazione in Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurare il connettore Eventi di sicurezza di Windows

Per raccogliere gli eventi di sicurezza di Windows in Azure Sentinel:To collect your Windows security events in Azure Sentinel:

1. Nel menu di spostamento di Azure Sentinel selezionare **Connettori dati**. Nell'elenco dei connettori fare clic su **Eventi**di protezione , quindi sul pulsante **Apri pagina connettore** in basso a destra. Seguire quindi le istruzioni visualizzate nella scheda **Istruzioni,** come descritto nella parte restante di questa sezione.

1. Verificare di disporre delle autorizzazioni appropriate come descritto in **Prerequisiti**.

1. Scaricare e installare [l'agente di Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (noto anche come Microsoft Monitoring Agent o MMA) nei computer per cui si desidera trasmettere gli eventi di sicurezza in Azure Sentinel.

    Per macchine virtuali di Azure:For Azure Virtual Machines:
    
    1. Fare clic su **Installa agente in Macchina virtuale Windows di Azure**e quindi sul collegamento visualizzato di seguito.
    1. Per ogni macchina virtuale che si desidera connettere, fare clic sul relativo nome nell'elenco visualizzato a destra e quindi fare clic su **Connetti**.

    Per computer Windows non Azure (fisici, virtuali locali o virtuali in un altro cloud):For non-Azure Windows machines (physical, virtual on-prem, or virtual in another cloud):

    1. Fare clic su **Installa agente in Windows Machine non Azure**e quindi sul collegamento visualizzato di seguito.
    1. Fare clic sui collegamenti di download appropriati visualizzati a destra, in **Computer Windows**.
    1. Utilizzando il file eseguibile scaricato, installare l'agente nei sistemi Windows desiderati e configurarlo utilizzando **l'ID area di lavoro e le chiavi** visualizzati sotto i collegamenti di download sopra indicati.

    > [!NOTE]
    >
    > Per consentire ai sistemi Windows senza la connettività Internet necessaria di trasmettere ancora eventi ad Azure Sentinel, scaricare e installare **OMS Gateway** in un computer separato, usando il collegamento in basso a destra, per fungere da proxy.  Sarà comunque necessario installare l'agente di Log Analytics in ogni sistema Windows di cui si desidera raccogliere gli eventi.
    >
    > Per ulteriori informazioni su questo scenario, vedere la [documentazione del **gateway di Log Analytics** ](../azure-monitor/platform/gateway.md).

    Per ulteriori opzioni di installazione e ulteriori dettagli, vedere la [documentazione **dell'agente log Analytics** ](../azure-monitor/platform/agent-windows.md).

1. Selezionare il set di eventi ([Tutto, Comune o Minimo](#event-sets)) che si desidera trasmettere in streaming.

1. Fare clic su **Update**.

1. Per usare lo schema pertinente in `SecurityEvent` Log Analytics per gli eventi di sicurezza di Windows, digitare nella finestra della query.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere voluti circa 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Windows security events to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate.](tutorial-detect-threats-custom.md)

