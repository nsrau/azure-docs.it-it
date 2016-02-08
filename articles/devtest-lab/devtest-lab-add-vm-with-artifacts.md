    <properties
	pageTitle="Add a VM with artifacts to a DevTest Lab | Microsoft Azure"
	description="Create a new virtual machine with Artifacts in DevTest Lab."
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Aggiungere una VM con elementi a un lab di sviluppo e test di Azure

## Panoramica

Creare una macchina virtuale in un Lab di sviluppo e test partendo da un'immagine di base di Azure o un'immagine caricata nel lab.

Gli*elementi* di Lab di sviluppo e test consentono di specificare le azioni eseguite quando viene creata la macchina virtuale. Le azioni degli elementi possono eseguire procedure quali l'esecuzione di comandi Powershell e Bash e l'installazione di software. Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario.

Il lab include gli elementi dell’archivio elementi di Lab di sviluppo e test, nonché gli elementi che vengono creati e aggiunti al proprio Archivio elementi.

In questo articolo viene illustrato come creare una macchina virtuale nel lab utilizzando gli elementi.

## Aggiungere una macchina virtuale con gli elementi

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Dall'elenco dei lab, toccare il lab in cui si desidera creare la nuova macchina virtuale.

1. Nel pannello del lab, toccare **+ macchine virtuali di Lab** come illustrato nella figura seguente. ![Pannello iniziale di lab di sviluppo e test](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Nel pannello **macchina virtuale di Lab**, immettere un nome per la nuova macchina virtuale nella casella di testo **nome macchina virtuale di Lab**.

1. Toccare **Base / Configura le impostazioni necessarie** e selezionare un'immagine di base per la macchina virtuale.

    ![Impostazioni della macchina virtuale di Lab](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Dopo aver selezionato un'immagine di base, il pannello **macchina virtuale di Lab** si espande per includere gli elementi **Nome utente** e **Password**. Immettere un **Nome utente** a cui verranno concessi privilegi di amministratore nella macchina virtuale.

    ![Pannello espanso della macchina virtuale di Lab](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Immettere una **Password**.

1. Toccare **Dimensioni della macchina virtuale** e selezionare uno degli elementi predefiniti che specificano le memorie centrali del processore, le dimensioni RAM e le dimensioni dell'unità disco rigido della macchina virtuale da creare.

1. Toccare **Elementi** e dall'elenco di elementi, selezionare e configurare gli elementi che si desidera aggiungere all'immagine di base. **Nota:** se si ha familiarità con i Lab di sviluppo e test o con la configurazione degli elementi, andare direttamente alla sezione [Selezione e configurazione di un elemento](#configuring-an-artifact) e tornare qui al termine.

1. Toccare **Crea** per aggiungere la macchina virtuale specificata al lab.

1. Il pannello lab consente di visualizzare lo stato di creazione della macchina virtuale prima come **fase di creazione**, poi come **fase di esecuzione** dopo aver avviato la macchina virtuale. Per connettersi alla macchina virtuale, toccare la macchina virtuale e, nel pannello della macchina virtuale toccare **Connetti**.

## Selezionare e configurare un elemento

Durante la creazione di una macchina virtuale, è possibile aggiungere elementi toccando **Elementi** nel pannello **Macchina virtuale di Lab**. Verrà visualizzato il pannello **Aggiungi elementi** che consente di aggiungere e configurare gli elementi della macchina virtuale dall’archivio ufficiale di Lab di sviluppo e test (**archivio ufficiale**) e gli elementi dall’archivio del team.

![Pannello Aggiungi elementi](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**Aggiunta di un elemento a una macchina virtuale**

Seguire questi passaggi per ogni elemento da aggiungere alla macchina virtuale:

1. Toccare l'elemento desiderato nel pannello **Aggiungi elementi** per visualizzare un pannello che consente di specificare i parametri dell'elemento.  

2. Immettere i valori dei parametri obbligatori e i parametri facoltativi necessari.

3. Toccare **Aggiungi** per aggiungere l'elemento e tornare al pannello **Aggiungi elementi**.

**Modifica dell'ordine di esecuzione degli elementi**

Quando si aggiunge e configura elementi per la macchina virtuale, viene visualizzato un collegamento che mostra il numero corrente di elementi nella parte superiore del pannello **Aggiungi elementi**. Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. Per modificare l'ordine in cui vengono eseguiti gli elementi, semplicemente trascinare e rilasciare gli elementi nell'elenco e toccare **OK** al termine.

**Visualizzazione/Modifica degli elementi selezionati**

Seguire questi passaggi per visualizzare o modificare i parametri degli elementi selezionati:

1. Nella parte superiore del pannello **Aggiungi elementi** toccare il collegamento che indica il numero di elementi aggiunti alla macchina virtuale.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Per visualizzare o modificare i parametri di un elemento specifico, toccare l’elemento nel pannello **Elementi selezionati**.

1. Apportare eventuali modifiche necessarie e toccare **OK** per chiudere il pannello **Aggiungi elemento**.

1. Toccare **OK** per chiudere il pannello **Elementi selezionati**.

1. Toccare **OK** per chiudere il pannello **Aggiungi elemento**.

## Passaggi successivi

Informazioni su come [creare elementi personalizzati per la macchina virtuale](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0128_2016-->