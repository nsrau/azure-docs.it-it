---
title: "Creare set di scalabilità di macchine virtuali di Azure con l&quot;interfaccia della riga di comando di Azure | Documentazione Microsoft"
description: "Informazioni sui set di scalabilità delle macchine virtuali."
keywords: "macchina virtuale linux,set di scalabilità macchine virtuali"
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5b8950619da3f42bc3c92443a7b9e9f2a97067f9
ms.openlocfilehash: 8d274127a4e1947e42b5544c3c1f62c4e48dcee9


---
# <a name="what-are-virtual-machine-scale-sets"></a>Che cosa sono i set di scalabilità delle macchine virtuali?
I set di scalabilità di macchine virtuali consentono di gestire più VM come un unico set. A livello generale, i set di scalabilità presentano i vantaggi e gli svantaggi seguenti:

Vantaggi:

1. Disponibilità elevata. Ogni set di scalabilità inserisce le relative VM in un set di disponibilità con 5 domini di errore (FD) e 5 domini di aggiornamento (UD) per garantire la disponibilità (per altre informazioni su domini di errore e domini di aggiornamento, vedere [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
2. Facile integrazione con il servizio di bilanciamento del carico di Azure e il gateway app.
3. Facile integrazione con la scalabilità automatica di Azure.
4. Distribuzione, gestione e pulizia semplificate delle VM.
5. Supporto delle versioni comuni di Windows e Linux e di immagini personalizzate.

Svantaggi:

1. Impossibile collegare dischi dati a istanze di VM in un set di scalabilità. Di contro, è necessario utilizzare l'archiviazione BLOB, file di Azure, tabelle di Azure o un'altra soluzione di archiviazione.

## <a name="quick-create-using-azure-cli"></a>Creazione rapida tramite l'interfaccia della riga di comando di Azure
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Per informazioni generali, vedere la [pagina di destinazione principale per i set di scalabilità](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Per altra documentazione, vedere la [pagina della documentazione principale per i set di scalabilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Ad esempio, per i modelli di Resource Manager con set di scalabilità, cercare "vmss" nel [repository di GitHub dei modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).




<!--HONumber=Jan17_HO4-->


