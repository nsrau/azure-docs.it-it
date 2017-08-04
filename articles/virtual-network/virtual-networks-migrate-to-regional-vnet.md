---
title: "Eseguire la migrazione di una rete virtuale di Azure (classica) da un gruppo di affinità in un'area | Microsoft Docs"
description: "Informazioni su come eseguire la migrazione di una rete virtuale (classica) da un gruppo di affinità in un'area."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b9b3bd0f2184ac85261166d5fe2ab67e1bf319d4
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Eseguire la migrazione di una rete virtuale (classica) da un gruppo di affinità in un'area

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo articolo illustra l'uso del modello di distribuzione classica. È consigliabile usare il modello di distribuzione di Resource Manager per le distribuzioni più recenti.

I gruppi di affinità assicurano che le risorse create all'interno di uno stesso gruppo di affinità vengano ospitate fisicamente da server vicini l'uno all'altro, in modo che le risorse possano comunicare più rapidamente. In passato, i gruppi di affinità erano un requisito per la creazione di reti virtuali (classiche). In quel periodo, il servizio di gestione di rete che gestiva le reti virtuali poteva operare solo all'interno di un set di server fisici, denominato unità di scala. I miglioramenti apportati all'architettura hanno esteso l'ambito di gestione delle reti a un’area.

A seguito di questi miglioramenti, i gruppi di affinità non sono più consigliati o necessari per le reti virtuali (classiche). Nelle reti virtuali (classiche), i gruppi di affinità sono stati sostituiti nell'uso dalle aree. Le reti virtuali (classiche) associate alle aree prendono il nome di reti virtuali a livello di area.

L'uso dei gruppi di affinità non è consigliabile in generale. Oltre che come requisito per le reti virtuali, i gruppi di affinità erano importanti anche per garantire che le risorse, ad esempio di calcolo (classica) o archiviazione (classica), fossero posizionate l'una vicino all'altra. Con l'architettura di rete corrente di Azure, tali requisiti di posizionamento non sono più necessari.

> [!IMPORTANT]
> Benché sia ancora tecnicamente possibile creare una rete virtuale associata a un gruppo di affinità, non esistono motivi vincolanti per procedere in questo modo. Molte funzionalità delle reti virtuali, come i gruppi di sicurezza di rete, sono disponibili solo se si usa una rete virtuale a livello di area e non per le reti virtuali associate a gruppi di affinità.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Modificare il file di configurazione di rete

1. Esportare il file di configurazione della rete. Per informazioni su come esportare un file di configurazione di rete tramite PowerShell o l'interfaccia della riga di comando 1.0 di Azure, vedere [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md#export).
2. Modificare il file di configurazione di rete, sostituendo **AffinityGroup** con **Location**. Specificare un'[area](https://azure.microsoft.com/regions) di Azure per **Location**.
   
   > [!NOTE]
   > **Location** corrisponde all'area specificata per il gruppo di affinità associato alla rete virtuale (classica). Ad esempio, se la rete virtuale (classica) è associata a un gruppo di affinità posizionato negli Stati Uniti occidentali, quando si esegue la migrazione, **Location** deve puntare a West US. 
   > 
   > 
   
    Modificare le righe seguenti nel file di configurazione della rete, sostituendo i valori esistenti con i propri: 
   
    **Valore precedente:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nuovo valore:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Salvare le modifiche e [importare](virtual-networks-using-network-configuration-file.md#import) la configurazione di rete in Azure.

> [!NOTE]
> Questa migrazione NON provoca alcun tempo di inattività per i servizi.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Come procedere se si ha una macchina virtuale (classica) in un gruppo di affinità
Le macchine virtuali (classiche) attualmente incluse in un gruppo di affinità non devono essere rimosse dal gruppo. Quando si esegue la distribuzione, una macchina virtuale viene distribuita in una singola unità di scala. I gruppi di affinità possono limitare il set di dimensioni di macchine virtuali disponibili per una nuova distribuzione, ma qualsiasi macchina virtuale esistente che venga distribuita è già limitata al set di dimensioni disponibile nell'unità di scala in cui avviene la distribuzione. Poiché la macchina virtuale è già distribuita in un'unità di scala, la rimozione di una macchina virtuale da un gruppo di affinità non incide in alcun modo sulla macchina virtuale.

