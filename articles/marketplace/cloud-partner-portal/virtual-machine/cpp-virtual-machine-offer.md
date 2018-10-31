---
title: Offerta di una macchina virtuale in Azure Marketplace | Microsoft Docs
description: Panoramica del processo per la pubblicazione di un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639132"
---
# <a name="virtual-machine-offer"></a>Offerta di una macchina virtuale

Questa sezione, che descrive gli elementi della pubblicazione di una macchina virtuale (VM, Virtual Machine), è da intendersi come guida per chi effettua la pubblicazione in [Azure Marketplace](https://azuremarketplace.microsoft.com).  Da questo punto di vista, è suddivisa nelle parti principali seguenti:

- [Prerequisiti](./cpp-prerequisites.md): elenca i requisiti tecnici e commerciali necessari per la creazione o la pubblicazione dell'offerta di una macchina virtuale
- [Creare un'offerta di macchina virtuale](./cpp-create-offer.md): elenca i passaggi necessari per creare la voce dell'offerta di una nuova macchina virtuale usando il [portale Cloud Partner](https://cloudpartner.azure.com)
- [Creare le risorse tecniche della macchina virtuale](./cpp-create-technical-assets.md): descrive come creare le risorse tecniche per una soluzione di macchina virtuale e come configurare il pacchetto come offerta di macchina virtuale in Azure Marketplace
- [Pubblicare l'offerta di macchina virtuale](./cpp-publish-offer.md): descrive come inviare l'offerta per la pubblicazione in Azure Marketplace


## <a name="vm-publishing-process-flow"></a>Flusso del processo di pubblicazione di una macchina virtuale

Il diagramma seguente illustra ad alto livello la procedura di pubblicazione di un'offerta di macchina virtuale. 

![Processo di pubblicazione di una VM](./media/publishvm_001.png)

1. Creare l'offerta: vengono configurati tutti i dettagli e tutte le informazioni sull'offerta, ad esempio la descrizione dell'offerta, i materiali di marketing, le clausole legali, le informazioni di supporto e le specifiche relative alle risorse.

2. Creare le risorse commerciali e tecniche: creare le risorse commerciali (documenti legali e materiali di marketing) e tecniche per la soluzione associata (in questo caso, la macchina virtuale e i dischi collegati). 

3. Creare lo SKU: creare lo SKU o gli SKU associati all'offerta e inviarli.  Per ogni immagine che si intende pubblicare è necessario uno SKU univoco. 
 
4. Certificare e pubblicare l'offerta: dopo il completamento delle risorse tecniche e dell'offerta, è possibile procedere all'invio di questa. L'invio avvia il processo di pubblicazione, durante il quale la soluzione viene testata, convalidata, certificata e quindi "lanciata" nel marketplace.  

## <a name="next-steps"></a>Passaggi successivi

Prima prendere in considerazione questi passaggi, è necessario soddisfare i [requisiti tecnici e commerciali](./cpp-prerequisites.md) per la pubblicazione di una macchina virtuale in Microsoft Azure Marketplace. 
