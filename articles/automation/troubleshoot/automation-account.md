---
title: Risolvere i problemi relativi all'account di Automazione di Azure
description: Questo articolo descrive come risolvere i problemi relativi a un account Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: edd57d3d77432f3bb37872ee26f414b56398cae0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187286"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Risolvere i problemi relativi all'account di Automazione di Azure

Questo articolo illustra le soluzioni ai problemi che possono verificarsi quando si usa un account di Automazione di Azure. Per informazioni generali sugli account di automazione, vedere [Panoramica sull'autenticazione dell'account di Automazione di Azure](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Impossibile registrare il provider di risorse di Automazione per le sottoscrizioni

### <a name="issue"></a>Problema

Quando si usano funzionalità di gestione nell'account di Automazione, ad esempio Gestione aggiornamenti, si verifica l'errore seguente:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

Il provider di risorse di Automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Risoluzione

Per registrare il provider di risorse di Automazione, seguire questa procedura nel portale di Azure:

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Passare a **Sottoscrizioni** e selezionare la sottoscrizione.   

3. In **Impostazioni** selezionare **Provider di risorse**.

4. Dall'elenco dei provider di risorse, verificare che il provider di risorse **Microsoft.Automation** sia registrato.

5. Se il provider non è elencato, registrarlo come descritto in [Risolvere gli errori di registrazione del provider di risorse](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="next-steps"></a>Passaggi successivi

Se questo articolo non risolve il problema, usare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
