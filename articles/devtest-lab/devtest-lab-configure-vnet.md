<properties
	pageTitle="Configurare una rete virtuale per Azure DevTest Labs | Microsoft Azure"
	description="Informazioni su come configurare una rete virtuale esistente e una subnet e utilizzarle in una VM con Azure DevTest Labs"
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

# Configurare una rete virtuale per Azure DevTest Labs

Come illustrato nell'articolo [Aggiungere una VM con elementi a un lab](devtest-lab-add-vm-with-artifacts.md), quando si crea una VM in un lab, è possibile specificare una rete virtuale configurata. Questo potrebbe essere utile, ad esempio, per accedere alle risorse di rete aziendali dalle VM usando la rete virtuale configurata con ExpressRoute o VPN da sito a sito. Le sezioni seguenti illustrano come aggiungere la rete virtuale esistente alle impostazioni della rete virtuale di un lab in modo che sia disponibile al momento della creazione delle VM.

## Configurare una rete virtuale per un lab con il portale di Azure
I passaggi seguenti descrivono la procedura per aggiungere una rete virtuale esistente (e una subnet) a un lab in modo che sia disponibile al momento della creazione di una VM nel lab.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello del lab selezionare **Configurazione**.

1. Nel pannello **Configurazione** del lab selezionare **Reti virtuali**.

1. Nel pannello **Reti virtuali** viene visualizzato l'elenco delle reti virtuali configurate per il lab corrente, come pure la rete virtuale predefinita creata per il lab.

1. Selezionare **+ Aggiungi**.

	![Aggiungere una rete virtuale esistente al lab](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)  
	
1. Nel pannello **Rete virtuale** selezionare **[Selezionare rete virtuale]**.

	![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. Nel pannello **Scegli rete virtuale** selezionare la rete virtuale desiderata. Il pannello mostra tutte le reti virtuali presenti nella stessa area nella sottoscrizione del lab.

1. Dopo aver selezionato una rete virtuale, viene visualizzato nuovamente il pannello **Rete virtuale** con diversi campi abilitati.

	![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)  

1. Specificare una descrizione per la combinazione rete virtuale/lab.

1. Per consentire l'uso di una subnet nella creazione della VM per il lab, selezionare **USA NELLA CREZIONE DI MACCHINE VIRTUALI**.

1. Per consentire gli indirizzi IP pubblici in una subnet, selezionare **CONSENTI IP PUBBLICO**.

1. Nel campo **MAX MACCHINE VIRTUALI PER UTENTE** specificare per ogni utente il massimo di VM per ogni subnet. Se si desidera un numero illimitato di VM, lasciare vuoto questo campo.

1. Selezionare **Salva**.

1. Dopo aver configurato la rete virtuale, è possibile selezionarla quando si crea una VM. Per informazioni su come creare una VM e specificare una rete virtuale, vedere l'articolo [Aggiungere una VM con elementi a un lab](devtest-lab-add-vm-with-artifacts.md).

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Passaggi successivi

Dopo avere aggiunto la rete virtuale desiderata al lab, il passaggio successivo consiste nell'[aggiungere una VM a un lab](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->