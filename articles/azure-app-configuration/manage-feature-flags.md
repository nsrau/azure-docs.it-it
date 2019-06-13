---
title: Esercitazione per l'uso di Configurazione app di Azure per gestire i flag di funzionalità | Microsoft Docs
description: In questa esercitazione si apprenderà come gestire i flag di funzionalità separatamente dall'applicazione usando Configurazione app di Azure.
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
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393563"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Esercitazione: Gestire i flag di funzionalità in Configurazione app di Azure

È possibile archiviare tutti i flag di funzionalità in Configurazione app di Azure e amministrarli da un'unica posizione. Configurazione app contiene un'interfaccia utente del portale denominata **Gestione funzionalità** progettata in modo specifico per i flag di funzionalità. Configurazione app supporta in modo nativo anche lo schema dati dei flag di funzionalità .NET Core.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Definire e gestire i flag di funzionalità in Configurazione app.
> * Accedere ai flag di funzionalità dall'applicazione.

## <a name="create-feature-flags"></a>Creare i flag di funzionalità

Gestione funzionalità nel portale di Azure per Configurazione app fornisce un'interfaccia utente per creare e gestire i flag di funzionalità da usare nelle applicazioni.

Per aggiungere un nuovo flag di funzionalità:

1. Selezionare **Gestione funzionalità** >  **+Aggiungi** per aggiungere un flag di funzionalità.

    ![Elenco dei flag di funzionalità](./media/azure-app-configuration-feature-flags.png)

1. Immettere un nome di chiave univoco per il flag di funzionalità. Questo nome è necessario per fare riferimento al flag nel codice.

1. Se si vuole, specificare una descrizione per il flag di funzionalità.

1. Impostare lo stato iniziale del flag di funzionalità. Questo stato è in genere *Disattivato* o *Attivato*. Lo stato *Attivato* viene modificato in *Condizionale* se si aggiunge un filtro al flag di funzionalità.

    ![Creazione dei flag di funzionalità](./media/azure-app-configuration-feature-flag-create.png)

1. Quando lo stato è *Attivato*, selezionare **+Aggiungi filtro** per specificare condizioni aggiuntive per qualificare lo stato. Immettere una chiave filtro predefinita o personalizzata e quindi selezionare **+Aggiungi parametro** per associare uno o più parametri al filtro. I filtri incorporati sono:

    | Chiave | Parametri JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0-100 percent} |
    | Microsoft.TimeWindow | {"Start": UTC time, "End": UTC time} |

    ![Filtro dei flag di funzionalità](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aggiornare gli stati dei flag di funzionalità

Per modificare il valore di stato di un flag di funzionalità:

1. Selezionare **Gestione funzionalità**.

1. A destra di un flag di funzionalità che si vuole modificare, selezionare i puntini di sospensione ( **...** ) e quindi selezionare **Modifica**.

1. Impostare un nuovo stato per il flag di funzionalità.

## <a name="access-feature-flags"></a>Accedere ai flag di funzionalità

I flag di funzionalità creati da Gestione funzionalità vengono archiviati e recuperati come normali valori chiave e conservati con uno speciale prefisso dello spazio dei nomi, `.appconfig.featureflag`. Per visualizzare i valori chiave sottostanti, usare Esplora configurazione. L'applicazione può recuperare questi valori usando i provider di configurazione di Configurazione app, gli SDK, le estensioni della riga di comando e le API REST.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come gestire i flag di funzionalità e i relativi stati usando Configurazione app. Per altre informazioni sul supporto della gestione funzionalità in Configurazione app e ASP.NET Core, vedere l'articolo seguente:

* [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md)
