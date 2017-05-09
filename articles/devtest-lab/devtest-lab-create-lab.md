---
title: Creare un lab in Azure DevTest Labs | Documentazione Microsoft
description: Creare un lab in Azure DevTest Labs per macchine virtuali
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 75188a8f74b08d28b92259348626d5734ed4ce6c
ms.contentlocale: it-it
ms.lasthandoff: 04/20/2017


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creare un lab di sviluppo/test di Azure
## <a name="prerequisites"></a>Prerequisiti
Per creare un lab sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Per altre informazioni sulle opzioni di acquisto di Azure, vedere [Come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). È necessario essere il proprietario della sottoscrizione per creare il lab.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Procedura per creare un lab in Azure DevTest Labs
La procedura seguente illustra come usare il portale di Azure per creare un lab in Azure DevTest Labs. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nel pannello **DevTest Labs** fare clic su **Aggiungi**.
   
    ![Aggiungere un lab](./media/devtest-lab-create-lab/add-lab-button.png)
4. Nel pannello **Crea lab DevTest** :
   
   1. Immettere un valore in **Nome lab** per il nuovo lab.
   2. Selezionare la **Sottoscrizione** da associare al lab.
   3. Selezionare un **Percorso** in cui archiviare il lab.
   4. Selezionare **Arresto automatico** per indicare se si vogliono abilitare e definire i parametri per l'arresto automatico di tutte le macchine virtuali del lab. 
   5. Selezionare **Aggiungi al dashboard** se si vuole visualizzare un collegamento al lab nel dashboard del portale.
   6. Selezionare **Opzioni di Automazione** per ottenere i modelli di Azure Resource Manager per l'automazione della configurazione. 
   7. Selezionare **Crea**.
    
    ![Creare un pannello lab](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Una volta creato il lab, ecco alcuni passaggi successivi da considerare:

* [Proteggere l'accesso a un lab](devtest-lab-add-devtest-user.md).
* [Definire i criteri del lab](devtest-lab-set-lab-policy.md).
* [Creare un modello di lab](devtest-lab-create-template.md).
* [Creare elementi personalizzati per le VM](devtest-lab-artifact-author.md).
* [Aggiungere una VM con elementi a un lab](devtest-lab-add-vm-with-artifacts.md).


