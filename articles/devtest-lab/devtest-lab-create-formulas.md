---
title: Creare formule in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare formule in Azure DevTest Labs e usarle per creare nuove macchine virtuali.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c18b0eefb2d8bb43416e15c5b91cf10827f90089
ms.lasthandoff: 03/15/2017


---
# <a name="create-azure-devtest-labs-formulas"></a>Creare formule in Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

In questo articolo viene illustrato come creare una formula da una base (immagine personalizzata, immagine del Marketplace o un'altra formula) o da una macchina virtuale esistente. 

## <a name="create-a-formula"></a>Creare una formula
Tutti gli utenti con autorizzazione *Utenti* per i lab di sviluppo/test possono creare macchine virtuali usando una formula come base. È possibile creare le formule in due modi: 

* Da una base: quando si vogliono definire tutte le caratteristiche della formula.
* Da una macchina virtuale di lab già esistente: quando si vuole creare una formula basata sulle impostazioni di una macchina virtuale esistente.

Per altre informazioni sull'aggiunta di utenti e autorizzazioni, vedere [Aggiungere proprietari e utenti in Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Creare una formula di base
Nella procedura seguente sono descritti i passaggi per creare una formula da un'immagine personalizzata, un'immagine Marketplace o un'altra formula.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

3. Nell'elenco dei lab selezionare il lab desiderato.  

4. Nel pannello del lab selezionare **Formule (basi riutilizzabili)**.
   
    ![Menu Formula](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Nel pannello **Formule** selezionare **+ Aggiungi**.
   
    ![Aggiungere una formula](./media/devtest-lab-create-formulas/add-formula.png)

6. Nel pannello **Scegli una base** selezionare la base (immagine personalizzata, immagine Marketplace o formula) da cui si desidera creare la formula.
   
    ![Elenco base](./media/devtest-lab-create-formulas/base-list.png)

7. Specificare i valori seguenti nel pannello **Crea formula** :
   
    * **Nome formula** : immettere un nome per la formula. Questo valore verrà visualizzato nell'elenco delle immagini di base quando si crea una macchina virtuale. Il nome viene convalidato durante la digitazione e, se non è valido, un messaggio indicherà i requisiti per un nome valido.
    * **Descrizione** : immettere una descrizione significativa per la formula. Questo valore è disponibile dal menu di scelta rapida della formula quando si crea una macchina virtuale.
    * **Nome utente** - Immettere un nome utente a cui siano concessi i privilegi di amministratore.
    * **Password** : immettere o scegliere dall'elenco a discesa un valore associato al segreto (password) che si desidera usare per l'utente specificato. Per altre informazioni sui segreti, vedere [Azure DevTest Labs: Personal secret store](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/) (Azure DevTest Labs: archivio personale dei segreti).
    * **Virtual machine disk type** (Tipo di disco della macchina virtuale) - Specificare l'unità disco rigido o l'unità SSD per indicare quale tipo di disco di archiviazione sia consentito per le macchine virtuali il cui provisioning è stato effettuato usando questa immagine di base.
    * **Dimensioni macchina virtuale** - Selezionare uno degli elementi predefiniti che specificano le memorie centrali del processore, le dimensioni della RAM e le dimensioni dell'unità disco rigido della macchina virtuale da creare. 
    * **Elementi** - Selezionare questa opzione per aprire il pannello **Aggiungi elementi** nel quale è possibile selezionare e configurare gli elementi da aggiungere all'immagine di base. Per altre informazioni sugli elementi, vedere [Gestire elementi di macchine virtuali in Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md).
    * **Impostazioni avanzate** - Selezionare questa opzione per aprire il pannello **Avanzate** in cui è possibile configurare le impostazioni seguenti:
        * **Rete virtuale** : specificare la rete virtuale desiderata.
        * **Subnet** : specificare la subnet desiderata.    
        * **Configurazione indirizzi IP** - Specificare se si desidera l'indirizzo IP pubblico, privato o condiviso. Per informazioni sugli indirizzi IP condivisi, vedere Understand shared IP addresses in Azure DevTest Labs (Informazioni sugli indirizzi IP condivisi in Azure DevTest Labs).
        * **Make this machine claimable** (Rendi attestabile questa macchina) - Rendere "attestabile" una macchina significa che non le sarà assegnata la proprietà al momento della creazione. Gli utenti del lab saranno invece in grado di assumere la proprietà ("attestazione") della macchina nel pannello del lab.     
    * **Immagine** : questo campo visualizza il nome dell'immagine di base selezionata nel pannello precedente. 
     
       ![Crea formula](./media/devtest-lab-create-formulas/create-formula.png)

8. Selezionare **Crea** per creare la formula.

9. Dopo essere stata creata, la formula viene visualizzata nell'elenco del pannello **Formule**.

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
   
    ![Macchine virtuali di lab](./media/devtest-lab-create-formulas/my-vms.png)
5. Nel pannello della VM selezionare **Crea formula (base riutilizzabile)**.
   
    ![Crea formula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Nel pannello **Crea formula** compilare i campi **Name** (Nome) e **Description** (Descrizione) della nuova formula.
   
    ![Pannello Crea formula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Fare clic su **OK** per creare la formula.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere una macchina virtuale in un lab in Azure DevTest Labs](devtest-lab-add-vm.md)
- [Gestire le formule di Azure DevTest Labs](devtest-lab-manage-formulas.md)
