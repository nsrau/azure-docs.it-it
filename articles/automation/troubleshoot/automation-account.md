---
title: Risoluzione dei problemi dell'account di automazione di Azure
description: Informazioni su come risolvere i problemi relativi a un account Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864131"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Risolvere i problemi relativi a un account di automazione di Azure

Questo articolo illustra le soluzioni ai problemi che possono verificarsi quando si usa un account di automazione di Azure. Per informazioni generali sugli account di automazione, vedere [creare un account Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: non è possibile registrare il provider di risorse di automazione per le sottoscrizioni

### <a name="issue"></a>Problema

Quando si lavora con le soluzioni di gestione nell'account di automazione, si verifica l'errore seguente:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

Il provider di risorse di automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Soluzione

Per registrare il provider di risorse di automazione, attenersi alla procedura seguente nella portale di Azure:

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Passare a **sottoscrizioni**e selezionare la sottoscrizione.   

3. In **Impostazioni**selezionare **provider di risorse**.

4. Dall'elenco dei provider di risorse, verificare che il provider di risorse **Microsoft. Automation** sia registrato.

5. Se il provider non è elencato, registrarlo come descritto in [risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Passaggi successivi

Se questo articolo non risolve il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport). Questo è l'account Microsoft Azure ufficiale per connettere la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**.