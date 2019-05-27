---
title: Configurare il livello PremiumV2 - Servizio app di Azure | Microsoft Docs
description: Informazioni su migliorare le prestazioni di un'app Web, un'app per dispositivi mobili o un'app per le API nel servizio app di Azure passando al nuovo piano tariffario PremiumV2.
keywords: servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c85644e3cab39f9e0864af91722ee54aab6d59f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66139803"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurare il livello PremiumV2 per il servizio app di Azure

Il nuovo piano tariffario **PremiumV2** consente di avere processori più veloci e spazio di archiviazione su unità SSD, nonché di raddoppiare il rapporto tra memoria e core rispetto ai piani tariffari esistenti. I vantaggi relativi alle prestazioni che ne derivano consentono di eseguire le app con un minor numero di istanze e garantiscono quindi un risparmio economico. Questo articolo illustra come creare un'app nel livello **PremiumV2** o passare un'app al livello **PremiumV2**.

## <a name="prerequisites"></a>Prerequisiti

Per scalare un'app al piano **PremiumV2**, è necessario avere un'app di Servizio app di Azure eseguita su un piano tariffario inferiore rispetto a **PremiumV2** e l'app deve essere in esecuzione in una distribuzione di Servizio app che supporta PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilità di PremiumV2

Il livello **PremiumV2** è disponibile per il servizio app sia in _Windows_ che in _Linux_.

**PremiumV2** è disponibile nella maggior parte delle aree di Azure. Per vedere se è disponibile nella propria area, eseguire in [Azure Cloud Shell](../cloud-shell/overview.md) il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Creare un'app nel livello PremiumV2

Il piano tariffario di un'app del servizio app è definito nel [piano di servizio app](overview-hosting-plans.md) su cui è in esecuzione. È possibile creare un piano di Servizio app in modo indipendente o nell'ambito della creazione di un'app.

Durante la configurazione del piano di servizio app nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> selezionare **Piano tariffario**. 

Selezionare **Produzione**, quindi selezionare **P1V2**, **P2V2** o **P3V2** e fare clic su **Applica**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se le opzioni **P1V2**, **P2V2** e **P3V2** non sono visualizzate oppure sono in grigio, il livello **PremiumV2** probabilmente non è disponibile nella distribuzione del servizio app sottostante che contiene il piano di servizio app. Per altri dettagli, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Passare un'app esistente al livello PremiumV2

Prima di passare un'app esistente al livello **PremiumV2**, assicurarsi che **PremiumV2** sia disponibile. Per informazioni, vedere [Disponibilità di PremiumV2](#availability). Se non è disponibile, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

A seconda dell'ambiente di hosting, il passaggio al livello successivo può richiedere operazioni aggiuntive. 

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire la pagina dell'app del servizio di app.

Nel riquadro di spostamento sinistro della pagina dell'app del servizio app selezionare **Aumenta prestazioni (piano di servizio app)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selezionare **Produzione**, quindi selezionare **P1V2**, **P2V2** o **P3V2** e fare clic su **Applica**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se l'operazione viene completata correttamente, nella pagina di panoramica dell'app appare l'indicazione che l'app è passata al livello **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se si verifica un errore

Alcuni piani di servizio app non possono essere passati al livello PremiumV2 se la distribuzione del servizio app sottostante non supporta PremiumV2.  Per altri dettagli, vedere [Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata](#unsupported).

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Passare a un piano superiore da una combinazione di gruppo di risorse e area non supportata

Se l'app viene eseguita in una distribuzione del servizio app in cui **PremiumV2** non è disponibile, o se l'app viene eseguita in un'area che attualmente non supporta **PremiumV2**, sarà necessario ridistribuire l'app per sfruttare **PremiumV2**.  Sono disponibili due opzioni:

- Creare un **nuovo** gruppo di risorse e quindi una **nuova** app e un nuovo piano di Servizio app nel **nuovo** gruppo di risorse, scegliendo l'area di Azure desiderata durante il processo di creazione.  È **necessario** selezionare il piano **PremiumV2** al momento della creazione del nuovo piano di servizio app.  In questo modo ci si assicura che la combinazione di gruppo di risorse, piano di servizio app e area di Azure consentirà di creare il piano di servizio app in una distribuzione del servizio app che supporta **PremiumV2**.  Ridistribuire quindi il codice dell'applicazione nell'app e nel piano di servizio app appena creati. Se lo si desidera, è possibile passare in seguito a un piano di servizio app inferiore rispetto a **PremiumV2** per risparmiare sui costi e sarà comunque possibile passare di nuovo a un piano superiore in futuro con **PremiumV2**.
- Se l'app è già in esecuzione in un livello **Premium** esistente, è possibile clonare l'app con tutte le impostazioni dell'app, le stringhe di connessione e la configurazione di distribuzione in un nuovo piano di servizio app che usa **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Nella pagina **Clona app** è possibile creare un piano di servizio app con **PremiumV2** nell'area voluta e specificare le impostazioni dell'app e la configurazione da clonare.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la creazione dell'app nel livello **PremiumV2** con script usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il comando seguente crea un piano di servizio app in _P1V2_. È possibile eseguirlo in Cloud Shell. Le opzioni per `--sku` sono P1V2, _P2V2_ e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il comando seguente crea un piano di servizio app in _P1V2_. Le opzioni per `-WorkerSize` sono _Small_, _Medium_ e _Large_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Altre risorse

[Aumentare le prestazioni di un'app in Azure](web-sites-scale.md)  
[Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md)
