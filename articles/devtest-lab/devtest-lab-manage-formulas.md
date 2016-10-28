<properties
	pageTitle="Gestire le formule in Azure DevTest Labs per creare VM | Microsoft Azure"
	description="È possibile creare, aggiornare e rimuovere le formule in Azure DevTest Labs e usarle per creare nuove VM."
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

# Gestire le formule dei lab di sviluppo/test per creare macchine virtuali

Una formula è un elenco di valori predefiniti di proprietà usati in Azure DevTest Labs per creare una macchina virtuale (VM). Quando si crea una macchina virtuale da una formula, i valori predefiniti possono essere modificati o usati così come sono. Le formule, ad esempio le [immagini personalizzate](./devtest-lab-create-template.md) e le [immagini Marketplace](./devtest-lab-configure-marketplace-images.md), offrono un meccanismo in grado di velocizzare il provisioning delle VM.

Questo articolo spiega come eseguire le attività seguenti:

- [Creare una formula](#create-a-formula)
- [Usare una formula per il provisioning di una VM](#use-a-formula-to-provision-a-vm)
- [Modificare una formula](#modify-a-formula)
- [Eliminare una formula](#delete-a-formula)

> [AZURE.NOTE] Ad esempio, le formule, come [custom images](./devtest-lab-create-template.md), consentono di creare un'immagine di base da un file VHD. L'immagine di base può quindi essere usata per il provisioning di una nuova VM. Per decidere quale formula è più corretta per un ambiente specifico, vedere l'articolo relativo al [confronto tra immagini personalizzate e formule nei lab di sviluppo/test](./devtest-lab-comparing-vm-base-image-types.md).

## Creare una formula
Tutti gli utenti con autorizzazione *Utenti* per i lab di sviluppo/test possono creare macchine virtuali usando una formula come base. È possibile creare le formule in due modi:

- Da una base: quando si vogliono definire tutte le caratteristiche della formula.
- Da una macchina virtuale di lab già esistente: quando si vuole creare una formula basata sulle impostazioni di una macchina virtuale esistente.

### Creare una formula di base
Nella procedura seguente sono descritti i passaggi per creare una formula da un'immagine personalizzata, un'immagine Marketplace o un'altra formula.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello del lab selezionare **Formule (basi riutilizzabili)**.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Nel pannello **Formule lab** selezionare **+ Aggiungi**.

    ![Aggiungere una formula](./media/devtest-lab-manage-formulas/add-formula.png)

1. Nel pannello **Scegli una base** selezionare la base (immagine personalizzata, immagine Marketplace o formula) da cui si desidera creare la formula.

    ![Elenco base](./media/devtest-lab-manage-formulas/base-list.png)

1. Specificare i valori seguenti nel pannello **Crea formula**:

	- **Nome formula**: immettere un nome per la formula. Questo valore verrà visualizzato nell'elenco delle immagini di base quando si crea una macchina virtuale. Il nome viene convalidato durante la digitazione e, se non è valido, un messaggio indicherà i requisiti per un nome valido.
	- **Descrizione**: immettere una descrizione significativa per la formula. Questo valore è disponibile dal menu di scelta rapida della formula quando si crea una macchina virtuale.
	- **Nome utente**: immettere un nome utente a cui verranno concessi privilegi di amministratore.
	- **Password**: immettere o scegliere dall'elenco a discesa un valore associato al segreto (password) che si desidera usare per l'utente specificato.
	- **Immagine**: questo campo visualizza il nome dell'immagine di base selezionata nel pannello precedente.
	- **Dimensioni macchina virtuale**: selezionare uno degli elementi predefiniti che specificano i core del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
	- **Rete virtuale**: specificare la rete virtuale desiderata.
	- **Subnet**: specificare la subnet desiderata.
	- **Indirizzo IP pubblico**: se i criteri del lab sono impostati per consentire gli indirizzi IP pubblici per la subnet selezionata, scegliere **Sì** o **No** per indicare se si desidera o meno rendere pubblico l'indirizzo IP. In caso contrario, questa opzione è disabilitata e impostata su **No**.
	- **Elementi**: selezionare e configurare gli elementi che verranno aggiunti all'immagine di base. I valori delle stringhe sicure non vengono salvati con la formula. Pertanto, i parametri degli elementi corrispondenti a stringhe sicure non vengono visualizzati.

    	![Creare una formula](./media/devtest-lab-manage-formulas/create-formula.png)

1. Selezionare **Crea** per creare la formula.

### Creare una formula da una VM
La procedura seguente consente di creare una formula basata su una macchina virtuale già esistente.

> [AZURE.NOTE] Per creare una formula da una VM, la VM deve essere stata creata dopo il 30 marzo 2016.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello **Panoramica** del lab selezionare la VM dalla quale creare la formula.

	![Macchine virtuali di lab](./media/devtest-lab-manage-formulas/my-vms.png)

1. Nel pannello della VM selezionare **Crea formula (base riutilizzabile)**.

	![Creare una formula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. Nel pannello **Crea formula** immettere un **Nome** e una **Descrizione** della nuova formula.

	![Pannello Crea formula](./media/devtest-lab-manage-formulas/create-formula-blade.png)

1. Fare clic su **OK** per creare la formula.

## Usare una formula per il provisioning di una VM
Dopo aver creato una formula, è possibile creare una VM in base a tale formula. La procedura è descritta nella sezione [Aggiungere una macchina virtuale con elementi](devtest-lab-add-vm-with-artifacts.md#add-a-vm-with-artifacts).

## Modificare una formula
Per modificare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello del lab selezionare **Formule (basi riutilizzabili)**.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Nel pannello **Formule lab** selezionare la formula che si vuole modificare.

1. Apportare le modifiche nel pannello **Aggiorna formula** e selezionare **Aggiorna**.

## Eliminare una formula 
Per eliminare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello **Impostazioni** del lab selezionare **Formule**.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Nel pannello **Formule lab** selezionare i puntini di sospensione a destra della formula da eliminare.

    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. Selezionare **Elimina** dal menu di scelta rapida della formula.

    ![Menu di scelta rapida Formula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Selezionare **Sì** nella finestra di dialogo di conferma dell'eliminazione.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## Passaggi successivi
Dopo avere creato una formula da usare durante la creazione di una VM, il passaggio successivo consiste nell'[aggiungere una VM al lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->