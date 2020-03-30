---
title: Utilizzare i filtri funzionalità per abilitare una funzionalità per un sottoinsieme di utenti
titleSuffix: Azure App Configuration
description: Informazioni su come utilizzare i filtri funzionalità per abilitare una funzionalità per un sottoinsieme di utenti
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057008"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Utilizzare i filtri funzionalità per abilitare una funzionalità per un sottoinsieme di utenti

I flag di funzionalità consentono di attivare o disattivare la funzionalità nell'applicazione. Un semplice flag di funzionalità è attivato o disattivato. L'applicazione si comporta sempre allo stesso modo. Ad esempio, è possibile implementare una nuova feature dietro un flag di feature. Quando il flag di funzionalità è abilitato, tutti gli utenti visualizzano la nuova funzionalità. La disattivazione del flag di funzionalità nasconde la nuova funzionalità.

Al contrario, un flag di _funzionalità condizionale_ consente l'abilitazione o la disabilitazione dinamica del flag di funzionalità. L'applicazione potrebbe comportarsi in modo diverso, a seconda dei criteri di flag di funzionalità. Si supponga di voler prima mostrare la nuova funzionalità a un piccolo sottoinsieme di utenti. Un flag di funzionalità condizionale consente di abilitare il flag di funzionalità per alcuni utenti, disabilitandolo per altri utenti. _I filtri funzionalità_ determinano lo stato del flag di funzionalità ogni volta che viene valutato.

La `Microsoft.FeatureManagement` libreria include due filtri di funzionalità:

- `PercentageFilter`abilita il flag di funzionalità in base a una percentuale.
- `TimeWindowFilter`abilita il flag di funzionalità durante un intervallo di tempo specificato.

È inoltre possibile creare un filtro di funzionalità personalizzato che implementa [l'interfaccia Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrazione di un filtro funzionalità

Per registrare un filtro `AddFeatureFilter` di funzionalità, chiamare il metodo , specificando il nome del filtro di funzionalità. Ad esempio, il codice `PercentageFilter`seguente registra :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configurazione di un filtro di funzionalità in Configurazione app di AzureConfiguring a feature filter in Azure App Configuration

Alcuni filtri di funzionalità hanno impostazioni aggiuntive. Ad esempio, `PercentageFilter` attiva una feature in base a una percentuale. Ha un'impostazione che definisce la percentuale da utilizzare.

È possibile configurare queste impostazioni per i flag di funzionalità definiti in Configurazione app di Azure.You can configure these settings for feature flags defined in Azure App Configuration. Ad esempio, attenersi `PercentageFilter` alla seguente procedura da utilizzare per abilitare il flag di funzionalità per il 50% delle richieste a un'app Web:

1. Seguire le istruzioni in [Guida introduttiva: Aggiungere flag di funzionalità a un'app ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) per creare un'app Web con un flag di funzionalità.

1. Nel portale di Azure passare all'archivio di configurazione e fare clic su **Gestione funzionalità**.

1. Fare clic sul menu di scelta rapida per il flag di feature *Beta* creato nella guida introduttiva. Fare clic su **Edit**.

    > [!div class="mx-imgBorder"]
    > ![Modifica il flag della funzione Beta](./media/edit-beta-feature-flag.png)

1. Nella schermata **Modifica,** selezionare il pulsante di opzione **Attivato,** se non è già selezionato. Quindi fare clic sul pulsante **Aggiungi filtro.** (L'etichetta del pulsante di opzione **On** cambierà in Lettura **Condizionale**.)

1. Nel campo **Chiave** immettere *Microsoft.Percentage*.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi filtro funzionalità](./media/feature-flag-add-filter.png)

1. Fare clic sul menu di scelta rapida accanto alla chiave di filtro della funzionalità. Fare clic su **Modifica parametri**.

    > [!div class="mx-imgBorder"]
    > ![Modificare i parametri del filtro delle entità geografiche](./media/feature-flag-edit-filter-parameters.png)

1. Posizionare il puntatore del mouse sotto l'intestazione **Nome** in modo che le caselle di testo vengano visualizzate nella griglia. Immettere un **Nome** di *valore* e un **Valore** di 50. Il campo **Valore** indica la percentuale di richieste per cui abilitare il filtro funzionalità.

    > [!div class="mx-imgBorder"]
    > ![Impostare i parametri del filtro delle funzioni](./media/feature-flag-set-filter-parameters.png)

1. Fare clic su **Applica** per tornare alla schermata **Modifica contrassegno funzionalità.** Quindi fare di nuovo clic su **Applica** per salvare le impostazioni dei contrassegni di funzionalità.

1. Lo **stato** del flag di funzionalità viene ora visualizzato come *Condizionale*. Questo stato indica che il flag di funzionalità verrà abilitato o disabilitato in base ai criteri applicati dal filtro funzionalità.

    > [!div class="mx-imgBorder"]
    > ![Flag di funzionalità condizionale](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtri delle funzioni in azione

Per vedere gli effetti di questo flag di funzionalità, avviare l'applicazione e premere il pulsante **Aggiorna** nel browser più volte. Vedrai che l'elemento *Beta* viene visualizzato sulla barra degli strumenti circa il 50% del tempo. È nascosto per il resto del `PercentageFilter` tempo, perché disattiva la funzione *Beta* per un sottoinsieme di richieste. Il video seguente mostra questo comportamento in azione.

> [!div class="mx-imgBorder"]
> ![PercentageFilter in azione](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica della gestione delle funzionalità](./concept-feature-management.md)
