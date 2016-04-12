<properties
	pageTitle="Risolvere i problemi relativi a errori di allocazione delle VM Linux | Microsoft Azure"
	description="Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Linux in Azure"
	services="virtual-machines-linux, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resourece-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Linux in Azure

Quando si crea una VM, si riavviano VM arrestate (deallocate) o si ridimensiona una VM, Microsoft Azure alloca risorse di calcolo alla sottoscrizione. In alcuni casi possono verificarsi errori quando si eseguono queste operazioni, anche prima di raggiungere i limiti della sottoscrizione di Azure. Questo articolo illustra le cause di alcuni dei più comuni errori di allocazione e suggerisce una possibile correzione. Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

La sezione "Passaggi per la risoluzione dei problemi generali" elenca i passaggi per risolvere problemi comuni. La sezione "Procedura di risoluzione dei problemi dettagliata" offre passaggi per la risoluzione dei problemi in base al messaggio di errore specifico. Prima di iniziare, di seguito sono riportate alcune informazioni di base per comprendere il funzionamento dell'allocazione e il motivo per cui si verificano gli errori di allocazione. È anche possibile [risolvere i problemi relativi agli errori di allocazione quando si crea, si riavvia o si ridimensiona una VM Windows in Azure](virtual-machines-windows-allocation-failure.md).


[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0330_2016-->