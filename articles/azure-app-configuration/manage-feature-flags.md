---
title: Esercitazione per l'uso di Configurazione app di Azure per gestire i flag di funzionalità | Microsoft Docs
description: In questa esercitazione si apprenderà come gestire i flag di funzionalità separatamente dall'applicazione usando Configurazione app di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412290"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Esercitazione: Gestire i flag di funzionalità in Configurazione app di Azure

È possibile archiviare tutti i flag di funzionalità in Configurazione app di Azure e amministrarli da un'unica posizione. Il portale prevede un'interfaccia utente, **Gestione funzionalità**, progettata in modo specifico per i flag di funzionalità. Per di più, Configurazione app supporta lo schema dati dei flag di funzionalità in modo nativo.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Definire e gestire i flag di funzionalità in Configurazione app.
> * Accedere ai flag di funzionalità dall'applicazione.

## <a name="create-feature-flags"></a>Creare i flag di funzionalità

**Gestione funzionalità** nel portale di Azure per Configurazione app fornisce un'interfaccia utente per creare e gestire i flag di funzionalità da usare nell'applicazione. Per aggiungere un nuovo flag di funzionalità, eseguire i passaggi seguenti.

1. Selezionare **Gestione funzionalità** > **+ Crea** per aggiungere un flag di funzionalità.

    ![Elenco dei flag di funzionalità](./media/azure-app-configuration-feature-flags.png)

2. Immettere un nome di chiave univoco per il flag di funzionalità. Questo nome è necessario per fare riferimento al flag nel codice.

3. Specificare facoltativamente una descrizione di semplice comprensione del flag di funzionalità.

4. Impostare lo stato iniziale del flag di funzionalità. In genere è semplicemente *attivato* oppure *disattivato*.

    ![Creazione dei flag di funzionalità](./media/azure-app-configuration-feature-flag-create.png)

5. Quando lo stato è *attivato* specificare facoltativamente qualsiasi condizione aggiuntiva per qualificarlo con **Aggiungi filtro**. Immettere una chiave filtro incorporata o personalizzata e associare uno o più parametri. I filtri incorporati sono:

    | Chiave | Parametri JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0-100 percent} |
    | Microsoft.TimeWindow | {"Start": UTC time, "End": UTC time} |

    ![Filtro dei flag di funzionalità](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aggiornare gli stati dei flag di funzionalità

Per modificare il valore dello stato di un flag di funzionalità, eseguire i passaggi seguenti.

1. Selezionare **Gestione funzionalità**.

2. Fare clic su **...** > **Modifica** a destra di un flag di funzionalità da modificare.

3. Impostare un nuovo stato per il flag di funzionalità.

## <a name="access-feature-flags"></a>Accedere ai flag di funzionalità

I flag di funzionalità creati da **Gestione funzionalità** vengono archiviati e recuperati come normali coppie chiave-valore, quindi conservati con uno speciale prefisso spazio dei nomi, *.appconfig.featureflag*. Per visualizzare le coppie chiave-valore sottostanti, usare **Esplora configurazione**. L'applicazione può recuperarli usando i provider di configurazione di Configurazione app, gli SDK, le estensioni della riga di comando e le API REST.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come gestire i flag di funzionalità e i relativi stati usando Configurazione app. Per altre informazioni sul supporto della gestione funzionalità in Configurazione app, vedere le risorse seguenti.

* [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md)
