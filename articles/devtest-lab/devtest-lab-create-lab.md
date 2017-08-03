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
ms.date: 05/30/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 265a968f902f53c7561c8c7e937f8eacfdb37167
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creare un lab di sviluppo/test di Azure
Un lab in Azure DevTest Labs è l'infrastruttura che comprende un gruppo di risorse, ad esempio macchine virtuali, e ne consente una migliore gestione tramite la specifica di limiti e quote. Questo articolo illustra la creazione di un lab con il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per creare un lab sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Per altre informazioni sulle opzioni di acquisto di Azure, vedere [Come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). È necessario essere il proprietario della sottoscrizione per creare il lab.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Procedura per creare un lab in Azure DevTest Labs
La procedura seguente illustra come usare il portale di Azure per creare un lab in Azure DevTest Labs. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Dal menu principale a sinistra selezionare **Altri servizi** in fondo all'elenco.

    ![Opzione di menu Altri servizi](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. Dall'elenco dei servizi disponibili selezionare **DevTest Labs**.
1. Nel pannello **DevTest Labs** fare clic su **Aggiungi**.
   
    ![Aggiungere un lab](./media/devtest-lab-create-lab/add-lab-button.png)

1. Nel pannello **Crea lab DevTest** :
   
    1. Immettere un valore in **Nome lab** per il nuovo lab.
    2. Selezionare la **Sottoscrizione** da associare al lab.
    3. Selezionare un **Percorso** in cui archiviare il lab.
    4. Selezionare **Arresto automatico** per indicare se si vogliono abilitare e definire i parametri per l'arresto automatico di tutte le macchine virtuali del lab. La funzionalità di arresto automatico è essenzialmente una funzionalità di riduzione dei costi che consente di specificare quando arrestare automaticamente la macchina virtuale. È possibile modificare le impostazioni di arresto automatico dopo la creazione del lab seguendo la procedura descritta nell'articolo [Gestire tutti i criteri per un lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    5. Selezionare **Aggiungi al dashboard** se si vuole visualizzare un collegamento al lab nel dashboard del portale.
    6. Selezionare **Opzioni di Automazione** per ottenere i modelli di Azure Resource Manager per l'automazione della configurazione. 
    7. Selezionare **Crea**. Dopo aver selezionato **Crea** verrà visualizzato il pannello **DevTest Labs**. È possibile monitorare lo stato del processo di creazione del lab esaminando l'area **Notifiche**. Al termine aggiornare la pagina per visualizzare il lab appena creato nell'elenco dei lab.  
    
    ![Creare un pannello lab](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Una volta creato il lab, ecco alcuni passaggi successivi da considerare:

* [Proteggere l'accesso a un lab](devtest-lab-add-devtest-user.md).
* [Definire i criteri del lab](devtest-lab-set-lab-policy.md).
* [Creare un modello di lab](devtest-lab-create-template.md).
* [Creare elementi personalizzati per le VM](devtest-lab-artifact-author.md).
* [Aggiungere una VM con elementi a un lab](https://azure.microsoft.com/resources/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab/).


