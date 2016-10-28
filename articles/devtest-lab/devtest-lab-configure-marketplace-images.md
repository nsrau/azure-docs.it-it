<properties
	pageTitle="Configurare le impostazioni dell'immagine di Azure Marketplace in Azure DevTest Labs | Microsoft Azure"
	description="Specificare le immagini di Azure Marketplace che possono essere usate per la creazione di una VM in Azure DevTest Labs"
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
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Configurare le impostazioni dell'immagine di Azure Marketplace in Azure DevTest Labs

Lab di sviluppo/test supporta la creazione di VM basate su immagini di Azure Marketplace in base alla configurazione delle immagini di Azure Marketplace da usare nel lab. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.

## Selezionare le immagini di Azure Marketplace consentite per la creazione di una macchina virtuale

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello del lab selezionare **Configurazione**.
	
1. Nel pannello **Configurazione** del lab selezionare **Immagini di Azure Marketplace**.

1. Specificare se si desidera che tutte le immagini di Azure Marketplace qualificate siano disponibili per essere usate come base di una nuova macchina virtuale. Se si seleziona **Sì**, tutte le immagini di Azure Marketplace che soddisfano tutti i criteri seguenti saranno disponibili nel lab:

	- L'immagine crea una singola macchina virtuale **e**
	- L'immagine usa Azure Resource Manager per il provisioning delle macchine virtuali **e**
	- L'immagine non richiede l'acquisto di un piano di licenze aggiuntivo
	
	Se non si desidera autorizzare alcuna immagine o si desidera specificare le immagini che possono essere usate, selezionare **No**.
 
	![Opzione per consentire l'uso di tutte le immagini di Marketplace come immagini di base per le macchine virtuali](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Se si seleziona **No** nel passaggio precedente, la casella di controllo **Immagini consentite/Seleziona tutto** viene abilitata. È possibile usare questa opzione con la casella di ricerca per selezionare o deselezionare rapidamente tutti gli elementi visualizzati nell'elenco. È inoltre possibile selezionare le singole immagini di Azure Marketplace che si desidera rendere disponibili per la creazione di macchine virtuali selezionando la casella di controllo corrispondente all'immagine. Non selezionare alcuna voce nell'elenco se non si desidera rendere disponibile alcuna immagine di Azure Marketplace per l'uso nel lab.

	![È possibile specificare quali immagini di Azure Marketplace possono essere usate come immagini di base per le macchine virtuali](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Passaggi successivi

Dopo avere configurato le immagini di Azure Marketplace consentite per la creazione di una VM, il passaggio successivo consiste nell'[aggiungere una VM nel lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->