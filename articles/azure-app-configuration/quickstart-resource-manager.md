---
title: Creare un archivio di Configurazione app di Azure con un modello di Azure Resource Manager
titleSuffix: Azure App Configuration
description: Informazioni su come creare un archivio di Configurazione app di Azure con un modello di Azure Resource Manager.
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 09/21/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 840f907015e9673caba46998493b5cb705de5fb7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824183"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Avvio rapido: Creare un archivio di Configurazione app di Azure con un modello di Resource Manager

Questa guida di avvio rapido descrive come:

- Distribuire un archivio di Configurazione app con un modello di Resource Manager
- Creare coppie chiave-valore in un archivio di Configurazione app con un modello di Resource Manager
- Leggere le coppie chiave-valore in un archivio di Configurazione app da un modello di Resource Manager

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/en-us/resources/templates/101-app-configuration-store-kv/). Crea un nuovo archivio di Configurazione app con due coppie chiave-valore al suo interno. Usa quindi la funzione `reference` per restituire i valori delle due risorse chiave-valore. La lettura del valore della chiave in questo modo ne consente l'uso in altre parti del modello.

La guida di avvio rapido usa l'elemento `copy` per creare più istanze di una risorsa chiave-valore. Per altre informazioni sull'elemento `copy`, vedere [Iterazione delle risorse nei modelli di Azure Resource Manager](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Questo modello richiede la versione del provider di risorse di Configurazione app `2020-07-01-preview` o successiva. Questa versione usa la funzione `reference` per leggere le coppie chiave-valore. La funzione `listKeyValue` usata per la lettura di coppie chiave-valore nella versione precedente non è disponibile a partire dalla versione `2020-07-01-preview`.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json" range="1-88" highlight="52-58,61-75,80,84":::

Nel modello sono definite due risorse di Azure:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): crea un archivio di Configurazione app.
- Microsoft.AppConfiguration/configurationStores/keyValues: crea una coppia chiave-valore all'interno dell'archivio di Configurazione app.

> [!NOTE]
> Il nome della risorsa `keyValues` è una combinazione di chiave ed etichetta. La chiave e l'etichetta sono unite dal delimitatore `$`. L'etichetta è facoltativa. Nell'esempio precedente la risorsa `keyValues` denominata `myKey` crea una coppia chiave-valore senza etichetta.
>
> La codifica percentuale, nota anche come codifica URL, consente di includere nelle chiavi o nelle etichette caratteri non consentiti nei nomi delle risorse del modello di Resource Manager. Neanche `%` è un carattere consentito, quindi al suo posto viene usato `~`. Per codificare correttamente un nome, seguire questa procedura:
>
> 1. Applicare la codifica URL
> 2. Sostituire `~` con `~7E`.
> 3. Sostituire `%` con `~`.
>
> Ad esempio, per creare una coppia chiave-valore con il nome della chiave `AppName:DbEndpoint` e il nome dell'etichetta `Test`, il nome della risorsa dovrà essere `AppName~3ADbEndpoint$Test`.

## <a name="deploy-the-template"></a>Distribuire il modello

Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un archivio di Configurazione app con due coppie chiave-valore al suo interno.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

È anche possibile distribuire il modello con il cmdlet di PowerShell seguente. Le coppie chiave-valore saranno incluse nell'output della console di PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Nella casella di ricerca del portale di Azure digitare **Configurazione app**. Selezionare **Configurazione app** nell'elenco.
1. Selezionare la risorsa di Configurazione app appena creata.
1. In **Operazioni** fare clic su **Esplora configurazioni**.
1. Verificare che esistano due coppie chiave-valore.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, l'archivio di Configurazione app e tutte le risorse correlate. Se si prevede di usare l'archivio di Configurazione app in futuro, è possibile evitare di eliminarlo. Se non si intende continuare a usare l'archivio, eliminare tutte le risorse create con questa guida di avvio rapido eseguendo il cmdlet seguente:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla creazione di altre applicazioni con Configurazione app di Azure, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Avvio rapido: Creare un'app ASP.NET Core con Configurazione app di Azure](quickstart-aspnet-core-app.md)