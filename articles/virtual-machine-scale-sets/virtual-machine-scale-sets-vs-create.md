---
title: "Distribuire un set di scalabilità di macchine virtuali tramite Visual Studio | Microsoft Docs"
description: "Distribuire set di scalabilità di macchine virtuali tramite Visual Studio e un modello di Resource Manager"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78a4b0c8d305f57f495402cecb92d18425ff6bff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Come creare un set di scalabilità di macchine virtuali con Visual Studio
Questo articolo descrive come distribuire un set di scalabilità della macchina virtuale di Azure usando una distribuzione del gruppo di risorse di Visual Studio.

[Set di scalabilità di macchine virtuali di Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) è una risorsa di calcolo di Azure per distribuire e gestire una raccolta di macchine virtuali simili con scalabilità automatica e bilanciamento del carico. È possibile eseguire il provisioning e distribuire set di scalabilità di macchine virtuali tramite i [modelli di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). I modelli di Azure Resource Manager possono essere distribuiti tramite l'interfaccia della riga di comando di Azure, PowerShell, REST e direttamente da Visual Studio. Visual Studio offre un set di modelli di esempio che possono essere distribuiti come parte di un progetto di distribuzione del gruppo di risorse di Azure.

Le distribuzioni del gruppo di risorse di Azure sono un modo di raggruppare e pubblicare un set di risorse di Azure correlate con un'unica operazione di distribuzione. Sono disponibili altre informazioni in [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Prerequisiti
Per iniziare a distribuire set di scalabilità di macchine virtuali in Visual Studio è necessario quanto segue:

* Visual Studio 2013 o versioni successive
* Azure SDK 2.7, 2.8 o 2.9

>[!NOTE]
>Queste istruzioni presuppongono l'uso di Visual Studio con [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Creazione di un progetto
1. Creare un nuovo progetto in Visual Studio scegliendo **File | Nuovo | Progetto**.
   
    ![File Nuovo][file_new]

2. In **Visual C# | Cloud** scegliere **Azure Resource Manager** per creare un progetto per la distribuzione di un modello di Azure Resource Manager.
   
    ![Crea progetto][create_project]

3. Dall'elenco dei modelli, selezionare il modello di set di scalabilità della macchina virtuale Linux o Windows.
   
   ![Seleziona modello][select_Template]

4. Dopo aver creato il progetto saranno disponibili gli script di distribuzione di PowerShell, un modello di Azure Resource Manager e un file di parametri per il set di scalabilità di macchine virtuali.
   
    ![Esplora soluzioni][solution_explorer]

## <a name="customize-your-project"></a>Personalizzare il progetto
È ora possibile modificare il modello per personalizzarlo in base alle esigenze dell'applicazione, ad esempio aggiungendo proprietà di estensione della macchina virtuale o modificando le regole di bilanciamento del carico. Per impostazione predefinita, i modelli del set di scalabilità di macchine virtuali sono configurati in modo da distribuire l'estensione AzureDiagnostics, che semplifica l'aggiunta di regole di scalabilità automatica. L'estensione inoltre distribuisce un servizio di bilanciamento del carico con un indirizzo IP pubblico, configurato con regole NAT in entrata. 

Il servizio di bilanciamento del carico consente di connettersi alle istanze della macchina virtuale con SSH (Linux) o RDP (Windows). L'intervallo di porte front-end inizia da 50000. Per Linux ciò significa che se si usa SSH con la porta 50000, si viene instradati alla porta 22 della prima macchina virtuale nel set di scalabilità. La connessione alla porta 50001 determina l'instradamento alla porta 22 della seconda macchina virtuale e così via.

 Un buon metodo per modificare i modelli con Visual Studio consiste nell'usare la struttura JSON per organizzare i parametri, le variabili e le risorse. Comprendendo lo schema, Visual Studio è in grado di indicare errori nel modello prima che venga distribuito.

![Esplora JSON][json_explorer]

## <a name="deploy-the-project"></a>Distribuire il progetto
1. Distribuire il modello di Azure Resource Manager per creare la risorsa set di scalabilità di macchine virtuali. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Distribuisci | Nuova distribuzione**.
   
    ![Modello di distribuzione][5deploy_Template]
    
2. Selezionare la sottoscrizione nella finestra di dialogo "Distribuisci in gruppo di risorse".
   
    ![Modello di distribuzione][6deploy_Template]

3. Da qui è possibile creare un gruppo di risorse di Azure a cui distribuire il modello.
   
    ![Nuovo gruppo di risorse][new_resource]

4. Successivamente, fare clic su **Modifica parametri** per immettere i parametri trasmessi al modello. Specificare nome utente e password per il sistema operativo, che servono per creare la distribuzione. Se PowerShell Tools for Visual Studio non è installato, è consigliabile selezionare **Salva password** per evitare un prompt della riga di comando di PowerShell nascosto, oppure usare il [supporto dell'insieme di credenziali delle chiavi](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Modifica parametri][edit_parameters]

5. Fare quindi clic su **Distribuisci**. La finestra **Output** visualizza lo stato della distribuzione. Si noti che l'azione esegue lo script **Deploy-AzureResourceGroup.ps1** .
   
   ![Finestra Output][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Esplorare il set di scalabilità di macchine virtuali
Al termine della distribuzione, è possibile visualizzare il nuovo set di scalabilità di macchine virtuali in **Cloud Explorer** di Visual Studio (aggiornare l'elenco). Cloud Explorer consente di gestire le risorse di Azure in Visual Studio durante lo sviluppo di applicazioni. È anche possibile visualizzare il set di scalabilità di macchine virtuali nel [Portale di Azure](https://portal.azure.com) e in [Esplora inventario risorse di Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 Il portale rappresenta il modo migliore per gestire la visualizzazione dell'infrastruttura di Azure con un Web browser, mentre Azure Resource Explorer offre un modo semplice per esplorare le risorse di Azure ed eseguirne il debug, offrendo una "visualizzazione per istanza" e visualizzando anche i comandi PowerShell per le risorse che si stanno analizzando.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver distribuito i set di scalabilità di macchine virtuali tramite Visual Studio è possibile personalizzare ulteriormente il progetto in base alle esigenze dell'applicazione. Ad esempio, è possibile configurare la scalabilità automatica aggiungendo una risorsa di **Insights**, aggiungendo l'infrastruttura al modello (ad esempio macchine virtuali autonome) o distribuendo applicazioni con l'estensione dello script personalizzata. Alcuni esempi utili di modelli sono disponibili nel repository GitHub dedicato ai [modelli della guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates) (cercare "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
