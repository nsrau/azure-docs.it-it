---
title: Risoluzione dei problemi relativi all'account di automazione
description: Informazioni su come risolvere i problemi relativi a un account Azure.Learn how to troubleshoot and resolve issues with an Azure account.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301005"
---
# <a name="automation-account-troubleshooting"></a>Risoluzione dei problemi relativi all'account di automazione

In questo articolo vengono illustrate le soluzioni ai problemi che possono verificarsi quando si utilizza un account di automazione. Le sezioni seguenti evidenziano messaggi di errore specifici e le possibili soluzioni per ciascuno di essi. Per informazioni generali sugli account di automazione, vedere [Creare un account Azure.](../automation-quickstart-create-account.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: impossibile registrare il provider di risorse di automazione per le sottoscrizioniScenario: Unable to register Automation Resource Provider for subscriptions

### <a name="issue"></a>Problema

Quando si utilizzano soluzioni di gestione nell'account di automazione, si verifica il seguente errore:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

Il provider di risorse di automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Risoluzione

Per registrare il provider di risorse di automazione, seguire questi passaggi nel portale di Azure:To register the Automation Resource Provider, follow these steps in the Azure portal:

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Passare a **Abbonamenti** e selezionare l'abbonamento dalla pagina Abbonamenti.   

3. In **Impostazioni**selezionare **Provider di risorse**.

4. Nell'elenco dei provider di risorse verificare che il provider di risorse **Microsoft.Automation** sia registrato.

5. Se non è presente nell'elenco, registrare il provider **Microsoft.Automation** seguendo la procedura descritta in Risolvere gli [errori per la registrazione](/azure/azure-resource-manager/resource-manager-register-provider-errors)del provider di risorse .

## <a name="next-steps"></a>Passaggi successivi

Se non hai visto il problema o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.