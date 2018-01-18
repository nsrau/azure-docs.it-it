---
title: Visualizzare e usare un modello di Azure Resource Manager di una macchina virtuale |Microsoft Docs
description: Informazioni su come usare un modello di Azure Resource Manager di una macchina virtuale per creare altre macchine virtuali
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: v-craic
ms.openlocfilehash: 97822d5fb11c5c106c67aaaab0b8972e1ec8deee
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Creare macchine virtuali usando un modello di Azure Resource Manager 

Quando si crea una macchina virtuale (VM) in DevTest Labs tramite il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), è possibile visualizzare il modello di Azure Resource Manager prima di salvare la macchina virtuale. Il modello può essere usato come base per creare altre lab VM con le stesse impostazioni.

Questo articolo descrive il modello di Resource Manager di più macchine virtuali rispetto al modello di una singola macchina virtuale e illustra come visualizzare e salvare un modello quando si crea una macchina virtuale.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modello di Resource Manager di più macchine virtuali rispetto al modello di Resource Manager di una singola macchina virtuale
Esistono due modi per creare macchine virtuali in DevTest Labs usando un modello di Resource Manager: eseguire il provisioning della risorsa Microsoft.DevTestLab/labs/virtualmachines o eseguire il provisioning della risorsa Microsoft.Commpute/virtualmachines. Le due procedure vengono usate in diversi scenari e richiedono autorizzazioni differenti.

- I modelli di Resource Manager che usano un tipo di risorsa Microsoft.DevTestLab/labs/virtualmachines (come dichiarato nella proprietà “risorsa” nel modello) possono eseguire il provisioning di lab VM individuali. Ogni macchina virtuale viene quindi visualizzata come un singolo elemento nell'elenco delle macchine virtuali di DevTest Labs:

   ![Elenco delle macchine come singoli elementi nell'elenco delle macchine virtuali di DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   È possibile eseguire il provisioning di questo tipo di modello di Resource Manager mediante il comando Azure PowerShell **New-AzureRmResourceGroupDeployment** o mediante il comando dell'interfaccia della riga di comando di Azure **az group deployment create**. Richiede le autorizzazioni di amministratore, in modo che gli utenti assegnati a un ruolo utente DevTest Labs non possano eseguire la distribuzione. 

- Modelli di Resource Manager che usano un tipo di risorsa Microsoft.Compute/virtualmachines possono eseguire il provisioning di più macchine virtuali come un unico ambiente nell'elenco di macchine virtuali di DevTest Labs:

   ![Elenco delle macchine come singoli elementi nell'elenco delle macchine virtuali di DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Le macchine virtuali nello stesso ambiente possono essere gestite insieme e condividere lo stesso ciclo di vita. Gli utenti a cui è assegnato un ruolo utente DevTest Labs possono creare gli ambienti usando tali modelli, purché l'amministratore abbia configurato il lab in questo modo.

Il resto di questo articolo illustra i modelli di Resource Manager che usano Microsoft.DevTestLab/labs/virtualmachines. Questi vengono usati dagli amministratori dei lab per automatizzare la creazione di VM (ad esempio, macchine virtuali richiedibili) o la generazione dell'immagine predefinita (ad esempio, una factory di immagini).

[Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) offre molti suggerimenti e linee guida per creare modelli di Azure Resource Manager affidabili e semplici da usare.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Visualizzare e usare un modello di Resource Manager di Azure di una macchina virtuale
1. Seguire i passaggi in [Creare la prima macchina virtuale in un lab](devtest-lab-create-first-vm.md) per iniziare a creare una macchina virtuale.
1. Immettere le informazioni necessarie per la macchina virtuale e aggiungere eventuali elementi desiderati per la macchina virtuale.
1. Nella parte inferiore della finestra Configurazione impostazioni, scegliere **View ARM template** (Visualizza modello ARM).

   ![Pulsante View ARM template](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Copiare e salvare il modello di Resource Manager da usare in un secondo momento per creare un'altra macchina virtuale.

   ![Modello di Resource Manager da salvare per un uso successivo](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Dopo aver salvato il modello di Resource Manager, è necessario aggiornare la sezione parametri del modello prima che sia possibile usarlo. È possibile creare un parameter.json che consente di personalizzare solo i parametri, all'esterno del modello di Resource Manager effettivo. 

![Personalizzare i parametri usando un file JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Il modello di Resource Manager è ora pronto per [creare una macchina virtuale](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Creare un ambiente con più VM con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Deploy a Resource Manager template to create a VM](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm) (Distribuire un modello di Resource Manager per creare una macchina virtuale)
* È possibile esplorare altri modelli di avvio rapido di Resource Manager per l'automazione di DevTest Labs nel [repository pubblico GitHub per DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
