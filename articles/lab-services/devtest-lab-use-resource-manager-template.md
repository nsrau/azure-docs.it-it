---
title: Visualizzare e usare un modello di Azure Resource Manager di una macchina virtuale |Microsoft Docs
description: Informazioni su come usare un modello di Azure Resource Manager di una macchina virtuale per creare altre macchine virtuali
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: c14abf3acce0084507a03f3d34fdd59566d88c28
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854284"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Creare macchine virtuali usando un modello di Azure Resource Manager 

Quando si crea una macchina virtuale (VM) in DevTest Labs tramite il [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), è possibile visualizzare il modello di Azure Resource Manager prima di salvare la macchina virtuale. Il modello può essere usato come base per creare altre lab VM con le stesse impostazioni.

Questo articolo descrive il modello di Resource Manager di più macchine virtuali rispetto al modello di una singola macchina virtuale e illustra come visualizzare e salvare un modello quando si crea una macchina virtuale.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modello di Resource Manager di più macchine virtuali rispetto al modello di Resource Manager di una singola macchina virtuale
Esistono due modi per creare macchine virtuali in DevTest Labs usando un modello di Gestione risorse: effettuare il provisioning della risorsa Microsoft. DevTestLab/Labs/VirtualMachines oppure effettuare il provisioning della risorsa Microsoft. Compute/VirtualMachines. Le due procedure vengono usate in diversi scenari e richiedono autorizzazioni differenti.

- I modelli di Resource Manager che usano un tipo di risorsa Microsoft.DevTestLab/labs/virtualmachines (come dichiarato nella proprietà “risorsa” nel modello) possono eseguire il provisioning di lab VM individuali. Ogni macchina virtuale viene quindi visualizzata come un singolo elemento nell'elenco delle macchine virtuali di DevTest Labs:

   ![Elenco delle macchine come singoli elementi nell'elenco delle macchine virtuali di DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   È possibile eseguire il provisioning di questo tipo di modello di Gestione risorse tramite il comando Azure PowerShell **New-AzResourceGroupDeployment** o tramite il comando dell'interfaccia della riga di comando di Azure **AZ Group Deployment create**. Richiede le autorizzazioni di amministratore, in modo che gli utenti assegnati a un ruolo utente DevTest Labs non possano eseguire la distribuzione. 

- Modelli di Resource Manager che usano un tipo di risorsa Microsoft.Compute/virtualmachines possono eseguire il provisioning di più macchine virtuali come un unico ambiente nell'elenco di macchine virtuali di DevTest Labs:

   ![Elenco delle macchine come singoli elementi nell'elenco delle macchine virtuali di DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Le macchine virtuali nello stesso ambiente possono essere gestite insieme e condividere lo stesso ciclo di vita. Gli utenti a cui è assegnato un ruolo utente DevTest Labs possono creare gli ambienti usando tali modelli, purché l'amministratore abbia configurato il lab in questo modo.

Il resto di questo articolo illustra i modelli di Resource Manager che usano Microsoft.DevTestLab/labs/virtualmachines. Questi vengono usati dagli amministratori dei lab per automatizzare la creazione di VM (ad esempio, macchine virtuali richiedibili) o la generazione dell'immagine predefinita (ad esempio, una factory di immagini).

[Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) offre molti suggerimenti e linee guida per creare modelli di Azure Resource Manager affidabili e semplici da usare.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Visualizzare e usare un modello di Resource Manager di Azure di una macchina virtuale
1. Seguire i passaggi in [Creare la prima macchina virtuale in un lab](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) per iniziare a creare una macchina virtuale.
1. Immettere le informazioni necessarie per la macchina virtuale e aggiungere eventuali elementi desiderati per la macchina virtuale.
1. Opzione alla scheda **Impostazioni avanzate** . 
1. Nella parte inferiore della finestra Configurazione impostazioni, scegliere **View ARM template** (Visualizza modello ARM).
1. Copiare e salvare il modello di Resource Manager da usare in un secondo momento per creare un'altra macchina virtuale.

   ![Modello di Resource Manager da salvare per un uso successivo](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Dopo aver salvato il modello di Resource Manager, è necessario aggiornare la sezione parametri del modello prima che sia possibile usarlo. È possibile creare un parameter.json che consente di personalizzare solo i parametri, all'esterno del modello di Resource Manager effettivo. 

![Personalizzare i parametri usando un file JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Il modello di Resource Manager è ora pronto per [creare una macchina virtuale](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Impostare la data di scadenza
In scenari come il training, le demo e le versioni di valutazione, è possibile creare macchine virtuali ed eliminarle automaticamente dopo una durata fissa, in modo da non incorrere in costi superflui. È possibile creare una macchina virtuale Lab con una data di scadenza specificando la proprietà **expirationDate** per la macchina virtuale. Esaminare lo stesso modello di Gestione risorse nel [repository GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Creare un ambiente con più VM con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Deploy a Resource Manager template to create a VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments) (Distribuire un modello di Resource Manager per creare una macchina virtuale)
* È possibile esplorare altri modelli di avvio rapido di Resource Manager per l'automazione di DevTest Labs nel [repository pubblico GitHub per DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
