<properties
	pageTitle="Configurare una rete virtuale per un lab di sviluppo/test | Microsoft Azure"
	description="Informazioni su come configurare una rete virtuale esistente e una subnet e utilizzarle in una VM"
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
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# Configurare una rete virtuale per un lab di sviluppo/test

## Panoramica

Come illustrato nell'articolo [Aggiungere una VM con elementi a un lab di sviluppo e test di Azure](devtest-lab-add-vm-with-artifacts.md), quando si crea una VM in un lab, è possibile specificare una rete virtuale configurata (e una subnet) per la macchina virtuale. Questo potrebbe essere utile, ad esempio, per accedere alle risorse di rete aziendali dalle VM utilizzando la rete virtuale impostata con ExpressRoute o VPN da sito a sito. Le sezioni seguenti illustrano come aggiungere la rete virtuale esistente alle impostazioni della rete virtuale del lab in modo che sia disponibile al momento della creazione delle VM.

## Configurare una rete virtuale per un lab di sviluppo/test con il portale di Azure
I passaggi seguenti descrivono la procedura per aggiungere una rete virtuale esistente (e una subnet) a un lab di sviluppo/test in modo che sia disponibile al momento della creazione di una VM nel lab.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Nell'elenco dei lab toccare il lab desiderato.

1. Verrà visualizzato il pannello **Impostazioni** del lab selezionato.

1. Toccare **Reti virtuali**.

	![È possibile configurare le reti virtuali dal pannello Impostazioni del lab](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. Nel pannello **Reti virtuali** verrà visualizzato l'elenco delle reti virtuali configurate per il lab corrente, come pure la rete virtuale predefinita creata per il lab.

1. Toccare **+ Aggiungi**.

	![Aggiungere una rete virtuale esistente al lab](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. Nel pannello **Rete virtuale** toccare **[Selezionare rete virtuale]**.

	![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. Nel pannello **Scegli rete virtuale** selezionare la rete virtuale desiderata. Il pannello mostra tutte le reti virtuali presenti nella stessa area nella sottoscrizione del lab.

1. Quando si seleziona una rete virtuale e si torna al pannello **Rete virtuale**, si noterà che alcuni campi sono stati abilitati.

	![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Specificare una descrizione per la combinazione rete virtuale/lab.

1. Per consentire l'uso di una subnet nella creazione della VM per il lab, selezionare l'opzione **USA NELLA CREZIONE DI VM**.

1. Per consentire gli indirizzi IP pubblici in una subnet, selezionare l'opzione **CONSENTI IP PUBBLICO**.

1. Nel campo **MAX VM PER UTENTE** specificare le VM massime per utente per ogni subnet. Se si desidera un numero illimitato di VM, lasciare vuoto questo campo.

1. Toccare **Salva**.

1. Dopo aver configurato la rete virtuale, è possibile selezionarla quando si crea una nuova VM. Questa operazione è esemplificata nell'articolo [Aggiungere una VM con elementi a un lab di sviluppo e test di Azure](devtest-lab-add-vm-with-artifacts.md).

## Passaggi successivi

Dopo aver aggiunto le reti virtuali desiderate al lab, il passaggio successivo consiste nell'[aggiungere una VM a un lab di sviluppo/test di Azure](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0323_2016-->