---
title: Configurare gli endpoint in una VM Linux classica | Microsoft Docs
description: Informazioni su come configurare gli endpoint per una macchina virtuale Linux nel portale di Azure classico per consentire la comunicazione con una VM Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn

---
# Come configurare gli endpoint in una macchina virtuale Linux classica in Azure
Tutte le macchine virtuali Linux create in Azure con il modello di distribuzione classico possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in Internet o altre reti virtuali richiedono gli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale. Questo articolo è disponibile anche per le [macchine virtuali Windows](virtual-machines-windows-classic-setup-endpoints.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Nel modello di distribuzione **Resource Manager** gli endpoint vengono configurati usando **Gruppi di sicurezza di rete (NGS)**. Per altre informazioni, vedere [Apertura di porte e di endpoint](virtual-machines-linux-nsg-quickstart.md).

Quando si crea una macchina virtuale Linux nel portale di Azure classico, viene in genere creato automaticamente un endpoint per Secure Shell (SSH). È possibile configurare altri endpoint durante la creazione della macchina virtuale o successivamente all'occorrenza.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Passaggi successivi
* È anche possibile creare un endpoint di VM con l'[interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md). Eseguire il comando **azure vm endpoint create**.
* Se una macchina virtuale è stata creata nel modello di distribuzione Resource Manager, è possibile usare l'interfaccia della riga di comando di Azure in modalità Resource Manager per [creare gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-cli.md) per controllare il traffico verso la macchina virtuale.

<!---HONumber=AcomDC_0713_2016-->