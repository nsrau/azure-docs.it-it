---
title: Aggiungere una VM con elementi a un lab in Azure DevTest Labs | Documentazione Microsoft
description: Informazioni su come aggiungere una VM con elementi in Azure DevTest Labs
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
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: aa6c87ef6e1f87cb47f68f9480ea068aa6262bf9


---
# <a name="add-a-vm-with-artifacts-to-a-lab-in-azure-devtest-labs"></a>Aggiungere una VM con elementi a un lab in Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-create-VMs-with-Artifacts-in-a-DevTest-Lab/player]
> 
> 

È possibile creare una macchina virtuale in un lab a partire da un elemento di *base* costituito da un'[immagine personalizzata](devtest-lab-create-template.md), da una [formula](devtest-lab-manage-formulas.md) o da un'[immagine di Marketplace](devtest-lab-configure-marketplace-images.md).

Gli *elementi* di DevTest Labs consentono di specificare le *azioni* eseguite quando viene creata la macchina virtuale. 

Le azioni degli elementi possono eseguire procedure, ad esempio script di Windows PowerShell e comandi Bash, e installare software. 

Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario.

Questo articolo descrive come creare una macchina virtuale nel lab usando gli elementi.

## <a name="add-a-vm-with-artifacts"></a>Aggiungere una macchina virtuale con elementi
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova VM.  
4. Nel pannello **Panoramica** del lab selezionare **+ Macchina virtuale**.  
    ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)
5. Nel pannello **Scegli una base** selezionare una base per la macchina virtuale.
6. Nel pannello **Macchina virtuale** immettere un nome per la nuova macchina virtuale nella casella di testo **Nome macchina virtuale**.
   
    ![Pannello Lab VM (VM lab)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)
7. Immettere un **Nome utente** a cui verranno concessi privilegi di amministratore nella macchina virtuale.  
8. Se si desidera usare una password archiviata nell' *archivio segreto*, selezionare **Usa segreti dall'archivio segreto**e specificare un valore chiave corrispondente al segreto (password). In alternativa, è sufficiente immettere una password nel campo di testo etichettato **Digita un valore**.
9. Selezionare **Dimensioni macchina virtuale** e uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
10. Selezionare **Rete virtuale** , quindi la rete virtuale desiderata.
11. Selezionare **Subnet** , quindi la subnet.
12. Se i criteri del lab sono impostati in modo da consentire gli indirizzi IP pubblici per la subnet selezionata, specificare se si vuole che l'indirizzo IP sia pubblico selezionando **Sì** o **No**. In caso contrario, questa opzione è disabilitata e impostata su **No**. 
13. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi da aggiungere all'immagine di base. 
    **Nota:** se non si ha familiarità con DevTest Labs o con la configurazione di elementi, passare alla sezione [Aggiungere un elemento esistente in una macchina virtuale](#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.
14. Se si vuole visualizzare o copiare il modello di Azure Resource Manager, passare alla sezione [Save Azure Resource Manager template](#save-azure-resource-manager-template) (Salva modello di Azure Resource Manager) e tornare qui al termine dell'operazione.
15. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.
16. Il pannello lab consente di visualizzare lo stato di creazione della macchina virtuale prima come **Creazione**, poi come **Esecuzione** dopo aver avviato la macchina virtuale.
17. Passare alla sezione [Passaggi successivi](#next-steps) . 

## <a name="add-an-existing-artifact-to-a-vm"></a>Aggiungere un elemento esistente in una macchina virtuale
Durante la creazione di una macchina virtuale, è possibile aggiungere elementi esistenti. Ogni lab include elementi del repository pubblico degli elementi di DevTest Labs ed elementi creati e aggiunti al repository degli elementi personale.
Per istruzioni sulla creazione di elementi, vedere l'articolo contenente informazioni su [Creare elementi personalizzati per le macchine virtuali di DevTest Labs](devtest-lab-artifact-author.md).

1. Nel pannello **Macchina virtuale** selezionare **Elementi**. 
2. Nel pannello **Aggiungi elementi** selezionare l'elemento desiderato.  
   
    ![Pannello Add Artifacts (Aggiungi elementi)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)
3. Immettere i valori dei parametri obbligatori e i parametri facoltativi necessari.  
4. Selezionare **Aggiungi** per aggiungere l'elemento e tornare al pannello **Aggiungi elementi**.
5. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.
6. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare indietro per [visualizzare o modificare un elemento](#view-or-modify-an-artifact).

## <a name="change-the-order-in-which-artifacts-are-run"></a>Modificare l'ordine di esecuzione degli elementi
Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del pannello **Aggiungi elementi** selezionare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. Per specificare l'ordine in cui eseguire gli elementi, trascinare e rilasciare gli elementi nell'ordine desiderato. **Nota:** se si verificano problemi di trascinamento dell'elemento, assicurarsi di trascinare dal lato sinistro dell'elemento. 
3. Selezionare **OK** al termine dell'operazione.  

## <a name="view-or-modify-an-artifact"></a>visualizzare o modificare un elemento
I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del pannello **Aggiungi elementi** selezionare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. Nel pannello **Selected Artifacts** (Elementi selezionati) selezionare l'elemento che si vuole visualizzare o modificare.  
3. Nel pannello **Aggiungi elemento** apportare le modifiche necessarie e selezionare **OK** per chiudere il pannello **Aggiungi elemento**.
4. Selezionare **OK** per chiudere il pannello **Elementi selezionati**.

## <a name="save-azure-resource-manager-template"></a>Save Azure Resource Manager template
Un modello di Azure Resource Manager permette di definire una distribuzione ripetibile in modo dichiarativo. I passaggi seguenti illustrano come salvare il modello di Azure Resource Manager per la VM da creare.
Dopo il salvataggio è possibile usare il modello di Azure Resource Manager per [distribuire nuove macchine virtuali con Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Nel pannello **Macchina virtuale** selezionare **View ARM Template** (Visualizza modello ARM).
2. Nel pannello **Visualizza modello Azure Resource Manager**selezionare il testo del modello.
3. Copiare il testo selezionato negli Appunti.
4. Selezionare **OK** per chiudere il pannello **View Azure Resource Manager Template** (Visualizza modello di Azure Resource Manager).
5. Aprire un editor di testo.
6. Incollare il testo del modello dagli Appunti.
7. Salvare il file per usarlo in seguito.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Dopo avere creato la VM, è possibile connettersi ad essa selezionando **Connetti** nel pannello della VM.
* Informazioni su come [creare elementi personalizzati per la VM di DevTest Labs](devtest-lab-artifact-author.md).
* Esplorare la [raccolta dei modelli ARM di avvio rapido di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)




<!--HONumber=Nov16_HO3-->


