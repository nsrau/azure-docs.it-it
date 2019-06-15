---
title: Problemi di riavvio o ridimensionamento della VM in Azure | Documentazione Microsoft
description: Risolvere i problemi della distribuzione Resource Manager con il riavvio e il ridimensionamento di una macchina virtuale esistente in Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f510a111a6c8846b300c09f368a3a2a05b2bb7ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719890"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Risolvere i problemi di distribuzione con il riavvio o il ridimensionamento di una VM Windows esistente in Azure
Quando si prova ad avviare una macchina virtuale (VM) di Azure arrestata o se ne ridimensiona una esistente, l'errore comune che si verifica è un errore di allocazione. L'errore si verifica quando il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni della VM richieste.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Raccogliere i log di attività
Per avviare la risoluzione dei problemi, raccogliere i log delle attività per identificare l'errore associato al problema. I collegamenti seguenti contengono informazioni dettagliate sul processo:

[Visualizzare le operazioni di distribuzione](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visualizzare i log attività per gestire le risorse di Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Errore durante l'avvio di una macchina virtuale arrestata
Si prova ad avviare una VM arrestata ma viene visualizzato un errore di allocazione.

### <a name="cause"></a>Causa
La richiesta di avvio della VM arrestata deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non ha spazio disponibile per soddisfare la richiesta.

### <a name="resolution"></a>Risoluzione
* Arrestare tutte le VM nel set di disponibilità e quindi riavviare ogni VM.
  
  1. Fare clic su **Gruppi di risorse** > *gruppo di risorse personale* > **Risorse** > *set di disponibilità personale* > **Macchine virtuali** > *macchina virtuale personale* > **Arresta**.
  2. Dopo l'arresto di tutte le VM, selezionare le VM arrestate e fare clic su Avvia.
* Ripetere la richiesta di riavvio in un secondo momento.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Errore durante il ridimensionamento di una macchina virtuale esistente
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
Se si verificano problemi durante la creazione di una nuova VM Windows in Azure, vedere [Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Windows in Azure](../windows/troubleshoot-deployment-new-vm.md).

