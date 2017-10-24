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
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Come creare un set di scalabilità di macchine virtuali tramite il portale di Azure
Questa esercitazione mostra quanto sia facile creare un set di scalabilità di macchine virtuali in pochi minuti usando il portale di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Scegliere l'immagine della VM dal Marketplace
Dal portale è possibile distribuire facilmente un set di scalabilità con CentOS, CoreOS, Debian, Ubuntu Server, altre immagini di Linux e immagini di Windows Server.

Innanzitutto, passare al [portale di Azure](https://portal.azure.com) in un browser Web. Fare clic su **Nuovo**, cercare **scale set** (set di scalabilità) e quindi selezionare la voce **Virtual machine scale set** (set di scalabilità di macchine virtuali):

![ricerca nel portale di set di scalabilità di macchine virtuali di Azure](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>Creare il set di scalabilità
È ora possibile usare le impostazioni predefinite e creare rapidamente il set di scalabilità.

* Immettere un nome per il set di scalabilità.  
Questo nome diventa la base del nome di dominio completo del bilanciamento del carico rispetto al set di scalabilità, pertanto, assicurarsi che il nome sia univoco in tutte le istanze di Azure.

* Selezionare il tipo di sistema operativo desiderato.

* Immettere il nome utente desiderato e selezionare il tipo di autenticazione preferito.  
Se è stata scelta una password, questa deve avere una lunghezza di 12 caratteri e deve soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Se si sceglie **Chiave pubblica SSH**, assicurarsi di incollare solo la chiave pubblica, *NON* la chiave privata:

* Selezionare **Sì** o **No** in **Abilita il ridimensionamento oltre 100 istanze**.  
Se si seleziona Sì, il set di scalabilità può estendersi su più gruppi di selezione host. Per altre informazioni, vedere [questa documentazione](./virtual-machine-scale-sets-placement-groups.md).

* Assicurarsi di selezionare **dimensioni istanza** adeguate.  
Per altre informazioni sulle dimensioni della macchina virtuale, vedere [Dimensioni delle macchine virtuali Windows](..\virtual-machines\windows\sizes.md) o [Dimensioni delle macchine virtuali Linux](..\virtual-machines\linux\sizes.md).

* Immettere il nome e la posizione del gruppo di risorse desiderato.  
Se l'area e le **dimensioni istanza** supportano le zone di disponibilità, il campo **Zona di disponibilità** è abilitato. Per altre informazioni sulle zone di disponibilità, vedere questo articolo di [panoramica](../availability-zones/az-overview.md).

* Immettere l'etichetta del nome di dominio desiderata (la base del nome di dominio completo per il bilanciamento del carico davanti al set di scalabilità).  
Questa etichetta deve essere univoca nell'ambiente Azure.

* Scegliere l'immagine del disco del sistema operativo desiderata, il numero di istanze e le dimensioni del computer.

* Scegliere il tipo di disco desiderato: gestito o non gestito.  
Per altre informazioni, vedere [questa documentazione](./virtual-machine-scale-sets-managed-disks.md). Se si sceglie di includere gruppi di posizionamento nel set di scalabilità, questa opzione non sarà disponibile perché è necessario un disco gestito per consentire ai set di scalabilità di includere gruppi di posizionamento.

* Abilitare o disabilitare il ridimensionamento automatico e configurarlo se abilitato.

![richiesta di creazione nel portale di un set di scalabilità di macchine virtuali di Azure](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>Connettersi a una VM nel set di scalabilità
Se si sceglie di limitare il set di scalabilità a un singolo gruppo di posizionamento, il set di scalabilità viene distribuito con le regole NAT configurate per consentire facilmente la connessione al set di scalabilità; in caso contrario, per connettersi alle macchine virtuali nel set di scalabilità, è probabilmente necessario creare un jumpbox nella stessa rete virtuale del set di scalabilità. Per esaminare gli elementi, passare alla scheda `Inbound NAT Rules` del servizio di bilanciamento del carico per il set di scalabilità:

![regole NAT nel portale per un set di scalabilità di macchine virtuali di Azure](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

È possibile connettersi a ogni macchina virtuale nel set di scalabilità usando le regole NAT. Ad esempio, per un set di scalabilità di Windows, se è disponibile una regola NAT nella porta di ingresso 50000, è possibile connettersi a tale computer tramite RDP in `<load-balancer-ip-address>:50000`. Per un set di scalabilità di Linux, è necessario connettersi usando il comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Passaggi successivi
Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità dall'interfaccia della riga di comando, vedere [questa pagina](virtual-machine-scale-sets-cli-quick-create.md).

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità da PowerShell, vedere [questa pagina](virtual-machine-scale-sets-windows-create.md).

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità da Visual Studio, vedere [questa pagina](virtual-machine-scale-sets-vs-create.md).

Per una documentazione generale, vedere la [pagina di panoramica della documentazione per i set di scalabilità](virtual-machine-scale-sets-overview.md).

Per informazioni generali, vedere la [pagina di destinazione principale per i set di scalabilità](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

