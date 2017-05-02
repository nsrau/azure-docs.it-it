---
title: "Creare un set di scalabilità tramite il portale di Azure | Microsoft Docs"
description: "Distribuire i set di scalabilità tramite il portale di Azure."
keywords: "set di scalabilità di macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 308355e7d66c977f5cbfc8de3b939b76c2f8f83c
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Come creare un set di scalabilità di macchine virtuali tramite il portale di Azure
Questa esercitazione mostra quanto sia facile creare un set di scalabilità di macchine virtuali in pochi minuti usando il portale di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Scegliere l'immagine della VM dal Marketplace
Dal portale è possibile distribuire facilmente un set di scalabilità con CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu Server o le immagini di Windows Server.

Innanzitutto, passare al [portale di Azure](https://portal.azure.com) in un browser Web. Fare clic su `New`, cercare `scale set` e selezionare la voce `Virtual machine scale set`:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-scale-set"></a>Creare il set di scalabilità
È ora possibile usare le impostazioni predefinite e creare rapidamente il set di scalabilità.

* Nel pannello `Basics` immettere un nome per il set di scalabilità. Questo nome diventa la base del nome di dominio completo del bilanciamento del carico rispetto al set di scalabilità, pertanto, assicurarsi che il nome sia univoco in tutte le istanze di Azure.
* Selezionare il tipo di sistema operativo desiderato, immettere il nome utente desiderato e selezionare il tipo di autenticazione preferito. Se è stata scelta una password, questa deve avere una lunghezza di 12 caratteri e deve soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Se si sceglie `SSH public key`, assicurarsi di incollare solo la chiave pubblica, NON quella privata:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Scegliere se si vuole limitare il set di scalabilità a un singolo gruppo di posizionamento o includere più gruppi di posizionamento. Se si consente l'inclusione di gruppi di posizionamento nel set di scalabilità, si consentono set di scalabilità con capacità pari a più di 100 VM (fino a 1.000) con alcune limitazioni. Per altre informazioni, vedere [questa documentazione](./virtual-machine-scale-sets-placement-groups.md).
* Immettere il nome del gruppo di risorse desiderato e il percorso e fare clic su `OK`.
* Nel pannello `Virtual machine scale set service settings` immettere l'etichetta del nome di dominio desiderato (la base del nome di dominio completo per il bilanciamento del carico rispetto al set di scalabilità). Questa etichetta deve essere univoca nell'ambiente Azure.
* Scegliere l'immagine del disco del sistema operativo desiderata, il numero di istanze e le dimensioni del computer.
* Scegliere il tipo di disco desiderato: gestito o non gestito. Per altre informazioni, vedere [questa documentazione](./virtual-machine-scale-sets-managed-disks.md). Se si sceglie di includere gruppi di posizionamento nel set di scalabilità, questa opzione non sarà disponibile perché è necessario un disco gestito per consentire ai set di scalabilità di includere gruppi di posizionamento.
* Abilitare o disabilitare il ridimensionamento automatico e, se abilitato, configurarlo:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Al termine della convalida fare clic su `OK` nel pannello `Summary` per iniziare a distribuire il set di scalabilità.


## <a name="connect-to-a-vm-in-the-scale-set"></a>Connettersi a una VM nel set di scalabilità
Se si sceglie di limitare il set di scalabilità a un singolo gruppo di posizionamento, il set di scalabilità viene distribuito con le regole NAT configurate per consentire facilmente la connessione al set di scalabilità; in caso contrario, per connettersi alle macchine virtuali nel set di scalabilità, è probabilmente necessario creare un jumpbox nella stessa rete virtuale del set di scalabilità. Per esaminare gli elementi, passare alla scheda `Inbound NAT Rules` del servizio di bilanciamento del carico per il set di scalabilità:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

È possibile connettersi a ogni macchina virtuale nel set di scalabilità usando le regole NAT. Ad esempio, per un set di scalabilità di Windows, se è disponibile una regola NAT nella porta di ingresso 50000, è possibile connettersi a tale computer tramite RDP in `<load-balancer-ip-address>:50000`. Per un set di scalabilità di Linux, è necessario connettersi usando il comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Passaggi successivi
Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità dall'interfaccia della riga di comando, vedere [questa pagina](virtual-machine-scale-sets-cli-quick-create.md).

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità da PowerShell, vedere [questa pagina](virtual-machine-scale-sets-windows-create.md).

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità da Visual Studio, vedere [questa pagina](virtual-machine-scale-sets-vs-create.md).

Per una documentazione generale, vedere la [pagina di panoramica della documentazione per i set di scalabilità](virtual-machine-scale-sets-overview.md).

Per informazioni generali, vedere la [pagina di destinazione principale per i set di scalabilità](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


