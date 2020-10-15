---
title: Configurare il livello PremiumV3
description: Scopri come migliorare le prestazioni per app Web, per dispositivi mobili e per le API nel servizio app Azure scalando il nuovo piano tariffario PremiumV3.
keywords: servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 0030a9340d874d94b9876e23f372e97655c145da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742674"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Configurare il livello PremiumV3 per il servizio app Azure

Il nuovo piano tariffario **PremiumV3** offre processori più veloci, archiviazione SSD e quadruplicare il rapporto tra memoria e Core dei piani tariffari esistenti (Double il livello **PremiumV2** ). I vantaggi relativi alle prestazioni che ne derivano consentono di eseguire le app con un minor numero di istanze e garantiscono quindi un risparmio economico. Questo articolo illustra come creare un'app nel livello **PremiumV3** o aumentare le prestazioni di un'app al livello **PremiumV3** .

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la scalabilità verticale di un'app in **PremiumV3**, è necessario disporre di un'app di servizio app Azure eseguita in un piano tariffario inferiore a **PremiumV3**e l'app deve essere in esecuzione in una distribuzione del servizio app che supporti PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Disponibilità PremiumV3

Il livello **PremiumV3** è disponibile sia per le app native che per quelle del contenitore, inclusi i contenitori di Windows e i contenitori Linux.

> [!NOTE]
> Tutti i contenitori di Windows in esecuzione nel livello **contenitore Premium** durante il periodo di anteprima continuano a funzionare così come sono, ma il livello **contenitore Premium** continuerà a rimanere in anteprima. Il livello **PremiumV3** è la sostituzione ufficiale per il livello **contenitore Premium** . 

**PremiumV3** è disponibile in alcune aree di Azure e la disponibilità in aree aggiuntive viene aggiunta continuamente. Per vedere se è disponibile nella propria area, eseguire in [Azure Cloud Shell](../cloud-shell/overview.md) il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Creare un'app nel livello PremiumV3

Il piano tariffario di un'app del servizio app è definito nel [piano di servizio app](overview-hosting-plans.md) su cui è in esecuzione. È possibile creare un piano di Servizio app in modo indipendente o nell'ambito della creazione di un'app.

Durante la configurazione del piano di servizio app nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> selezionare **Piano tariffario**. 

Selezionare **produzione**, quindi selezionare **P1V3**, **P2V3**o **P3V3**, quindi fare clic su **applica**.

![Screenshot che mostra i piani tariffari consigliati per l'app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se non vengono visualizzate le opzioni **P1V3**, **P2V3**e **P3V3** o se le opzioni sono visualizzate in grigio, **PremiumV3** probabilmente non è disponibile nella distribuzione del servizio app sottostante che contiene il piano di servizio app. Per altri dettagli, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Ridimensionare un'app esistente al livello PremiumV3

Prima di ridimensionare un'app esistente al livello **PremiumV3** , verificare che **PremiumV3** sia disponibile. Per informazioni, vedere [disponibilità PremiumV3](#availability). Se non è disponibile, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

A seconda dell'ambiente di hosting, il passaggio al livello successivo può richiedere operazioni aggiuntive. 

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire la pagina dell'app del servizio di app.

Nel riquadro di spostamento sinistro della pagina dell'app del servizio app selezionare **Aumenta prestazioni (piano di servizio app)**.

![Screenshot che illustra come aumentare il piano di servizio app.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selezionare **produzione**, quindi selezionare **P1V3**, **P2V3**o **P3V3**, quindi fare clic su **applica**.

![Screenshot che mostra i piani tariffari consigliati per l'app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se l'operazione viene completata correttamente, la pagina Panoramica dell'app mostra che è ora in un livello **PremiumV3** .

![Screenshot che mostra il piano tariffario PremiumV3 nella pagina Panoramica dell'app.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se si riceve un errore

Alcuni piani di servizio app non possono essere scalati fino al livello PremiumV3 se la distribuzione del servizio app sottostante non supporta PremiumV3. Per altri dettagli, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata

Se l'app viene eseguita in una distribuzione del servizio app in cui **PremiumV3** non è disponibile o se l'app viene eseguita in un'area che attualmente non supporta **PremiumV3**, è necessario ridistribuire l'app per sfruttare i vantaggi di **PremiumV3**.  Sono disponibili due opzioni:

- Creare un'app in un nuovo gruppo di risorse e con un nuovo piano di servizio app. Quando si crea il piano di servizio app, selezionare un livello **PremiumV3** . Questo passaggio garantisce che il piano di servizio app venga distribuito in un'unità di distribuzione che supporta **PremiumV3**. Quindi, ridistribuire il codice dell'applicazione nell'app appena creata. Anche se si ridimensiona il piano di servizio app fino a un livello inferiore per ridurre i costi, è sempre possibile ridimensionare il backup in **PremiumV3** perché l'unità di distribuzione lo supporta.
- Se l'app è già in esecuzione in un livello **Premium** esistente, è possibile clonare l'app con tutte le impostazioni dell'app, le stringhe di connessione e la configurazione della distribuzione in un nuovo piano di servizio app che usa **PremiumV3**.

    ![Screenshot che illustra come clonare l'app.](media/app-service-configure-premium-tier/clone-app.png)

    Nella pagina **clona app** è possibile creare un piano di servizio app usando **PremiumV3** nell'area desiderata e specificare le impostazioni e la configurazione dell'app che si vuole clonare.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Passaggio dallo SKU Premium container a Premium V3

Se si dispone di un'app che usa lo SKU del contenitore Premium di anteprima e si vuole passare al nuovo SKU Premium V3, è necessario ridistribuire l'app per sfruttare i vantaggi di **PremiumV3**. Per eseguire questa operazione, vedere la prima opzione in [scalabilità verticale rispetto a una combinazione di area e gruppo di risorse non supportata](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la creazione di app nel livello **PremiumV3** con gli script, usando l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il comando seguente crea un piano di servizio app in _P1V2_. È possibile eseguirlo in Cloud Shell. Le opzioni per `--sku` sono P1V3, _P2V3_e _P3V3_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il comando seguente crea un piano di servizio app in _P1V3_. Le opzioni per `-WorkerSize` sono _Small_, _Medium_ e _Large_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Altre risorse

[Aumentare le prestazioni di un'app in Azure](manage-scale-up.md) 
 [Ridimensionare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md)