---
title: Distribuire set di scalabilità di macchine virtuali tramite Visual Studio
description: Distribuire set di scalabilità di macchine virtuali tramite Visual Studio e un modello di Resource Manager
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066969"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Come creare un set di scalabilità di macchine virtuali con Visual Studio

Questo articolo illustra come distribuire un set di scalabilità di macchine virtuali di Azure usando una distribuzione del gruppo di risorse di Visual Studio.This article shows you how to deploy an Azure Virtual Machine Scale Set using a Visual Studio Resource Group deployment.

Set di [scalabilità di macchine virtuali di Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) è una risorsa di calcolo di Azure per distribuire e gestire una raccolta di macchine virtuali simili con scalabilità automatica e bilanciamento del carico. È possibile eseguire il provisioning e distribuire set di scalabilità di macchine virtuali tramite i [modelli di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager templates can be deployed using Azure CLI, PowerShell, REST and also directly from Visual Studio. Visual Studio fornisce un set di modelli di esempio, che è possibile distribuire come parte di un progetto di distribuzione di Azure Resource Group.Visual Studio provides a set of example templates, which you can deploy as part of an Azure Resource Group deployment project.

Le distribuzioni del gruppo di risorse di Azure sono un modo di raggruppare e pubblicare un set di risorse di Azure correlate con un'unica operazione di distribuzione. Per altre informazioni, vedere Creazione e distribuzione di gruppi di [risorse di Azure tramite Visual Studio.](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a distribuire i set di scalabilità di macchine virtuali in Visual Studio, sono necessari i prerequisiti seguenti:To get started deploying Virtual Machine Scale Sets in Visual Studio, you need the following prerequisites:

* Visual Studio 2013 o versioni successive
* Azure SDK 2.7, 2.8 o 2.9

>[!NOTE]
>Questo articolo usa Visual Studio 2019 con [Azure SDK 2.8.](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

## <a name="create-a-project"></a>Creazione di un progetto<a name="creating-a-project"></a> 

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Create a new project (Creare un nuovo progetto)** scegliere Azure Resource **Group** for C'è e quindi selezionare **Next (Avanti).**

1. In **Configura il nuovo progetto**immettere un nome e selezionare **Crea**.

    ![Assegnare un nome e creare il progetto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Nell'elenco dei modelli scegliere il modello Set di **scalabilità macchina virtuale Windows** o Set di **scalabilità di macchine virtuali Linux.From** the list of templates, choose either the Windows Virtual Machine Scale Set template or Linux Virtual Machine Scale Set template. Selezionare **OK**.

   ![Selezionare un modello di macchina virtuale](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Dopo aver creato il progetto, **Esplora soluzioni** contiene uno script di distribuzione di PowerShell, un modello di Azure Resource Manager e un file di parametri per il set di scalabilità di macchine virtuali.

## <a name="customize-your-project"></a>Personalizzare il progetto

A questo punto è possibile modificare il modello per personalizzarlo in base alle esigenze dell'applicazione. È possibile aggiungere proprietà di estensione della macchina virtuale o modificare le regole di bilanciamento del carico. Per impostazione predefinita, i modelli del set di scalabilità della macchina virtuale sono configurati per distribuire l'estensione **AzureDiagnostics,** che semplifica l'aggiunta di regole di scalabilità automatica. I modelli distribuiscono anche un servizio di bilanciamento del carico con un indirizzo IP pubblico, configurato con regole NAT in ingresso.

Il servizio di bilanciamento del carico consente di connettersi alle istanze della macchina virtuale con SSH (Linux) o RDP (Windows). L'intervallo di porte front-end inizia da 50000. Per Linux, se si sSH alla porta 50000, il bilanciamento del carico instrada la porta 22 della prima macchina virtuale nel set di scalabilità. La connessione alla porta 50001 viene instradata alla porta 22 della seconda macchina virtuale e così via.

 Un buon modo per modificare i modelli con Visual Studio consiste nell'utilizzare la **struttura JSON**. È possibile organizzare i parametri, le variabili e le risorse. Con una conoscenza dello schema, Visual Studio può indicare gli errori nel modello prima di distribuirlo.

![Esplora JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Distribuire il progetto

Distribuire il modello di Azure Resource Manager per creare la risorsa Set di scalabilità macchina virtuale:Deploy the Azure Resource Manager template to create the Virtual Machine Scale Set resource:

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e **scegliere Distribuisci** > **nuovo**.

    ![Distribuire il progetto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. In **Distribuisci in gruppo di risorse**scegliere la sottoscrizione da usare e selezionare un gruppo di risorse. Se necessario, è possibile creare un gruppo di risorse.

1. Selezionare quindi **Modifica parametri** per immettere i parametri passati al modello.

   ![Immettere la sottoscrizione e il gruppo di risorse](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Specificare il nome utente e la password per il sistema operativo. Questi valori sono necessari per creare la distribuzione. Se PowerShell Tools per Visual Studio non è installato, selezionare **Salva password** per evitare un prompt dei comandi di PowerShell nascosto oppure usare il supporto dell'insieme di [credenziali delle](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)chiavi . Selezionare **Salva** per continuare.

    ![Modificare i parametri di distribuzione](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. In **Distribuisci**in gruppo di risorse selezionare **Distribuisci**. L'azione esegue lo script **Deploy-AzureResourceGroup.ps1.** La finestra **Output** visualizza lo stato della distribuzione.

   ![L'output mostra i risultati](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Esplora il set di scalabilità delle macchine virtuali<a name="exploring-your-virtual-machine-scale-set"></a>

Selezionare **Visualizza** > **Cloud Explorer** per visualizzare il nuovo set di scalabilità di macchine virtuali. Se necessario, utilizzare **Aggiorna tutto.**

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** consente di gestire le risorse di Azure in Visual Studio durante lo sviluppo di applicazioni. È anche possibile visualizzare il set di scalabilità di macchine virtuali nel [Portale di Azure](https://portal.azure.com) e in [Esplora inventario risorse di Azure](https://resources.azure.com/).

 Il portale offre il modo migliore per gestire l'infrastruttura di Azure con un Web browser. Azure Resource Explorer offre un modo semplice per esplorare ed eseguire il debug delle risorse di Azure.Azure Resource Explorer provides an easy way to explore and debug Azure resources. Azure Resource Explorer offre la visualizzazione dell'istanza e mostra anche i comandi di PowerShell per le risorse visualizzate.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito correttamente i set di scalabilità di macchine virtuali tramite Visual Studio, è possibile personalizzare ulteriormente il progetto in base ai requisiti dell'applicazione. Ad esempio, configurare la scalabilità automatica aggiungendo una risorsa **Insights.For** example, configure autoscale by adding an Insights resource. È possibile aggiungere l'infrastruttura al modello, ad esempio macchine virtuali autonome, o distribuire applicazioni usando l'estensione di script personalizzata. Modelli di esempio di Azure disponibili nel repository GitHub [dei modelli di avvio rapido](https://github.com/Azure/azure-quickstart-templates) di Azure.Good example templates can be found in the Azure Quickstart Templates GitHub repository. Cercare `vmss`.
