---
title: Problemi di riavvio o ridimensionamento della VM | Documentazione Microsoft
description: Risolvere i problemi della distribuzione Resource Manager con il riavvio e il ridimensionamento di una macchina virtuale Windows esistente in Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 01/10/2017
ms.author: delhan
translationtype: Human Translation
ms.sourcegitcommit: 94c18aa0c4fe38fb74931d5ed61fece207c8b5ce
ms.openlocfilehash: afa8d2b76ab0f85b5eaf615b264af2f29936016c


---
# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Risolvere i problemi della distribuzione Resource Manager con il riavvio e il ridimensionamento di una macchina virtuale Windows esistente in Azure
Quando si prova ad avviare una macchina virtuale (VM) di Azure arrestata o se ne ridimensiona una esistente, l'errore comune che si verifica è un errore di allocazione. L'errore si verifica quando il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni della VM richieste.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Raccogliere i log di attività
Per avviare la risoluzione dei problemi, raccogliere i log delle attività per identificare l'errore associato al problema. I collegamenti seguenti contengono informazioni dettagliate sul processo:

[Visualizzare le operazioni di distribuzione](../azure-resource-manager/resource-manager-deployment-operations.md)

[Visualizzare i log attività per gestire le risorse di Azure](../azure-resource-manager/resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Errore durante l'avvio di una VM arrestata
Si prova ad avviare una VM arrestata ma viene visualizzato un errore di allocazione.

### <a name="cause"></a>Causa
La richiesta di avvio della VM arrestata deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non ha spazio disponibile per soddisfare la richiesta.

### <a name="resolution"></a>Risoluzione
* Arrestare tutte le VM nel set di disponibilità e quindi riavviare ogni VM.
  
  1. Fare clic su **Gruppi di risorse** > *gruppo di risorse personale* > **Risorse** > *set di disponibilità personale* > **Macchine virtuali** > *macchina virtuale personale* > **Arresta**.
  2. Dopo l'arresto di tutte le VM, selezionare le VM arrestate e fare clic su Avvia.
* Ripetere la richiesta di riavvio in un secondo momento.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Errore durante il ridimensionamento di una VM esistente
Si prova a ridimensionare una VM esistente ma viene visualizzato un errore di allocazione.

### <a name="cause"></a>Causa
La richiesta di ridimensionamento della VM deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non supporta le dimensioni della VM richieste.

### <a name="resolution"></a>Risoluzione
* Ripetere la richiesta usando una VM di dimensioni inferiori.
* Se le dimensioni della VM richieste non possono essere modificate:
  
  1. Arrestare tutte le VM nel set di disponibilità.
     
     * Fare clic su **Gruppi di risorse** > *gruppo di risorse personale* > **Risorse** > *set di disponibilità personale* > **Macchine virtuali** > *macchina virtuale personale* > **Arresta**.
  2. Dopo l'arresto di tutte le VM, ridimensionare la VM desiderata impostando una dimensione maggiore.
  3. Selezionare la VM ridimensionata e fare clic su **Avvia**, quindi avviare ognuna delle VM arrestate.

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi durante la creazione di una nuova VM Windows in Azure, vedere [Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Windows in Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO2-->


