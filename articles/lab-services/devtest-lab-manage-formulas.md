---
title: Gestire le formule in Azure DevTest Labs per creare VM | Microsoft Docs
description: Informazioni su come aggiornare e rimuovere le formule di Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562532"
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

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.

3. Nell'elenco dei lab selezionare il lab desiderato.  

4. Nella pagina del lab selezionare **Formule (basi riutilizzabili)** .
   
    ![Menu Formula](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Nella pagina **Formule** selezionare **+ Aggiungi**.
   
    ![Aggiungere una formula](./media/devtest-lab-create-formulas/add-formula.png)

6. Nella pagina **Scegli una base** selezionare la base (immagine personalizzata, immagine Marketplace o formula) da cui si vuole creare la formula.
   
    ![Elenco base](./media/devtest-lab-create-formulas/base-list.png)

7. Nella scheda **Impostazioni di base** della pagina **Crea formula** specificare i valori seguenti:
   
    * **Nome formula** : immettere un nome per la formula. Questo valore verrà visualizzato nell'elenco delle immagini di base quando si crea una macchina virtuale. Il nome viene convalidato durante la digitazione e, se non è valido, un messaggio indicherà i requisiti per un nome valido.
    * **Nome utente** - Immettere un nome utente a cui siano concessi i privilegi di amministratore.
    * **Password** : immettere o scegliere dall'elenco a discesa un valore associato al segreto (password) che si desidera usare per l'utente specificato. Per informazioni sul salvataggio dei segreti in un insieme di credenziali delle chiavi e sull'uso durante la creazione delle risorse di lab, vedere [Archiviare segreti in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Dimensioni macchina virtuale**: selezionare **Modifica dimensioni** per modificare le dimensioni della macchina virtuale. 
    * **Artefatti**: selezionare la pagina **Aggiungi o rimuovi artefatti** in cui è possibile selezionare e configurare gli artefatti da aggiungere all'immagine di base. Per altre informazioni sugli elementi, vedere [Create custom artifacts for your Azure DevTest Labs virtual machine](devtest-lab-artifact-author.md) (Creare elementi personalizzati per la macchina virtuale di Azure DevTest Labs).
8. Passare alla scheda **Impostazioni avanzate** e specificare i valori seguenti:
    - **Rete virtuale**: per cambiare la rete virtuale, selezionare **Cambia rete virtuale**. 
    - **Subnet**: per cambiare la subnet, selezionare **Cambia subnet**. 
    - **Configurazione indirizzi IP** - Specificare se si desidera l'indirizzo IP pubblico, privato o condiviso. Per altre informazioni sugli indirizzi IP condivisi, vedere [Understand shared IP addresses in Azure DevTest Labs](./devtest-lab-shared-ip.md) (Informazioni sugli indirizzi IP condivisi in Azure Devtest Labs).
    - **Data e ora scadenza**: specificare la data e l'ora di scadenza della macchina virtuale in modo che venga eliminata automaticamente. 
    - **Make this machine claimable** (Rendi attestabile questa macchina) - Rendere "attestabile" una macchina significa che non le sarà assegnata la proprietà al momento della creazione. Gli utenti del lab saranno invece in grado di assumere la proprietà ("attestazione") della macchina nella pagina del lab.     
    - **Numero di istanze a disposizione degli utenti** -specificare il numero di istanze a disposizione degli utenti che si desidera creare. 
8. Selezionare **Invia** per creare la formula.

9. Dopo essere stata creata, la formula viene visualizzata nell'elenco della pagina **Formule**.

### <a name="create-a-formula-from-a-vm"></a>Creare una formula da una VM
La procedura seguente consente di creare una formula basata su una macchina virtuale già esistente. 

> [!NOTE]
> Per creare una formula da una VM, la VM deve essere stata creata dopo il 30 marzo 2016. 
> 
> 

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nella pagina **Panoramica** del lab selezionare la macchina virtuale dalla quale creare la formula.
   
    ![Macchine virtuali di lab](./media/devtest-lab-create-formulas/my-vms.png)
5. Nella pagina della macchina virtuale selezionare **Crea formula (base riutilizzabile)** .
   
    ![Crea formula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Nella pagina **Crea formula** compilare i campi **Nome** e **Descrizione** della nuova formula.
   
    ![Pagina Crea formula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Fare clic su **OK** per creare la formula.

## <a name="modify-a-formula"></a>Modificare una formula
Per modificare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nella pagina del lab selezionare **Formule (basi riutilizzabili)** .
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nella pagina **Formule lab** selezionare la formula che si vuole modificare.
6. Nella pagina **Aggiorna formula** apportare le modifiche desiderate e selezionare **Aggiorna**.

## <a name="delete-a-formula"></a>Eliminare una formula
Per eliminare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nella pagina **Impostazioni** del lab selezionare **Formule**.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nella pagina **Formule lab** selezionare i puntini di sospensione a destra della formula da eliminare.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Selezionare **Elimina**dal menu di scelta rapida della formula.
   
    ![Menu di scelta rapida Formula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selezionare **Sì** nella finestra di dialogo di conferma dell'eliminazione.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato una formula da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale al lab](devtest-lab-add-vm.md).

