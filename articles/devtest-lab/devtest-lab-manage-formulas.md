---
title: Gestire le formule in Azure DevTest Labs per creare VM | Microsoft Docs
description: Informazioni su come aggiornare e rimuovere le formule di Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: v-craic
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3dcd285761774c3cd1050976894f1f15db61b52c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gestire le formule di Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

In questo articolo viene illustrato come creare una formula da una base (immagine personalizzata, immagine del Marketplace o un'altra formula) o da una macchina virtuale esistente. Questo articolo illustra inoltre la gestione delle formule esistenti.

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
    * **Elementi** - Selezionare questa opzione per aprire il pannello **Aggiungi elementi** nel quale è possibile selezionare e configurare gli elementi da aggiungere all'immagine di base. Per ulteriori informazioni su elementi, vedere [creare gli elementi personalizzati per la macchina virtuale di Azure DevTest Labs](devtest-lab-artifact-author.md).
    * **Impostazioni avanzate** - Selezionare questa opzione per aprire il pannello **Avanzate** in cui è possibile configurare le impostazioni seguenti:
        * **Rete virtuale** : specificare la rete virtuale desiderata.
        * **Subnet** : specificare la subnet desiderata.    
        * **Configurazione indirizzi IP** - Specificare se si desidera l'indirizzo IP pubblico, privato o condiviso. Per altre informazioni sugli indirizzi IP condivisi, vedere [Understand shared IP addresses in Azure DevTest Labs](./devtest-lab-shared-ip.md) (Informazioni sugli indirizzi IP condivisi in Azure Devtest Labs).
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
Dopo avere creato una formula da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale al lab](devtest-lab-add-vm.md).

