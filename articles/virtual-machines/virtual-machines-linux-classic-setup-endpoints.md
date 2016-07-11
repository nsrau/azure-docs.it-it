<properties
	pageTitle="Configurare gli endpoint in una VM Linux classica | Microsoft Azure"
	description="Informazioni su come configurare gli endpoint nel portale di Azure classico per consentire la comunicazione con una macchina virtuale Linux in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# Come configurare gli endpoint in una macchina virtuale classica in Azure

Tutte le macchine virtuali Linux create in Azure con il modello di distribuzione classico possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in Internet o altre reti virtuali richiedono gli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale. Questo articolo è disponibile anche per le [macchine virtuali Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Quando si crea una macchina virtuale Linux nel portale di Azure classico, viene in genere creato automaticamente un endpoint per Secure Shell (SSH). È possibile configurare altri endpoint durante la creazione della macchina virtuale o successivamente all'occorrenza.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Passaggi successivi

* È anche possibile creare un endpoint di VM con l'[interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md). Eseguire il comando **azure vm endpoint create**.

* Se una macchina virtuale è stata creata nel modello di distribuzione Resource Manager, è possibile usare l'interfaccia della riga di comando di Azure in modalità Resource Manager per [creare gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-cli.md) per controllare il traffico verso la VM.

<!---HONumber=AcomDC_0629_2016-->