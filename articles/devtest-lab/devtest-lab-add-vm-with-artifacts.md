<properties
	pageTitle="Aggiungere una VM con elementi a un Lab di sviluppo/test | Microsoft Azure"
	description="Informazioni su come aggiungere una macchina virtuale con elementi in un lab di sviluppo/test"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Aggiungere una macchina virtuale con elementi in un lab di sviluppo/test

> [AZURE.NOTE] Fare clic sul collegamento seguente per visualizzare il video associato a questo articolo, relativo alla [procedura di creazione di macchine virtuali con elementi in un lab di sviluppo/test](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## Panoramica

Creare una macchina virtuale in un lab di sviluppo/test a partire da un'immagine di base costituita da un'[immagine personalizzata](./devtest-lab-create-template.md) o un'immagine di Marketplace.

Gli*elementi* del lab di sviluppo/test consentono di specificare le *azioni* eseguite quando viene creata la macchina virtuale.

Le azioni degli elementi possono eseguire procedure quali l'esecuzione di script Powershell e comandi Bash e l'installazione di software.

Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario.

In questo articolo viene illustrato come creare una macchina virtuale nel lab usando gli elementi.

## Aggiungere una macchina virtuale con gli elementi

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Dall'elenco dei lab, toccare il lab in cui si desidera creare la nuova macchina virtuale.

1. Nel pannello del lab, toccare **+ macchine virtuali di Lab** come illustrato nella figura seguente. ![Pannello iniziale di lab di sviluppo e test](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Nel pannello **macchina virtuale di Lab**, immettere un nome per la nuova macchina virtuale nella casella di testo **nome macchina virtuale di Lab**.

1. Toccare **Base / Configura le impostazioni necessarie** e selezionare un'immagine di base per la macchina virtuale.

    ![Impostazioni della macchina virtuale di Lab](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Dopo aver selezionato un'immagine di base e toccato **OK**, il pannello della **macchina virtuale del lab** si espande per includere gli elementi dell'interfaccia utente per la specifica delle informazioni dell'account utente, inclusi **nome utente**, **tipo di autenticazione** (se il sistema operativo per la base selezionata è Linux) e **password** (presupponendo un'autenticazione di tipo *password*).

    ![Pannello espanso della macchina virtuale di Lab](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Immettere un **Nome utente** a cui verranno concessi privilegi di amministratore nella macchina virtuale.

1. Se il sistema operativo della base selezionata è Linux, specificare il tipo di autenticazione *password* o *chiave pubblica SSH*.

1. A seconda del tipo di autenticazione specificato, immettere una password o una chiave pubblica SSH.

1. Toccare **Dimensioni della macchina virtuale** e selezionare uno degli elementi predefiniti che specificano le memorie centrali del processore, le dimensioni RAM e le dimensioni dell'unità disco rigido della macchina virtuale da creare.

1. Toccare **Rete virtuale** e selezionare la rete virtuale desiderata.

1. Toccare **Subnet** e selezionare la subnet.

1. Se i criteri del lab sono impostati per consentire gli indirizzi IP pubblici per la subnet selezionata, specificare se si desidera che l'indirizzo IP sia pubblico selezionando **Sì** o **No**. In caso contrario, questa opzione è disabilitata e impostata su **No**.

1. Toccare **Elementi** e dall'elenco di elementi, selezionare e configurare gli elementi che si desidera aggiungere all'immagine di base. **Nota:** se non si ha familiarità con i lab di sviluppo/test o la configurazione degli elementi, passare alla sezione [Aggiungere un elemento esistente in una macchina virtuale](#add-an-existing-artifact-to-a-vm) e tornare qui al termine.

1. Toccare **Crea** per aggiungere la macchina virtuale specificata al lab.

1. Il pannello lab consente di visualizzare lo stato di creazione della macchina virtuale prima come **fase di creazione**, poi come **fase di esecuzione** dopo aver avviato la macchina virtuale.

1. Passare alla sezione [Passaggi successivi](#next-steps).

## Aggiungere un elemento esistente in una macchina virtuale

Durante la creazione di una macchina virtuale, è possibile aggiungere elementi esistenti. Ogni lab include elementi del repository degli elementi di lab di sviluppo/test pubblici ed elementi creati e aggiunti al proprio repository degli elementi. Per scoprire come creare gli elementi, vedere l'articolo contenente informazioni su [come creare elementi personalizzati da usare con i lab di sviluppo/test](devtest-lab-artifact-author.md).

1. Nel pannello della **macchina virtuale del lab** toccare **Elementi**. 

1. Nel pannello **Aggiungi elementi** toccare l'elemento desiderato.

![Pannello Aggiungi elementi](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Immettere i valori dei parametri obbligatori e i parametri facoltativi necessari.  

1. Toccare **Aggiungi** per aggiungere l'elemento e tornare al pannello **Aggiungi elementi**.

1. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.

1. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti gli elementi](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare a [visualizzare o modificare un elemento](#view-or-modify-an-artifact).

## Modifica dell'ordine di esecuzione degli elementi

Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del pannello **Aggiungi elementi** toccare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Trascinare e rilasciare gli elementi nell'elenco in base all'ordine desiderato. **Nota:** se si verificano problemi di trascinamento dell'elemento, assicurarsi di trascinare dal lato sinistro dell'elemento.

1. Toccare **OK** al termine.

## Visualizzare o modificare un elemento

I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del pannello **Aggiungi elementi** toccare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Nel pannello **Elementi selezionati** toccare l'elemento che si desidera visualizzare o modificare.

1. Nel pannello **Aggiungi elemento** apportare le modifiche necessarie e toccare **OK** per chiudere il pannello **Aggiungi elemento**.

1. Toccare **OK** per chiudere il pannello **Elementi selezionati**.

## Passaggi successivi

- Dopo aver creato la macchina virtuale, è possibile connettersi alla macchina virtuale toccando **Connetti** nel pannello della macchina virtuale.
- Per scoprire come creare gli elementi, vedere l'articolo contenente informazioni su [come creare elementi personalizzati da usare con i lab di sviluppo/test](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0316_2016-->