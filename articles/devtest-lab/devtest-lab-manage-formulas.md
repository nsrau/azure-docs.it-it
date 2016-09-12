<properties
	pageTitle="Gestire le formule dei lab di sviluppo/test per creare macchine virtuali | Microsoft Azure"
	description="È possibile creare, aggiornare e rimuovere le formule dei lab di sviluppo/test e usarle per creare nuove macchine virtuali."
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
	ms.date="08/25/2016"
	ms.author="tarcher"/>  

# Gestire le formule dei lab di sviluppo/test per creare macchine virtuali

## Panoramica

Le formule, ad esempio le [immagini personalizzate](./devtest-lab-create-template.md) e le [immagini Marketplace](./devtest-lab-configure-marketplace-images.md), offrono un meccanismo in grado di velocizzare il provisioning delle macchine virtuali. Una formula è un elenco di valori predefiniti di proprietà usati nei lab di sviluppo/test per creare macchine virtuali. Quando si crea una macchina virtuale da una formula, i valori predefiniti possono essere modificati o usati così come sono.

Questo articolo spiega come eseguire le attività seguenti:

- [Creare una nuova formula](#create-a-new-formula)
- [Usare una formula per creare una nuova macchina virtuale](#use-a-formula-to-create-a-new-vm)
- [Modificare una formula](#modify-a-formula)
- [Eliminare una formula](#delete-a-formula)

> [AZURE.NOTE] Le formule sono simili alle [immagini personalizzate](./devtest-lab-create-template.md) poiché ognuna consente di creare un'immagine di base da un disco rigido virtuale usato per eseguire il provisioning di una macchina virtuale. Per decidere quale formula è più corretta per un ambiente specifico, vedere l'articolo relativo al [confronto tra immagini personalizzate e formule nei lab di sviluppo/test](./devtest-lab-comparing-vm-base-image-types.md).

## Creare una nuova formula
Tutti gli utenti con autorizzazione *Utenti* per i lab di sviluppo/test possono creare macchine virtuali in un lab usando una formula come base. È possibile creare le formule in due modi:

- Da zero: quando si vogliono definire tutte le caratteristiche della formula da zero.
- Da una macchina virtuale di lab già esistente: quando si vuole creare una formula basata sulle impostazioni di una macchina virtuale esistente.

### Creare una nuova formula da zero
La procedura seguente consente di creare una nuova formula da zero.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Viene visualizzato il pannello **Impostazioni** del lab selezionato.

1. Nel pannello **Impostazioni** del lab selezionare **Formule**.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Nel pannello **Formule lab** selezionare **+ Aggiungi**.

    ![Aggiungere una nuova formula](./media/devtest-lab-manage-formulas/add-formula.png)

1. Nel pannello **Scegli una base** selezionare l'immagine personalizzata o l'immagine Marketplace da cui si desidera creare la formula.

    ![Elenco base](./media/devtest-lab-manage-formulas/base-list.png)

1. Specificare i valori seguenti nel pannello **Crea formula**:

	- **Nome formula**: immettere un nome per la formula. Questo valore verrà visualizzato nell'elenco delle immagini di base quando si crea una macchina virtuale. Il nome viene convalidato durante la digitazione e, se non è valido, un messaggio indicherà i requisiti per un nome valido.
	- **Descrizione**: immettere una descrizione significativa per la formula. Questo valore è disponibile dal menu di scelta rapida della formula quando si crea una macchina virtuale.
	- **Immagine**: questo campo visualizza il nome dell'immagine di base selezionata nel pannello precedente.
	- **Dimensioni macchina virtuale**: selezionare uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della macchina virtuale da creare.
	- **Rete virtuale**: selezionare questa opzione, quindi la rete virtuale desiderata.
	- **Subnet**: selezionare questa opzione, quindi la subnet desiderata.
	- **Indirizzo IP pubblico**: se i criteri del lab sono impostati per consentire gli indirizzi IP pubblici per la subnet selezionata, specificare se l'indirizzo IP deve essere pubblico selezionando **Sì** o **No**. In caso contrario, questa opzione è disabilitata e impostata su **No**.
	- **Elementi**: selezionare questa opzione, quindi, dall’elenco di elementi, selezionare e configurare gli elementi che si vuole aggiungere all’immagine di base. Si noti che i parametri degli elementi che sono stringhe protette non vengono visualizzati, poiché la formula non salva i valori delle stringhe protette.

    	![Creare una formula](./media/devtest-lab-manage-formulas/create-formula.png)

1. Selezionare **Crea** per creare la formula. Dopo che è stata creata, la formula è elencata nel pannello **Lab formulas** (Formule del lab).

	![Formula appena creata](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### Creare una nuova formula da una macchina virtuale di lab
La procedura seguente consente di creare una formula basata su una macchina virtuale già esistente.

> [AZURE.NOTE] Solo le macchine virtuali create dopo il 30 marzo 2016 supportano la creazione di una nuova formula da una macchina virtuale di lab.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello del lab individuare la sezione **Macchine virtuali personali** e selezionare la macchina virtuale da cui si vuole creare la nuova formula.

	![Macchine virtuali di lab](./media/devtest-lab-manage-formulas/my-vms.png)

1. Nel pannello **Impostazioni** della macchina virtuale selezionare **Crea formula**.

	![Creare una formula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. Nel pannello **Crea formula** immettere un **nome** e una **descrizione** per la nuova formula e selezionare **OK**. Dopo che è stata creata, la formula è elencata nel pannello **Lab formulas** (Formule del lab).

	![Pannello Crea formula](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## Modificare una formula
Per modificare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello **Impostazioni** del lab selezionare **Formule**.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Nel pannello **Formule lab** selezionare la formula che si vuole modificare.

1. Apportare le modifiche nel pannello **Aggiorna formula** e selezionare **Aggiorna**.

## Eliminare una formula 
Per eliminare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello **Impostazioni** del lab selezionare **Formule**.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Nel pannello **Formule lab** selezionare i puntini di sospensione a destra della formula da eliminare.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. Selezionare **Elimina** dal menu di scelta rapida della formula.

    ![Menu di scelta rapida Formula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Selezionare **Sì** nella finestra di dialogo di conferma dell'eliminazione.

    ![Menu di scelta rapida Formula](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)  

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## Passaggi successivi
Dopo avere creato una formula da usare durante la creazione di una VM, il passaggio successivo consiste nell'[aggiungere una VM al lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->