---
title: Risoluzione dei problemi dell'account di Automazione
description: Informazioni su come risolvere i problemi relativi a un account Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679393"
---
# <a name="troubleshoot-the-automation-account"></a>Risolvere i problemi relativi all'account di automazione

Questo articolo illustra le soluzioni ai problemi che possono verificarsi quando si usa un account di automazione. Nelle sezioni seguenti vengono illustrati i messaggi di errore specifici e le possibili risoluzioni per ciascuno di essi. Per informazioni generali sugli account di automazione, vedere [creare un account Azure](../automation-quickstart-create-account.md).

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

2. Passare a **sottoscrizioni** e selezionare la sottoscrizione nella pagina sottoscrizioni.   

3. In **Impostazioni**selezionare **provider di risorse**.

4. Dall'elenco dei provider di risorse, verificare che il provider di risorse **Microsoft. Automation** sia registrato.

5. Se il provider non è elencato, registrarlo come descritto in [risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato in precedenza o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi a [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.