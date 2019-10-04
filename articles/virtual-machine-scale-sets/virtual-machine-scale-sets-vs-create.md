---
title: Distribuire un set di scalabilità di macchine virtuali tramite Visual Studio | Microsoft Docs
description: Distribuire set di scalabilità di macchine virtuali tramite Visual Studio e un modello di Resource Manager
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: d397f81ce29e0ec738156b755948985a4edfc70b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802264"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Come creare un set di scalabilità di macchine virtuali con Visual Studio

Questo articolo illustra come distribuire un set di scalabilità di macchine virtuali di Azure usando una distribuzione del gruppo di risorse di Visual Studio.

I [set di scalabilità di macchine virtuali di Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) sono una risorsa di calcolo di Azure per distribuire e gestire una raccolta di macchine virtuali simili con scalabilità automatica e bilanciamento del carico. È possibile eseguire il provisioning e distribuire set di scalabilità di macchine virtuali tramite i [modelli di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager modelli possono essere distribuiti usando l'interfaccia della riga di comando di Azure, PowerShell, REST e anche direttamente da Visual Studio. Visual Studio offre un set di modelli di esempio, che è possibile distribuire come parte di un progetto di distribuzione del gruppo di risorse di Azure.

Le distribuzioni del gruppo di risorse di Azure sono un modo di raggruppare e pubblicare un set di risorse di Azure correlate con un'unica operazione di distribuzione. Per altre informazioni, vedere [creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a distribuire set di scalabilità di macchine virtuali in Visual Studio, sono necessari i prerequisiti seguenti:

* Visual Studio 2013 o versioni successive
* Azure SDK 2.7, 2.8 o 2.9

>[!NOTE]
>Questo articolo usa Visual Studio 2019 con [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## Creazione di un progetto<a name="creating-a-project"></a> 

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Crea un nuovo progetto**scegliere **gruppo di risorse** di Azure C# per e quindi fare clic su **Avanti**.

1. In **Configura il nuovo progetto**immettere un nome e selezionare **Crea**.

    ![Nome e creazione del progetto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Dall'elenco dei modelli scegliere il modello di set di **scalabilità di macchine virtuali Windows** o **set di scalabilità di macchine virtuali Linux** . Scegliere **OK**.

   ![Selezionare un modello di macchina virtuale](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Dopo aver creato il progetto, **Esplora soluzioni** contiene uno script di distribuzione di PowerShell, un modello di Azure Resource Manager e un file di parametri per il set di scalabilità di macchine virtuali.

## <a name="customize-your-project"></a>Personalizzare il progetto

A questo punto è possibile modificare il modello per personalizzarlo in base alle esigenze dell'applicazione. È possibile aggiungere le proprietà di estensione della macchina virtuale o modificare le regole di bilanciamento del carico. Per impostazione predefinita, i modelli del set di scalabilità di macchine virtuali sono configurati per distribuire l'estensione **AzureDiagnostics** , semplificando così l'aggiunta delle regole di scalabilità automatica. I modelli distribuiscono anche un servizio di bilanciamento del carico con un indirizzo IP pubblico, configurato con le regole NAT in ingresso.

Il servizio di bilanciamento del carico consente di connettersi alle istanze di macchine virtuali con SSH (Linux) o RDP (Windows). L'intervallo di porte front-end inizia da 50000. Per Linux, se si esegue la connessione SSH alla porta 50000, il bilanciamento del carico indirizza la porta 22 della prima macchina virtuale nel set di scalabilità. La connessione alla porta 50001 viene indirizzata alla porta 22 della seconda macchina virtuale e così via.

 Un modo efficace per modificare i modelli con Visual Studio consiste nell'usare il **contorno JSON**. È possibile organizzare i parametri, le variabili e le risorse. Con una conoscenza dello schema, Visual Studio può evidenziare gli errori nel modello prima di distribuirlo.

![Esplora JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Distribuire il progetto

Distribuire il modello di Azure Resource Manager per creare la risorsa del set di scalabilità di macchine virtuali:

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Distribuisci** > **nuovo**.

    ![Distribuire il progetto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. In **Distribuisci in gruppo di risorse**scegliere la sottoscrizione da usare e selezionare un gruppo di risorse. Se necessario, è possibile creare un gruppo di risorse.

1. Selezionare quindi **modifica parametri** per immettere i parametri passati al modello.

   ![Immettere la sottoscrizione e il gruppo di risorse](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Fornire il nome utente e la password per il sistema operativo. Questi valori sono necessari per creare la distribuzione. Se PowerShell Tools for Visual Studio non è installato, selezionare **Salva password** per evitare un prompt dei comandi di PowerShell nascosto oppure usare [Key Vault supporto](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Selezionare **Save (Salva** ) per continuare.

    ![Modifica parametri di distribuzione](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. In **Distribuisci in gruppo di risorse**selezionare **Distribuisci**. L'azione esegue lo script **deploy-azureresourcegroup. ps1** . La finestra **Output** visualizza lo stato della distribuzione.

   ![Output Mostra i risultati](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## Esplorare il set di scalabilità di macchine virtuali<a name="exploring-your-virtual-machine-scale-set"></a>

Selezionare **visualizza** > **Cloud Explorer** per visualizzare il nuovo set di scalabilità di macchine virtuali. Se necessario, utilizzare **Refresh all**.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** consente di gestire le risorse di Azure in Visual Studio durante lo sviluppo di applicazioni. È anche possibile visualizzare il set di scalabilità di macchine virtuali nel [Portale di Azure](https://portal.azure.com) e in [Esplora inventario risorse di Azure](https://resources.azure.com/).

 Il portale rappresenta il modo migliore per gestire l'infrastruttura di Azure con un Web browser. Azure Resource Explorer offre un modo semplice per esplorare ed eseguire il debug delle risorse di Azure. Azure Resource Explorer offre la visualizzazione dell'istanza e Mostra anche i comandi di PowerShell per le risorse che si stanno analizzando.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito i set di scalabilità di macchine virtuali tramite Visual Studio è possibile personalizzare ulteriormente il progetto in base alle esigenze dell'applicazione. Ad esempio, configurare la scalabilità automatica aggiungendo una risorsa di **Insights** . È possibile aggiungere un'infrastruttura al modello, ad esempio macchine virtuali autonome o distribuire applicazioni usando l'estensione script personalizzata. Modelli di esempio validi sono disponibili nel repository GitHub dei [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates) . Cercare `vmss`.
