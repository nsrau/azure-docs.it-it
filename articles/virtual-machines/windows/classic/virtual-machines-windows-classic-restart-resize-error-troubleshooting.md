---
title: Problemi di riavvio o ridimensionamento della VM | Documentazione Microsoft
description: Risolvere i problemi della distribuzione classica con il riavvio e il ridimensionamento di una macchina virtuale Windows esistente in Azure
services: virtual-machines-windows
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 11/03/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: bed5da25042d29983bad9a80cd44bdd7df261c2e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Risolvere i problemi della distribuzione classica con il riavvio e il ridimensionamento di una macchina virtuale Windows esistente in Azure
> [!div class="op_single_selector"]
> * [Classico](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Gestione risorse](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Quando si prova ad avviare una macchina virtuale (VM) di Azure arrestata o se ne ridimensiona una esistente, l'errore comune che si verifica è un errore di allocazione. L'errore si verifica quando il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni della VM richieste.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Raccogliere log di controllo
Per avviare la risoluzione dei problemi, raccogliere i log di controllo per identificare l'errore associato al problema.

Nel portale di Azure fare clic su **Esplora** > **Macchine virtuali** > *Macchina virtuale Windows* > **Impostazioni** > **Log di controllo**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Errore durante l'avvio di una VM arrestata
Si prova ad avviare una VM arrestata ma viene visualizzato un errore di allocazione.

### <a name="cause"></a>Causa
La richiesta di avvio della VM arrestata deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non ha spazio disponibile per soddisfare la richiesta.

### <a name="resolution"></a>Risoluzione
* Creare un nuovo servizio cloud e associarlo a un'area o una rete virtuale basata sull'area, ma non a un gruppo di affinità.
* Eliminare la VM arrestata.
* Ricreare la VM nel nuovo servizio cloud usando i dischi.
* Avviare la macchina virtuale ricreata.

Se si verifica un errore durante il tentativo di creare un nuovo servizio cloud, riprovare più tardi o cambiare l'area per il servizio cloud.

> [!IMPORTANT]
> Il nuovo servizio cloud avrà un nuovo nome e un indirizzo VIP, quindi si dovranno modificare tali informazioni per tutte le dipendenze che usano queste informazioni per il servizio cloud esistente.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Errore durante il ridimensionamento di una VM esistente
Si prova a ridimensionare una VM esistente ma viene visualizzato un errore di allocazione.

### <a name="cause"></a>Causa
La richiesta di ridimensionamento della VM deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non supporta le dimensioni della VM richieste.

### <a name="resolution"></a>Risoluzione
Ridurre le dimensioni della VM richieste e quindi ripetere la richiesta di ridimensionamento.

* Fare clic su **Esplora tutto** > **Macchine virtuali (classico)** > *la macchina virtuale* > **Impostazioni** > **Dimensioni**. Per i passaggi dettagliati, vedere l'articolo relativo al [Ridimensionamento della macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx).

Se non è possibile ridurre le dimensioni della VM, seguire questi passaggi:

* Creare un nuovo servizio cloud, verificando che non sia collegato a un gruppo di affinità e non sia associato a una rete virtuale collegata a un gruppo di affinità.
* Nel servizio creare una nuova VM con dimensioni maggiori.

È possibile consolidare tutte le VM nello stesso servizio cloud. Se il servizio cloud esistente è associato a una rete virtuale basata sull'area, è possibile connetterlo alla rete virtuale esistente.

Se il servizio cloud esistente non è associato a una rete virtuale basata sull'area, è necessario eliminare le VM nel servizio cloud esistente e ricrearle nel nuovo servizio cloud dai relativi dischi. È tuttavia importante ricordare che il nuovo servizio cloud avrà un nuovo nome e un nuovo indirizzo VIP, quindi sarà necessario aggiornarli per tutte le dipendenze che attualmente usano queste informazioni per il servizio cloud esistente.

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi durante la creazione di una VM Windows in Azure, vedere [Risolvere i problemi di distribuzione quando si crea una nuova macchina virtuale Windows in Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

