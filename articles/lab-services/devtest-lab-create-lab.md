---
title: Creare un lab in Azure DevTest Labs | Documentazione Microsoft
description: Creare un lab in Azure DevTest Labs per macchine virtuali
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: c54b97bdf69908f32015631a9e527c6e289d1d2a
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080498"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creare un lab di sviluppo/test di Azure
Un lab in Azure DevTest Labs è l'infrastruttura che comprende un gruppo di risorse, ad esempio macchine virtuali, e ne consente una migliore gestione tramite la specifica di limiti e quote. Questo articolo illustra la creazione di un lab con il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per creare un lab sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Per altre informazioni sulle opzioni di acquisto di Azure, vedere [Come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). È necessario essere il proprietario della sottoscrizione per creare il lab.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Procedura per creare un lab in Azure DevTest Labs
La procedura seguente illustra come usare il portale di Azure per creare un lab in Azure DevTest Labs. 

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Nel menu principale a sinistra selezionare **Tutti i servizi** all'inizio dell'elenco. Selezionare * (stella) accanto a **DevTest Labs** nella sezione **DEVOPS**. Questa azione aggiunge **DevTest Labs** al menu di spostamento a sinistra in modo che sia facilmente accessibile la volta successiva. 

    ![Tutti i servizi - Selezionare DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Selezionare ora **DevTest Labs** nel menu di spostamento a sinistra. Sulla barra degli strumenti selezionare **Aggiungi**. 
   
    ![Aggiungere un lab](./media/devtest-lab-create-lab/add-lab-button.png)
1. Nella pagina **Crea un'istanza di DevTest Labs** eseguire queste operazioni: 
    1. Immettere un **nome** per il lab.
    2. Selezionare la **Sottoscrizione** da associare al lab.
    3. Immettere un **nome per il gruppo di risorse** del lab. 
    4. Selezionare un **percorso** in cui archiviare il lab.
    4. Selezionare **Arresto automatico** per indicare se si vogliono abilitare e definire i parametri per l'arresto automatico di tutte le macchine virtuali del lab. La funzionalità di arresto automatico è essenzialmente una funzionalità di riduzione dei costi che consente di specificare quando arrestare automaticamente la macchina virtuale. È possibile modificare le impostazioni di arresto automatico dopo la creazione del lab seguendo la procedura descritta nell'articolo [Gestire tutti i criteri per un lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Immettere le informazioni **NOME** e **VALORE** per **Tag** se si vuole creare un contrassegno personalizzato da aggiungere a ogni risorsa che si creerà nel lab. I tag sono utili per gestire e organizzare le risorse del lab per categoria. Per altre informazioni sui tag, ad esempio come aggiungere tag dopo avere creato il lab, vedere [Aggiungere tag a un lab](devtest-lab-add-tag.md).
    6. Selezionare **Opzioni di Automazione** per ottenere i modelli di Azure Resource Manager per l'automazione della configurazione. 
    7. Selezionare **Create**. È possibile monitorare lo stato del processo di creazione del lab esaminando l'area **Notifiche**. 
    
        ![Creare una sezione di lab di DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Al termine, selezionare **Vai alla risorsa** nella notifica. Al termine aggiornare la pagina **DevTest Labs** per visualizzare il lab appena creato nell'elenco dei lab.  Selezionare il lab nell'elenco. Viene visualizzata la home page del lab. 

        ![Home page del lab](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Una volta creato il lab, ecco alcuni passaggi successivi da considerare:

* [Proteggere l'accesso a un lab](devtest-lab-add-devtest-user.md)
* [Impostare i criteri del lab](devtest-lab-set-lab-policy.md)
* [Creare un modello di lab](devtest-lab-create-template.md)
* [Creare elementi personalizzati per le VM](devtest-lab-artifact-author.md)
* [Aggiungere una VM a un lab](devtest-lab-add-vm.md)

