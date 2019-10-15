---
title: Creare e distribuire un modello di Azure Resource Manager con IntelliJ IDEA | Microsoft Docs
description: Informazioni su come creare il primo modello di Azure Resource Manager con IntelliJ IDEA ed eseguirne la distribuzione.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: d25a5720e3aaa8ad6306a72d2db8d3ca2e89861b
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169440"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con IntelliJ IDEA

Questo articolo illustra come distribuire un modello di Resource Manager in Azure usando IntelliJ IDEA, nonché il processo di modifica e di aggiornamento del modello direttamente dall'IDE. I modelli di Resource Manager sono file JSON che definiscono le risorse che è necessario distribuire per la soluzione. Per comprendere i concetti associati alla distribuzione e alla gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).

![Avvio rapido per i modelli di Resource Manager: diagramma relativo al portale](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Al termine dell'esercitazione si distribuirà un account di archiviazione di Azure. Lo stesso processo può essere usato per distribuire altre risorse di Azure.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition o Community Edition installato
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) installato, vedere la [guida alla gestione dei plug-in di IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html) per altre informazioni
* [Accesso](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) all'account Azure per Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Distribuire un modello di avvio rapido

Invece di creare un modello da zero, aprire un modello da [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/). I modelli di avvio rapido di Azure costituiscono un repository di modelli di Resource Manager. Il modello usato in questa guida introduttiva è denominato [Create a standard storage account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) (Creare un account di archiviazione Standard). Questo modello definisce una risorsa account di archiviazione di Azure.

1. Fare clic con il pulsante destro del mouse e salvare [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) e [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) sul computer locale.

1. Se Azure Toolkit è installato correttamente ed è stato eseguito l'accesso, nella barra laterale di IntelliJ IDEA verrà visualizzato Azure Explorer. Fare clic con il pulsante destro del mouse su **Resource Management** (Gestione risorse) e scegliere **Create Deployment** (Crea distribuzione).

    ![Modello di Resource Manager: fare con il pulsante destro del mouse per creare la distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Configurare **Deployment Name** (Nome distribuzione), **Subscription** (Sottoscrizione), **Resource Group** (Gruppo di risorse) e **Region** (Area). In questo esempio si distribuisce il modello in un nuovo gruppo di risorse `testRG`. Selezionare quindi `azuredeploy.json` come **Resource Template** (Modello risorsa) e il file `azuredeploy.parameters.json` scaricato come **Resource Parameters** (Parametri risorsa).

    ![Modello di Resource Manager: selezionare i file per creare la distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Facendo clic su OK verrà avviata la distribuzione. Fino al suo completamento, lo stato di avanzamento sarà riportato nella **barra di stato** di IntelliJ IDEA in basso.

    ![Modello di Resource Manager: stato della distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Esplorare una distribuzione esistente

1. Al termine della distribuzione, è possibile visualizzare il nuovo gruppo di risorse `testRG` e la nuova distribuzione che sono stati creati. Facendo clic con il pulsante destro del mouse sulla distribuzione si può visualizzare un elenco delle azioni possibili. Selezionare quindi **Show Properties** (Mostra proprietà).

    ![Modello di Resource Manager: esplorare la distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Verrà aperta una visualizzazione scheda contenente alcune proprietà utili, come lo stato della distribuzione e la struttura del modello.

    ![Modello di Resource Manager: visualizzazione delle proprietà della distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Modificare e aggiornare una distribuzione esistente

1. Selezionare **Edit Deployment** (Modifica distribuzione) dal menu di scelta rapida o nella precedente visualizzazione di Show Properties (Mostra proprietà). Verrà aperta un'altra visualizzazione scheda contenente i file del modello e dei parametri per la distribuzione in Azure. Per salvare i file in locale, è possibile fare clic su **Export Template File** (Esporta file modello) o **Export Parameter Files** (Esporta file parametri).

    ![Modello di Resource Manager: modificare la distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. È possibile modificare i due file in questa pagina e distribuire le modifiche in Azure. In questo esempio si modifica il valore di **storageAccountType** da `Standard_LRS` a `Standard_GRS` nel file dei parametri. Fare quindi clic su **Update Deployment** (Aggiorna distribuzione) in basso e confermare l'aggiornamento.

    ![Modello di Resource Manager: modificare la distribuzione](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Al termine dell'aggiornamento della distribuzione, è possibile verificare nel portale che l'account di archiviazione creato sia stato modificato in `Standard_GRS`.

## <a name="clean-up-resources"></a>Pulire le risorse

1. Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse. È possibile eseguire questa operazione dal portale di Azure o dall'interfaccia della riga di comando di Azure. In Azure Explorer in IntelliJ IDEA fare clic con il pulsante destro del mouse sul **gruppo di risorse** creato e scegliere Delete (Elimina).

    ![Eliminare il gruppo di risorse in Azure Explorer in IntelliJ IDEA](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Si noti che eliminando una distribuzione non verranno eliminate le risorse da essa create. Se non sono più necessarie, eliminare il gruppo di risorse o le risorse specifiche corrispondenti.

## <a name="next-steps"></a>Passaggi successivi

L'obiettivo principale di questa guida di avvio rapido è usare IntelliJ IDEA per distribuire un modello esistente da Modelli di avvio rapido di Azure. È stato anche illustrato come visualizzare e aggiornare una distribuzione esistente in Azure. I modelli di avvio rapido di Azure potrebbero non fornire tutto il necessario. Per altre informazioni sullo sviluppo dei modelli, vedere la nuova serie di esercitazioni per principianti:

> [!div class="nextstepaction"]
> [Esercitazioni per principianti](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Visitare il centro per sviluppatori Java in Azure](https://docs.microsoft.com/azure/java)
