---
title: Gestire elementi di VM in Azure DevTest Labs | Documentazione Microsoft
description: Informazioni su come gestire elementi di VM in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>Gestire elementi di VM in Azure DevTest Labs
Gli *elementi* di Azure DevTest Labs consentono di specificare le *azioni* eseguite quando viene eseguito il provisioning della VM. 

Le azioni degli elementi possono eseguire procedure, ad esempio script di Windows PowerShell e comandi Bash, e installare software. 

Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario.

Questo articolo descrive come gestire gli elementi per una VM nel lab.

## <a name="add-an-existing-artifact-to-a-vm"></a>Aggiungere un elemento esistente in una macchina virtuale
Durante la creazione di una macchina virtuale, è possibile aggiungere elementi esistenti. Ogni lab include elementi del repository pubblico degli elementi di DevTest Labs ed elementi creati e aggiunti al repository degli elementi personale.
Per istruzioni sulla creazione di elementi, vedere l'articolo contenente informazioni su [Creare elementi personalizzati per le macchine virtuali di DevTest Labs](devtest-lab-artifact-author.md).

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco di lab selezionare il lab contenente la VM che si vuole usare.  
1. Selezionare **Macchine virtuali**.
1. Selezionare la VM desiderata.
1. Selezionare **Elementi**. 
1. Selezionare **Apply artifacts** (Applica elementi).
1. In **Apply artifacts** (Applica elementi) selezionare l'elemento da aggiungere alla VM.
1. Nel pannello **Aggiungi elemento** immettere i valori dei parametri obbligatori e i parametri facoltativi necessari.  
1. Selezionare **Aggiungi** per aggiungere l'elemento e tornare al pannello **Apply artifacts** (Applica elementi).
1. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.
1. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare indietro per [visualizzare o modificare un elemento](#view-or-modify-an-artifact).
1. Una volta aggiunti tutti gli elementi, selezionare **Applica**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Modificare l'ordine di esecuzione degli elementi
Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del pannello **Apply artifacts** (Applica elementi) selezionare il collegamento che indica il numero di elementi aggiunti alla VM.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Nel pannello **Selected artifacts** (Elementi selezionati) trascinare e rilasciare gli elementi nell'ordine desiderato. **Nota:** se si verificano problemi di trascinamento dell'elemento, assicurarsi di trascinare dal lato sinistro dell'elemento. 
1. Selezionare **OK** al termine dell'operazione.  

## <a name="view-or-modify-an-artifact"></a>visualizzare o modificare un elemento
I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del pannello **Apply artifacts** (Applica elementi) selezionare il collegamento che indica il numero di elementi aggiunti alla VM.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Nel pannello **Selected Artifacts** (Elementi selezionati) selezionare l'elemento che si vuole visualizzare o modificare.  
1. Nel pannello **Aggiungi elemento** apportare le modifiche necessarie e selezionare **OK** per chiudere il pannello **Aggiungi elemento**.
1. Selezionare **OK** per chiudere il pannello **Selected Artifacts** (Elementi selezionati).

## <a name="save-azure-resource-manager-template"></a>Save Azure Resource Manager template
Un modello di Azure Resource Manager permette di definire una distribuzione ripetibile in modo dichiarativo. I passaggi seguenti illustrano come salvare il modello di Azure Resource Manager per la VM da creare.
Dopo il salvataggio è possibile usare il modello di Azure Resource Manager per [distribuire nuove macchine virtuali con Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Nel pannello **Macchina virtuale** selezionare **View ARM Template** (Visualizza modello ARM).
2. Nel pannello **Visualizza modello Azure Resource Manager** selezionare il testo del modello.
3. Copiare il testo selezionato negli Appunti.
4. Selezionare **OK** per chiudere il pannello **View Azure Resource Manager Template** (Visualizza modello di Azure Resource Manager).
5. Aprire un editor di testo.
6. Incollare il testo del modello dagli Appunti.
7. Salvare il file per usarlo in seguito.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare elementi personalizzati per la VM di DevTest Labs](devtest-lab-artifact-author.md).

