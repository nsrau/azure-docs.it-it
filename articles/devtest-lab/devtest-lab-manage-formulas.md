---
title: Gestire le formule in Azure DevTest Labs per creare VM | Microsoft Docs
description: "È possibile creare, aggiornare e rimuovere le formule in Azure DevTest Labs e usarle per creare nuove VM."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 52590f0c47954d115f39cbe988e04a0ed3feb94d
ms.openlocfilehash: cd3352ae30734cce116fda5a838f95e5e0b86a8a
ms.lasthandoff: 02/27/2017


---
# <a name="manage-devtest-labs-formulas-to-create-vms"></a>Gestire le formule dei lab di sviluppo/test per creare macchine virtuali
Una formula è un elenco di valori predefiniti di proprietà usati in Azure DevTest Labs per creare una macchina virtuale (VM). Quando si crea una macchina virtuale da una formula, i valori predefiniti possono essere modificati o usati così come sono. Le formule, ad esempio le [immagini personalizzate](devtest-lab-create-template.md) e le [immagini Marketplace](devtest-lab-configure-marketplace-images.md), offrono un meccanismo in grado di velocizzare il provisioning delle macchine virtuali.  

Questo articolo spiega come eseguire le attività seguenti:

* [Creare una formula](#create-a-formula)
* [Usare una formula per il provisioning di una VM](#use-a-formula-to-provision-a-vm)
* [Modificare una formula](#modify-a-formula)
* [Eliminare una formula](#delete-a-formula)

> [!NOTE]
> Le formule, ad esempio [immagini personalizzate](devtest-lab-create-template.md), consentono di creare un'immagine di base da un file VHD. L'immagine di base può quindi essere usata per il provisioning di una nuova VM. Per stabilire la formula più corretta per un ambiente specifico, vedere l'articolo [Confronto tra immagini personalizzate e formule nei lab di sviluppo/test](devtest-lab-comparing-vm-base-image-types.md).
> 
> 

## <a name="create-a-formula"></a>Creare una formula
Tutti gli utenti con autorizzazione *Utenti* per i lab di sviluppo/test possono creare macchine virtuali usando una formula come base. È possibile creare le formule in due modi: 

* Da una base: quando si vogliono definire tutte le caratteristiche della formula.
* Da una macchina virtuale di lab già esistente: quando si vuole creare una formula basata sulle impostazioni di una macchina virtuale esistente.

### <a name="create-a-formula-from-a-base"></a>Creare una formula di base
Nella procedura seguente sono descritti i passaggi per creare una formula da un'immagine personalizzata, un'immagine Marketplace o un'altra formula.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nel pannello del lab selezionare **Formule (basi riutilizzabili)**.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nel pannello **Lab formulas** (Formule lab) selezionare **+ Add** (+ Aggiungi).
   
    ![Aggiungere una formula](./media/devtest-lab-manage-formulas/add-formula.png)
6. Nel pannello **Scegli una base** selezionare la base (immagine personalizzata, immagine Marketplace o formula) da cui si desidera creare la formula.
   
    ![Elenco base](./media/devtest-lab-manage-formulas/base-list.png)
7. Specificare i valori seguenti nel pannello **Crea formula** :
   
   * **Nome formula** : immettere un nome per la formula. Questo valore verrà visualizzato nell'elenco delle immagini di base quando si crea una macchina virtuale. Il nome viene convalidato durante la digitazione e, se non è valido, un messaggio indicherà i requisiti per un nome valido.
   * **Descrizione** : immettere una descrizione significativa per la formula. Questo valore è disponibile dal menu di scelta rapida della formula quando si crea una macchina virtuale.
   * **Nome utente** : immettere un nome utente a cui verranno concessi privilegi di amministratore.
   * **Password** : immettere o scegliere dall'elenco a discesa un valore associato al segreto (password) che si desidera usare per l'utente specificato.  
   * **Immagine** : questo campo visualizza il nome dell'immagine di base selezionata nel pannello precedente. 
   * **Dimensioni macchina virtuale** : selezionare uno degli elementi predefiniti che specificano i core del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
   * **Rete virtuale** : specificare la rete virtuale desiderata.
   * **Subnet** : specificare la subnet desiderata.
   * **Indirizzo IP pubblico**: se i criteri del lab sono impostati per consentire gli indirizzi IP pubblici per la subnet selezionata, scegliere **Sì** o **No** per indicare se si desidera o meno rendere pubblico l'indirizzo IP. In caso contrario, questa opzione è disabilitata e impostata su **No**.
   * **Elementi** : selezionare e configurare gli elementi che verranno aggiunti all'immagine di base. I valori delle stringhe sicure non vengono salvati con la formula. Pertanto, i parametri degli elementi corrispondenti a stringhe sicure non vengono visualizzati. 
     
       ![Crea formula](./media/devtest-lab-manage-formulas/create-formula.png)
8. Selezionare **Crea** per creare la formula.

### <a name="create-a-formula-from-a-vm"></a>Creare una formula da una VM
La procedura seguente consente di creare una formula basata su una macchina virtuale già esistente. 

> [!NOTE]
> Per creare una formula da una VM, la VM deve essere stata creata dopo il 30 marzo 2016. 
> 
> 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nel pannello **Panoramica** del lab selezionare la VM dalla quale creare la formula.
   
    ![Macchine virtuali di lab](./media/devtest-lab-manage-formulas/my-vms.png)
5. Nel pannello della VM selezionare **Crea formula (base riutilizzabile)**.
   
    ![Crea formula](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. Nel pannello **Crea formula** compilare i campi **Name** (Nome) e **Description** (Descrizione) della nuova formula.
   
    ![Pannello Crea formula](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. Fare clic su **OK** per creare la formula.

## <a name="use-a-formula-to-provision-a-vm"></a>Usare una formula per il provisioning di una VM
Dopo aver creato una formula, è possibile creare una VM in base a tale formula. La procedura è descritta nell'articolo [Aggiungere una macchina virtuale con elementi](devtest-lab-add-vm-with-artifacts.md).

## <a name="modify-a-formula"></a>Modificare una formula
Per modificare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nel pannello del lab selezionare **Formule (basi riutilizzabili)**.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nel pannello **Formule lab** selezionare la formula che si vuole modificare.
6. Apportare le modifiche nel pannello **Update formula** (Aggiorna formula) e selezionare **Update** (Aggiorna).

## <a name="delete-a-formula"></a>Eliminare una formula
Per eliminare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nel pannello **Settings** (Impostazioni) del lab selezionare **Formulas** (Formule).
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nel pannello **Formule lab** selezionare i puntini di sospensione a destra della formula da eliminare.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Selezionare **Elimina**dal menu di scelta rapida della formula.
   
    ![Menu di scelta rapida Formula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selezionare **Sì** nella finestra di dialogo di conferma dell'eliminazione.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato una formula da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale al lab](devtest-lab-add-vm-with-artifacts.md).


