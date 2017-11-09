---
title: Risolvere i problemi della distribuzione classica di una macchina virtuale Windows | Microsoft Docs
description: Risolvere i problemi della distribuzione classica quando si crea una nuova macchina virtuale Windows in Azure
services: virtual-machines-windows
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 9f01d237-ba39-4c32-b72d-18f5f505d43a
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 2fe0990e05758b7e538097915ffe82d9a3ec9d05
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Risolvere i problemi della distribuzione classica con la creazione di una nuova macchina virtuale Windows in Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per la versione di Resource Manager di questo articolo, vedere [qui](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Raccogliere log di controllo
Per avviare la risoluzione dei problemi, raccogliere i log di controllo per identificare l'errore associato al problema.

Nel portale di Azure fare clic su **Esplora** > **Macchine virtuali** > *macchina virtuale Windows* > **Impostazioni** > **Log di controllo**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**S:** se il sistema operativo è Windows generalizzato e viene caricato e/o acquisito con l'impostazione generalizzata, non si verificheranno errori. Analogamente, se il sistema operativo Windows è specializzato e viene caricato e/o acquisito con l'impostazione specializzata, non si verificheranno errori.

**Errori di caricamento:**

**N<sup>1</sup>:** se il sistema operativo è Windows generalizzato e viene caricato come specializzato, si verificherà un errore di timeout del provisioning con la VM bloccata nella schermata di Configurazione guidata.

**N<sup>2</sup>:** se il sistema operativo è Windows specializzato e viene caricato come generalizzato, si verificherà un errore di provisioning con la VM bloccata nella schermata di Configurazione guidata perché la nuova VM è in esecuzione con nome computer, nome utente e password originali.

**Risoluzione:**

Per risolvere entrambi questi errori, caricare il disco rigido virtuale originale, disponibile in locale, con la stessa impostazione usata per il sistema operativo (generalizzato/specializzato). Per caricare il disco come generalizzato, ricordarsi di eseguire prima sysprep. Per altre informazioni, vedere [Creare e caricare un disco rigido virtuale con Windows Server in Azure](createupload-vhd.md) .

**Errori di acquisizione:**

**N<sup>3</sup>:** se il sistema operativo è Windows generalizzato e viene acquisito come specializzato, si verificherà un errore di timeout del provisioning perché la VM originale non può essere usata essendo contrassegnata come generalizzata.

**N<sup>4</sup>:** se il sistema operativo è Windows specializzato e viene acquisito come generalizzato, si verificherà un errore di provisioning perché la nuova VM è in esecuzione con nome computer, nome utente e password originali. La VM originale, inoltre, non può essere usata perché è contrassegnata come specializzata.

**Risoluzione:**

Per risolvere entrambi questi errori, eliminare l'immagine corrente dal portale e [acquisirla di nuovo dai dischi rigidi virtuali correnti](capture-image.md) con la stessa impostazione usata per il sistema operativo (generalizzato/specializzato).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Immagine personalizzata/della raccolta/del marketplace - errore di allocazione
Questo errore si verifica nelle situazioni in cui la nuova richiesta di VM viene inviata a un cluster che non ha spazio disponibile sufficiente per soddisfare la richiesta o che non può supportare le dimensioni della VM richieste. Non è possibile combinare serie di VM diverse nello stesso servizio cloud. Se si vuole quindi creare una nuova VM con dimensioni diverse rispetto a quelle che il servizio cloud può supportare, la richiesta di calcolo non riuscirà.

In base ai vincoli del servizio cloud usato per creare la nuova VM, si potrebbe verificare un errore causato da una delle due situazioni seguenti.

**Causa 1:** il servizio cloud è aggiunto a un cluster specifico oppure è collegato a un gruppo di affinità e perciò aggiunto a un cluster specifico in base alla progettazione. Le nuove richieste di risorse di calcolo in quel gruppo di affinità vengono quindi provate nello stesso cluster in cui sono ospitate le risorse esistenti. Tuttavia, lo stesso cluster potrebbe non supportare le dimensioni della VM richieste o non ha spazio disponibile sufficiente, causando un errore di allocazione. Questo vale indipendentemente dal fatto che le nuove risorse vengano create tramite un servizio cloud nuovo o esistente.

**Risoluzione 1:**

* Creare un nuovo servizio cloud e associarlo a un'area o una rete virtuale basata sull'area.
* Crea una nuova VM nel nuovo servizio cloud.
  Se si verifica un errore durante il tentativo di creare un nuovo servizio cloud, riprovare in un secondo momento o cambiare l'area per il servizio cloud.

> [!IMPORTANT]
> Se si sta provando a creare una nuova VM in un servizio cloud esistente, ma è stato necessario creare un nuovo servizio cloud per la nuova VM, è possibile scegliere di consolidare tutte le VM nello stesso servizio cloud. A questo scopo, eliminare le VM nel servizio cloud esistente e acquisirle di nuovo dai relativi dischi nel nuovo servizio cloud. È tuttavia importante ricordare che il nuovo servizio cloud avrà un nuovo nome e un nuovo indirizzo VIP, quindi sarà necessario aggiornarli per tutte le dipendenze che attualmente usano queste informazioni per il servizio cloud esistente.
> 
> 

**Causa 2:** il servizio cloud è associato a una rete virtuale collegata a un gruppo di affinità e perciò è aggiunto a un cluster specifico in base alla progettazione. Tutte le nuove richieste di risorse di calcolo in quel gruppo di affinità vengono quindi provate nello stesso cluster in cui sono ospitate le risorse esistenti. Tuttavia, lo stesso cluster potrebbe non supportare le dimensioni della VM richieste o non ha spazio disponibile sufficiente, causando un errore di allocazione. Questo vale indipendentemente dal fatto che le nuove risorse vengano create tramite un servizio cloud nuovo o esistente.

**Risoluzione 2:**

* Crea una nuova rete virtuale a livello di area
* Creare la nuova VM nella nuova rete virtuale.
* [Connettere la rete virtuale esistente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) alla nuova rete virtuale. Altre informazioni sulle [reti virtuali a livello di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). In alternativa, è possibile [eseguire la migrazione della rete virtuale basata su gruppi di affinità alla rete virtuale a livello di area](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e quindi creare la nuova VM.

## <a name="next-steps"></a>Passaggi successivi
Se si incontrano problemi quando si avvia una VM Windows arrestata o si ridimensiona una VM Windows esistente in Azure, vedere l'articolo su come [risolvere i problemi della distribuzione classica con il riavvio o il ridimensionamento di una macchina virtuale Windows esistente in Azure](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md).

