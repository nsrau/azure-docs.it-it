---
title: 'Avvio rapido: Configurare la disponibilità elevata con Frontdoor di Azure - Interfaccia della riga di comando di Azure'
description: Questo argomento di avvio rapido illustra come usare Frontdoor di Azure per creare un'applicazione Web globale a disponibilità elevata e prestazioni elevate con l'interfaccia della riga di comando di Azure.
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: a24c0fdb244f87dbf281bcf59b5e1986a215142a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275628"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Avvio rapido: Creare un'istanza di Frontdoor per un'applicazione Web globale a disponibilità elevata con l'interfaccia della riga di comando di Azure

Iniziare a usare Frontdoor di Azure con l'interfaccia della riga di comando di Azure per creare un'applicazione Web globale a disponibilità elevata e prestazioni elevate.

Frontdoor indirizza il traffico Web a risorse specifiche in un pool back-end. Definire il dominio front-end, aggiungere le risorse a un pool back-end e creare una regola di gestione. Questo articolo usa una semplice configurazione di un unico pool back-end con due risorse dell'app Web e un'unica regola di gestione che usa il percorso predefinito corrispondente a "/*".

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell
- Assicurarsi che l'estensione Frontdoor sia stata aggiunta all'interfaccia della riga di comando di Azure

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Per questo argomento di avvio rapido sono necessari due gruppi di risorse. Uno negli *Stati Uniti centrali* e il secondo negli *Stati Uniti centro-meridionali*.

Come prima cosa creare con [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true) un gruppo di risorse:

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Creare due istanze di un'app Web

Per questo argomento di avvio rapido, è necessario creare due istanze di un'applicazione Web eseguite in aree di Azure diverse. Entrambe le istanze dell'applicazione Web vengono eseguite in modalità attiva/attiva, in modo che entrambe possano gestire il traffico.

Se non si dispone già di un'app Web, usare lo script seguente per configurare due app Web di esempio.

### <a name="create-app-service-plans"></a>Creare piani di servizio app

Prima di poter creare le app Web, sono necessari due piani di servizio app, uno negli *Stati Uniti centrali* e il secondo negli *Stati Uniti centro-meridionali*.

Creare i piani di servizio app con [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
-resource-groupg myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Creare app Web

L'esecuzione dei comandi seguenti creerà un'app Web in ognuno dei piani di servizio app del passaggio precedente. I nomi delle app Web devono essere univoci a livello globale.

Creare un'app Web con [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Prendere nota del nome host predefinito di ogni app Web, in modo da poter definire gli indirizzi back-end quando si distribuirà l'istanza di Frontdoor nel passaggio successivo.

## <a name="create-the-front-door"></a>Creare l'istanza di Frontdoor

Creare un'istanza di Frontdoor di base con le impostazioni predefinite di bilanciamento del carico, il probe di integrità e le regole di gestione eseguendo questa procedura:

Creare un'istanza di Frontdoor con [az network front-door create](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--resource-group:** specificare un gruppo di risorse in cui si vuole distribuire l'istanza di Frontdoor.

**--name:** specificare un nome univoco a livello globale per l'istanza di Frontdoor di Azure. 

**--accepted-protocols:** i valori possibili sono **http** e **https**. Se si vuole usare entrambi i valori, specificarli entrambi separati da uno spazio.

**--backend-address:** definire qui il nome host di entrambe le app Web separate da uno spazio.

Al termine della distribuzione, prendere nota del nome host nella sezione *frontEndpoints*.

## <a name="test-the-front-door"></a>Testare l'istanza di Frontdoor

Aprire un Web browser e immettere il nome host ottenuto dai comandi. L'istanza di Frontdoor indirizza la richiesta a una delle risorse back-end.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Pagina di test dell'istanza di Frontdoor":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con l'istanza di Frontdoor non sono più necessarie, eliminare entrambi i gruppi di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione dell'istanza di Frontdoor e di tutte le risorse correlate. 

Per eliminare il gruppo di risorse, usare [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:
* Frontdoor
* Due app Web

Per informazioni su come aggiungere un dominio personalizzato all'istanza di Frontdoor, proseguire con le esercitazioni su Frontdoor.

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](front-door-custom-domain.md)