---
title: Connettere i dati degli eventi di sicurezza di Windows ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati degli eventi di sicurezza di Windows ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: c74cc5cc65f16eb38ead7c09b5e662cd2463af35
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555276"
---
# <a name="connect-windows-security-events"></a>Connettere gli eventi di sicurezza di Windows 



È possibile trasmettere tutti gli eventi di sicurezza dai server Windows connessi all'area di lavoro di Azure Sentinel. Questa connessione consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza.  È possibile selezionare gli eventi da trasmettere:

- **Tutti gli eventi** : tutti gli eventi di sicurezza di Windows e AppLocker.
- **Comune** : set standard di eventi a scopo di controllo. Un audit trail utente completo è incluso in questo set. Ad esempio, questo set contiene gli eventi di accesso utente e di disconnessione utente (ID evento 4634). Sono incluse azioni di controllo quali modifiche al gruppo di sicurezza, operazioni Kerberos del controller del dominio principale e altri eventi consigliati dalle organizzazioni del settore.

Gli eventi che hanno un volume molto basso sono stati inclusi nell'insieme Comuni. Questo insieme è stato scelto perché l'obiettivo è quello di ridurre il volume, non di filtrare eventi specifici.
- **Minimo** : un piccolo set di eventi che potrebbero indicare potenziali minacce. Abilitando questa opzione, non sarà possibile disporre di un audit trail completo.  Questo set riguarda solo gli eventi che potrebbero indicare una violazione riuscita ed eventi importanti che hanno un volume molto basso. Questo set, ad esempio, contiene l'account di accesso con esito positivo e negativo (ID evento 4624, 4625), ma non contiene informazioni di disconnessione, che è importante per il controllo, ma non significative per il rilevamento e ha un volume relativamente elevato. La maggior parte del volume di dati di questo set è costituito dagli eventi di accesso e dall'evento di creazione del processo (ID evento 4688).
- **None** : nessun evento di sicurezza o AppLocker.

> [!NOTE]
> 
> - I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.
> - Se il Centro sicurezza di Azure e Azure Sentinel sono in esecuzione nella stessa area di lavoro, il connettore degli eventi di sicurezza può essere connesso solo dal centro sicurezza di Azure o da Azure Sentinel. Per gestire questi eventi da Azure Sentinel, è consigliabile disconnetterli dal centro sicurezza di Azure e connetterli solo a Sentinel di Azure.


L'elenco seguente fornisce una suddivisione completa degli ID evento di sicurezza e app Locker per ogni set:

| Livello dati | Indicatori di eventi raccolti |
| --- | --- |
| Minime | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comune | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Configurare il connettore per gli eventi di sicurezza di Windows

Per integrare completamente gli eventi di sicurezza di Windows con Azure Sentinel:

1. Nel portale di Azure Sentinel selezionare **connettori dati** e quindi fare clic sul riquadro **eventi di sicurezza** . 
1. Consente di selezionare i tipi di dati di cui si desidera eseguire il flusso.
1. Fare clic su **Update**.
6. Per utilizzare lo schema pertinente in Log Analytics per gli eventi di sicurezza di Windows, cercare **SecurityEvent**.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari circa 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere gli eventi di sicurezza di Windows ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

