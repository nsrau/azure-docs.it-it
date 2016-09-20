<properties
	pageTitle="Aggiungere una VM con elementi a un lab in Azure DevTest Labs | Microsoft Azure"
	description="Informazioni su come aggiungere una VM con elementi in Azure DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="tarcher"/>

# Aggiungere una VM con elementi a un lab in Azure DevTest Labs

> [AZURE.VIDEO how-to-create-vms-with-artifacts-in-a-devtest-lab]

È possibile creare una macchina virtuale lab a partire da un elemento di *base* costituito da un'[immagine personalizzata](./devtest-lab-create-template.md), da una [formula](./devtest-lab-manage-formulas.md) o da un'[immagine di Marketplace](./devtest-lab-configure-marketplace-images.md).

Gli *elementi* di DevTest Labs consentono di specificare le *azioni* eseguite quando viene creata la macchina virtuale.

Le azioni degli elementi possono eseguire procedure, ad esempio script di Windows PowerShell e comandi Bash, e installare software.

Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario.

Questo articolo descrive come creare una macchina virtuale nel lab usando gli elementi.

## Aggiungere una macchina virtuale con elementi

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova VM.

1. Nel pannello **Panoramica** del lab selezionare **+ Macchina virtuale**. ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Nel pannello **Scegli una base** selezionare una base per la macchina virtuale.

1. Nel pannello **Macchina virtuale** immettere un nome per la nuova macchina virtuale nella casella di testo **Nome macchina virtuale**.

	![Pannello Lab VM (VM lab)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. Immettere un **Nome utente** a cui verranno concessi privilegi di amministratore nella macchina virtuale.

1. Se si desidera usare una password archiviata nell'*archivio segreto*, selezionare **Usa segreti dall'archivio segreto** e specificare un valore chiave corrispondente al segreto (password). In alternativa, è sufficiente immettere una password nel campo di testo etichettato **Digita un valore**.
 
1. Selezionare **Dimensioni macchina virtuale** e uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.

1. Selezionare **Rete virtuale**, quindi la rete virtuale desiderata.

1. Selezionare **Subnet**, quindi la subnet.

1. Se i criteri del lab sono impostati in modo da consentire gli indirizzi IP pubblici per la subnet selezionata, specificare se si vuole che l'indirizzo IP sia pubblico selezionando **Sì** o **No**. In caso contrario, questa opzione è disabilitata e impostata su **No**.

1. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi da aggiungere all'immagine di base. **Nota:** se non si ha familiarità con DevTest Labs o con la configurazione di elementi, passare alla sezione [Aggiungere un elemento esistente in una macchina virtuale](#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.

1. Se si vuole visualizzare o copiare il modello di Azure Resource Manager, passare alla sezione [Save Azure Resource Manager template](#save-arm-template) (Salva modello di Azure Resource Manager) e tornare qui al termine dell'operazione.

1. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.

1. Il pannello lab consente di visualizzare lo stato di creazione della macchina virtuale prima come **fase di creazione**, poi come **fase di esecuzione** dopo aver avviato la macchina virtuale.

1. Passare alla sezione [Passaggi successivi](#next-steps).

## Aggiungere un elemento esistente in una macchina virtuale

Durante la creazione di una macchina virtuale, è possibile aggiungere elementi esistenti. Ogni lab include elementi del repository pubblico degli elementi di DevTest Labs ed elementi creati e aggiunti al repository degli elementi personale. Per istruzioni sulla creazione di elementi, vedere l'articolo contenente informazioni su [Creare elementi personalizzati per le macchine virtuali di DevTest Labs](devtest-lab-artifact-author.md).

1. Nel pannello **Macchina virtuale** selezionare **Elementi**.

1. Nel pannello **Aggiungi elementi** selezionare l'elemento desiderato.

	![Pannello Add Artifacts (Aggiungi elementi)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Immettere i valori dei parametri obbligatori e i parametri facoltativi necessari.

1. Selezionare **Aggiungi** per aggiungere l'elemento e tornare al pannello **Aggiungi elementi**.

1. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.

1. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare indietro per [visualizzare o modificare un elemento](#view-or-modify-an-artifact).

## Modificare l'ordine di esecuzione degli elementi

Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del pannello **Aggiungi elementi** selezionare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Per specificare l'ordine in cui eseguire gli elementi, trascinare e rilasciare gli elementi nell'ordine desiderato. **Nota:** se si verificano problemi di trascinamento dell'elemento, assicurarsi di trascinare dal lato sinistro dell'elemento.

1. Selezionare **OK** al termine dell'operazione.

## Visualizzare o modificare un elemento

I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del pannello **Aggiungi elementi** selezionare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Nel pannello **Selected Artifacts** (Elementi selezionati) selezionare l'elemento che si vuole visualizzare o modificare.

1. Nel pannello **Aggiungi elemento** apportare le modifiche necessarie e selezionare **OK** per chiudere il pannello**Aggiungi elemento**.

1. Selezionare **OK** per chiudere il pannello **Selected Artifacts** (Elementi selezionati).

## Salvare il modello di Azure Resource Manager

Un modello di Azure Resource Manager permette di definire una distribuzione ripetibile in modo dichiarativo. I passaggi seguenti illustrano come salvare il modello di Azure Resource Manager per la VM da creare. Dopo il salvataggio è possibile usare il modello di Azure Resource Manager per [distribuire nuove VM con Azure PowerShell](../resource-group-overview.md#template-deployment).

1. Nel pannello **Macchina virtuale** selezionare **Visualizza modello ARM**.

1. Nel pannello **Visualizza modello Azure Resource Manager** selezionare il testo del modello.

1. Copiare il testo selezionato negli Appunti.

1. Selezionare **OK** per chiudere il pannello **View Azure Resource Manager Template** (Visualizza modello di Azure Resource Manager).

1. Aprire un editor di testo.

1. Incollare il testo del modello dagli Appunti.

1. Salvare il file per usarlo in seguito.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Passaggi successivi

- Dopo avere creato la VM, è possibile connettersi ad essa selezionando **Connetti** nel pannello della VM.
- Informazioni su come [creare elementi personalizzati per la VM di DevTest Labs](devtest-lab-artifact-author.md).
- Esplorare la [raccolta dei modelli ARM di avvio rapido di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

<!---HONumber=AcomDC_0907_2016-->