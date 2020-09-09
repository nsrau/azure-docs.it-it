---
title: 'Esercitazione: Configurare il motore regole - Frontdoor di Azure'
description: Questo articolo include un'esercitazione relativa alla configurazione del motore regole nel portale di Azure e nell'interfaccia della riga di comando.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1ff8ca7d7c5972869de4c0d394129c591c7f4a7c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399073"
---
# <a name="configure-your-rules-engine"></a>Configurare il motore regole

Questo articolo illustra la procedura per creare una configurazione del motore regole e la prima regola nel portale di Azure e nell'interfaccia della riga di comando. 

## <a name="configure-rules-engine-in-azure-portal"></a>Configurare il motore regole nel portale di Azure
1. Prima di creare una configurazione del motore regole [creare una frontdoor](quickstart-create-front-door.md).

2. All'interno della risorsa frontdoor passare a **Impostazioni** e selezionare **Configurazione motore regole**. Fare clic su **Aggiungi**, assegnare un nome alla configurazione e iniziare a creare la prima configurazione del motore regole.

    ![trovare il motore regole](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Fare clic su **Aggiungi regola** per creare la prima regola. Quindi, fare su **Aggiungi condizione** o **Aggiungi azione** per definire la regola.
    
    > [!NOTE]
    >- Per eliminare una condizione o un'azione dalla regola, usare il Cestino sul lato destro della condizione o dell'azione specifica.
    > - Per creare una regola che si applica a tutto il traffico in ingresso, non specificare alcuna condizione.
    > - Per interrompere la valutazione delle regole una volta soddisfatta la prima condizione di corrispondenza, selezionare **Arresta valutazione delle regole rimanenti**. Se questa opzione è selezionata e tutte le condizioni di corrispondenza di una regola specifica sono soddisfatte, le altre regole nella configurazione non verranno eseguite.  

    ![trovare il motore regole](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Determinare la priorità delle regole all'interno della configurazione usano i pulsanti Sposta su, Sposta giù e Sposta all'inizio. La priorità è in ordine crescente, ovvero la regola elencata per prima è quella più importante.

5. Dopo aver creato una o più regole, premere **Salva**. Questa azione crea la configurazione del motore regole.

6. Dopo aver creato una o più configurazioni del motore regole, associarne una a una regola di route. Mentre una singola configurazione può essere applicata a molte regole di route, una regola di route può contenere una sola configurazione del motore regole. Per creare l'associazione, passare a **Progettazione frontdoor** > **Route rules** (Regole di route). Selezionare la regola di route a cui aggiungere la configurazione del motore regole, passare a **Dettagli route** > **Configurazione motore regole** e selezionare la configurazione da associare.

    ![trovare il motore regole](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Configurare il motore regole nell'interfaccia della riga di comando di Azure

1. Se non è già stato fatto, installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Aggiungere l'estensione "front-door":- az extension add --name front-door. Quindi, accedere e passare alla sottoscrizione con az account set --subscription <name_or_Id>.

2. Per iniziare, creare un motore regole. Questo esempio illustra una regola con un'azione basata su intestazione e una condizione di corrispondenza. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Elencare tutte le regole. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Aggiungere un'azione di sostituzione della route di inoltro. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Elencare tutte le azioni in una regola. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Collegare una configurazione del motore regole a una regola di routing.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Scollegare il motore regole. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Per altre informazioni, vedere l'elenco completo di comandi del motore regole di Frontdoor di Azure disponibile [qui](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest).   

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [motore regole di Frontdoor di Azure](front-door-rules-engine.md). 
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
- Vedere le [informazioni di riferimento dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest) per il motore regole di Frontdoor di Azure. 
- Vedere le [informazioni di riferimento di PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0) per il motore regole di Frontdoor di Azure. 
