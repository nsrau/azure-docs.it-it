<properties
	pageTitle="Aggiungere una macchina virtuale con elementi a un lab | Microsoft Azure"
	description="Informazioni su come aggiungere una macchina virtuale con elementi in DevTest Labs"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Aggiungere una macchina virtuale con elementi a un lab

> [AZURE.NOTE] Visualizzare il video associato all’articolo, relativo alla [procedura di creazione di macchine virtuali con elementi in un lab](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## Panoramica

È possibile creare una macchina virtuale (VM, Virtual Machine) in un lab a partire da un elemento di *base* costituito da un'[immagine personalizzata](./devtest-lab-create-template.md), da una [formula](./devtest-lab-manage-formulas.md) o da un'[immagine di Marketplace](./devtest-lab-configure-marketplace-images.md).

Gli *elementi* di DevTest Labs consentono di specificare le *azioni* eseguite quando viene creata la macchina virtuale.

Le azioni degli elementi possono eseguire procedure, ad esempio script di Windows PowerShell e comandi Bash, e installare software.

I *parametri* degli elementi consentono di personalizzare gli elementi per determinati scenari.

Questo articolo descrive come creare una macchina virtuale nel lab usando gli elementi.

## Aggiungere una macchina virtuale con elementi

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova macchina virtuale.

1. Nel pannello del lab selezionare **+ Lab VM** (+ VM lab) come illustrato nella figura seguente. ![Pulsante Add Lab VM (Aggiungi VM lab)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Nel pannello **Scegli una base** selezionare una base per la macchina virtuale.

1. Nel pannello **Lab VM** (VM lab), immettere un nome per la nuova macchina virtuale nella casella di testo **Lab VM Name** (Nome VM lab).

	![Pannello Lab VM (VM lab)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. Immettere un **Nome utente** a cui verranno concessi privilegi di amministratore nella macchina virtuale.

1. Se il sistema operativo della base selezionata è Linux, specificare il tipo di autenticazione *Password* o *Chiave pubblica SSH*.

1. A seconda del tipo di autenticazione specificato, immettere una password o una chiave pubblica SSH.

1. Selezionare **Dimensioni macchina virtuale**, quindi uno degli elementi predefiniti che specificano le memorie centrali del processore, le dimensioni della RAM e le dimensioni dell'unità disco rigido della macchina virtuale da creare.

1. Selezionare **Rete virtuale**, quindi la rete virtuale desiderata.

1. Selezionare **Subnet**, quindi la subnet.

1. Se i criteri del lab sono impostati in modo da consentire gli indirizzi IP pubblici per la subnet selezionata, specificare se si vuole che l'indirizzo IP sia pubblico selezionando **Sì** o **No**. In caso contrario, questa opzione è disabilitata e impostata su **No**.

1. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi che si vuole aggiungere all'immagine di base. **Nota:** se non si ha familiarità con DevTest Labs o con la configurazione di elementi, passare alla sezione [Aggiungere un elemento esistente in una macchina virtuale](#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.

1. Se si vuole visualizzare o copiare il modello ARM (Azure Resource Manager), passare alla sezione [Save ARM template](#save-arm-template) (Salva modello ARM) e tornare qui al termine dell'operazione.

1. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.

1. Il pannello del lab consente di visualizzare lo stato della macchina virtuale prima in **fase di creazione** e quindi in **fase di esecuzione**, dopo l'avvio della macchina virtuale.

1. Passare alla sezione [Passaggi successivi](#next-steps).

## Aggiungere un elemento esistente in una macchina virtuale

Durante la creazione di una macchina virtuale, è possibile aggiungere elementi esistenti. Ogni lab include elementi del repository pubblico degli elementi di DevTest Labs ed elementi creati e aggiunti al repository degli elementi personale. Per istruzioni sulla creazione di elementi, vedere l'articolo contenente informazioni su [come creare elementi personalizzati da usare con DevTest Labs](devtest-lab-artifact-author.md).

1. Nel pannello **VM lab** selezionare **Elementi**. 

1. Nel pannello **Aggiungi elementi** selezionare l'elemento desiderato.

![Pannello Add Artifacts (Aggiungi elementi)](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Immettere i valori dei parametri obbligatori e i parametri facoltativi necessari.  

1. Selezionare **Aggiungi** per aggiungere l'elemento e tornare al pannello **Aggiungi elementi**.

1. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.

1. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare indietro per [visualizzare o modificare un elemento](#view-or-modify-an-artifact).

## Modificare l'ordine di esecuzione degli elementi

Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del pannello **Aggiungi elementi** selezionare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Trascinare e rilasciare gli elementi nell'elenco in base all'ordine desiderato. **Nota:** se si verificano problemi di trascinamento dell'elemento, assicurarsi di trascinare dal lato sinistro dell'elemento.

1. Selezionare **OK** al termine dell’operazione.

## Visualizzare o modificare un elemento

I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del pannello **Aggiungi elementi** selezionare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Nel pannello **Elementi selezionati** selezionare l'elemento che si vuole visualizzare o modificare.

1. Nel pannello **Aggiungi elemento** apportare le modifiche necessarie e selezionare **OK** per chiudere il pannello.

1. Selezionare **OK** per chiudere il pannello **Elementi selezionati**.

## Salvare il modello ARM

Un modello ARM permette di definire una distribuzione ripetibile in modo dichiarativo. I passaggi seguenti illustrano come salvare il modello ARM per la macchina virtuale da creare. Dopo il salvataggio, è possibile utilizzare il modello ARM per [distribuire nuove macchine virtuali con Azure PowerShell](../resource-group-overview.md#template-deployment).

1. Nel pannello **VM lab** selezionare **Visualizza modello ARM**.

1. Nel pannello **View Azure Resource Manager Template** (Visualizza modello Azure Resource Manager) selezionare tutto il testo del modello.

1. Copiare il testo selezionato negli Appunti.

1. Selezionare **OK** per chiudere il **pannello Visualizza modello Azure Resource Manager**.

1. Aprire un editor di testo.

1. Incollare il testo del modello dagli Appunti.

1. Salvare il file per usarlo in seguito.

## Passaggi successivi

- Dopo aver creato la macchina virtuale, è possibile connettersi alla macchina virtuale selezionando **Connetti** nel pannello della macchina virtuale.
- Leggere le informazioni su come [creare elementi personalizzati per la macchina virtuale di DevTest Labs](devtest-lab-artifact-author.md).
- Esplorare la [raccolta dei modelli ARM di avvio rapido di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

<!---HONumber=AcomDC_0608_2016-->