---
title: Aggiungere una VM in un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere una macchina virtuale in un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: v-craic
ms.openlocfilehash: c9395966c83626d09a556442e9f0b33c4995af76
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Aggiungere una VM in un lab in Azure DevTest Labs
Se è già stata [creata la prima VM](devtest-lab-create-first-vm.md), l'operazione è stata eseguita con un'[immagine di marketplace](devtest-lab-configure-marketplace-images.md) precaricata. Se ora si vuole aggiungere le macchine virtuali successive al lab, è anche possibile scegliere una *base* che sia un'[immagine personalizzata](devtest-lab-create-template.md) o una [formula](devtest-lab-manage-formulas.md). Questa esercitazione illustra l'uso del portale di Azure per aggiungere una VM in un lab in DevTest Labs.

Questo articolo descrive anche come gestire gli elementi per una VM nel lab.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Passaggi per aggiungere una VM a un lab in Azure DevTest Labs
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova VM.  
1. Nel riquadro **Panoramica** del lab selezionare **+ Aggiungi**.  

    ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Nel riquadro **Scegli una base** selezionare una base per la macchina virtuale.
1. Nel riquadro **Macchina virtuale** immettere un nome per la nuova macchina virtuale nella casella di testo **Nome macchina virtuale**.

    ![Riquadro lab della macchina virtuale](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Immettere un **Nome utente** a cui vengono concessi privilegi di amministratore nella macchina virtuale.  
1. Se si vuole usare una password archiviata nell'[archivio segreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selezionare **Use a saved secret** (Usa un segreto salvato) e specificare un valore chiave corrispondente al segreto (password). In alternativa, immettere una password nel campo di testo **Digita un valore**.
1. Il **tipo di disco della macchina virtuale** determina il tipo di disco di archiviazione consentito per le macchine virtuali nel lab.
1. Selezionare **Dimensioni macchina virtuale** e uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
1. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi da aggiungere all'immagine di base.
    **Nota:** se non si ha familiarità con DevTest Labs o con la configurazione di elementi, vedere la sezione [Aggiungere un elemento esistente in una macchina virtuale](#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.
1. Selezionare **Impostazioni avanzate** per configurare le opzioni di rete e le opzioni relative alla scadenza della VM. 

   Per impostare un'opzione di scadenza, scegliere l'icona del calendario per specificare una data in cui la macchina virtuale verrà eliminata automaticamente.  Per impostazione predefinita, la macchina virtuale non scadrà. 
1. Se si vuole visualizzare o copiare il modello di Azure Resource Manager, vedere la sezione [Salvare il modello di Azure Resource Manager](#save-azure-resource-manager-template) e tornare qui al termine dell'operazione.
1. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.
1. Nel riquadro del lab viene visualizzato lo stato di creazione della macchina virtuale, prima come **Creazione** e poi come **Esecuzione** dopo l'avvio della macchina virtuale.

> [!NOTE]
> L'argomento [Add a claimable VM](devtest-lab-add-claimable-vm.md) (Aggiungere una macchina virtuale a disposizione degli utenti) illustra come rendere disponibile una macchina virtuale per l'uso da parte di qualsiasi utente nel laboratorio.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Aggiungere un elemento esistente in una macchina virtuale
Durante la creazione di una macchina virtuale, è possibile aggiungere elementi esistenti. Ogni lab include elementi del repository pubblico degli elementi di DevTest Labs ed elementi creati e aggiunti al repository degli elementi personale.

* Gli *elementi* di Azure DevTest Labs consentono di specificare le *azioni* eseguite quando viene eseguito il provisioning della macchina virtuale, ad esempio, l'esecuzione di script Windows PowerShell, comandi Bash e installazione del software.
* Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario

Per istruzioni sulla creazione di elementi, vedere l'articolo contenente informazioni su [Creare elementi personalizzati per le macchine virtuali di DevTest Labs](devtest-lab-artifact-author.md).

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco di lab selezionare il lab contenente la VM che si vuole usare.  
1. Selezionare **Macchine virtuali**.
1. Selezionare la VM desiderata.
1. Selezionare **Gestisci gli artefatti**. 
1. Selezionare **Applica artefatti**.
1. In **Applica artefatti** selezionare l'artefatto da aggiungere alla macchina virtuale.
1. Nel riquadro **Aggiungi artefatto** immettere i valori dei parametri obbligatori e gli eventuali parametri facoltativi necessari.  
1. Selezionare **Aggiungi** per aggiungere l'artefatto e tornare al riquadro **Applica artefatti**.
1. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.
1. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare indietro per [visualizzare o modificare un elemento](#view-or-modify-an-artifact).
1. Una volta aggiunti tutti gli elementi, selezionare **Applica**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Modificare l'ordine di esecuzione degli elementi
Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del riquadro **Applica artefatti** selezionare il collegamento che indica il numero di artefatti aggiunti alla macchina virtuale.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Nel riquadro **Artefatti selezionati** trascinare e rilasciare gli artefatti nell'ordine desiderato. **Nota:** se si verificano problemi di trascinamento dell'elemento, assicurarsi di trascinare dal lato sinistro dell'elemento. 
1. Selezionare **OK** al termine dell'operazione.  

## <a name="view-or-modify-an-artifact"></a>visualizzare o modificare un elemento
I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del riquadro **Applica artefatti** selezionare il collegamento che indica il numero di artefatti aggiunti alla macchina virtuale.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Nel riquadro **Artefatti selezionati** selezionare l'artefatto che si vuole visualizzare o modificare.  
1. Nel riquadro **Aggiungi artefatto** apportare le modifiche necessarie e selezionare **OK** per chiudere il riquadro **Aggiungi artefatto**.
1. Selezionare **OK** per chiudere il riquadro **Artefatti selezionati**.

## <a name="save-azure-resource-manager-template"></a>Save Azure Resource Manager template
Un modello di Azure Resource Manager permette di definire una distribuzione ripetibile in modo dichiarativo. I passaggi seguenti illustrano come salvare il modello di Azure Resource Manager per la VM da creare.
Dopo il salvataggio è possibile usare il modello di Azure Resource Manager per [distribuire nuove macchine virtuali con Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Nel pannello **Macchina virtuale** selezionare **Visualizza il modello ARM**.
2. Nel riquadro **Visualizza il modello di Azure Resource Manager** selezionare il testo del modello.
3. Copiare il testo selezionato negli Appunti.
4. Selezionare **OK** per chiudere il riquadro **Visualizza il modello di Azure Resource Manager**.
5. Aprire un editor di testo.
6. Incollare il testo del modello dagli Appunti.
7. Salvare il file per usarlo in seguito.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Dopo avere creato la macchina virtuale, è possibile connettersi ad essa selezionando **Connetti** nel riquadro della macchina virtuale.
* Informazioni su come [creare elementi personalizzati per la VM di DevTest Labs](devtest-lab-artifact-author.md).
* Esplorare la [raccolta dei modelli di avvio rapido di Azure Resource Manager di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
