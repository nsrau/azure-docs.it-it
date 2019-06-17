---
title: Risoluzione dei problemi relativi al Rilevamento modifiche di Azure
description: Questo articolo contiene informazioni sulla risoluzione dei problemi di Rilevamento modifiche
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a6610b5cb3f01fc70b1737fc4492e09d9a7637b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61085327"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Risolvere i problemi di Rilevamento modifiche e Inventario

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: I record di Rilevamento modifiche non vengono visualizzati per i computer Windows

#### <a name="issue"></a>Problema

Non vengono visualizzati eventuali risultati di Inventario o Rilevamento modifiche per i computer Windows che sono stati caricati per Rilevamento modifiche.

#### <a name="cause"></a>Causa

Questo errore può dipendere dalle cause seguenti:

1. **Microsoft Monitoring Agent** non è in esecuzione
2. Le comunicazioni verso l'account di Automazione sono bloccate.
3. I Management Pack per il Rilevamento modifiche non vengono scaricati.
4. La macchina virtuale di cui si esegue l'onboarding può provenire da una macchina clonata che non è stata preparata tramite Sysprep con Microsoft Monitoring Agent installato.

#### <a name="resolution"></a>Risoluzione

1. Verificare che **Microsoft Monitoring Agent** (HealthService.exe) sia in esecuzione nel computer.
1. Controllare **Visualizzatore eventi** nel computer e cercare gli eventi che contengono la parola `changetracking`.
1. Vedere [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e sulle porte da abilitare per il funzionamento di Rilevamento modifiche.
1. Verificare che i seguenti management Pack di Rilevamento modifiche e Inventario siano presenti in locale:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Se viene utilizzata un'immagine clonata, preparare prima l'immagine con Sysprep e installare l'agente MMA al termine dell'operazione.

Se queste soluzioni non consentono di risolvere il problema e si contatta il supporto tecnico, è possibile eseguire i comandi seguenti per raccogliere la diagnostica sull'agente

Nel computer agente, passare a `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` ed eseguire i comandi seguenti:

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Per impostazione predefinita è abilitato il Tracciamento degli errori, se si desidera abilitare i messaggi di errore dettagliati come nell'esempio precedente, usare il parametro `VER`. Per la traccia delle informazioni, usare `INF` quando si richiama `StartTracing.cmd`.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
