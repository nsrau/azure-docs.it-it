---
title: Problemi di riavvio o ridimensionamento della VM | Documentazione Microsoft
description: Risolvere i problemi della distribuzione Resource Manager con il riavvio e il ridimensionamento di una macchina virtuale Linux esistente in Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 51f1610c-0290-464a-97d4-c2e485c7ae7f
ms.service: virtual-machines-linux
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.workload: required
ms.date: 09/09/2016
ms.author: delhan
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9f85988268d4c832c46da1790c83d4a78141f489


---
# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Risolvere i problemi della distribuzione Resource Manager con il riavvio e il ridimensionamento di una macchina virtuale Linux esistente in Azure
Quando si prova ad avviare una macchina virtuale (VM) di Azure arrestata o se ne ridimensiona una esistente, l'errore comune che si verifica è un errore di allocazione. L'errore si verifica quando il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni della VM richieste.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Raccogliere log di controllo
Per avviare la risoluzione dei problemi, raccogliere i log di controllo per identificare l'errore associato al problema. I collegamenti seguenti contengono informazioni dettagliate sul processo:

[Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operazioni di controllo con Gestione risorse](../resource-group-audit.md)

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
Se si verificano problemi durante la creazione di una nuova VM Linux in Azure, vedere [Risolvere i problemi della distribuzione classica con la creazione di una nuova macchina virtuale Linux in Azure](virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Nov16_HO3-->


