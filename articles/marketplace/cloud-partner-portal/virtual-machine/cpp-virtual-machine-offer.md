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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: d51fffad897ba2658c7bee51c26e7e3be9f10e88
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188759"
---
# <a name="virtual-machine-offer"></a>Offerta di una macchina virtuale

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Questa sezione descrive come pubblicare una nuova offerta di macchina virtuale in [Azure Marketplace](https://azuremarketplace.microsoft.com). Viene fornito il supporto per macchine virtuali basate su Windows e basate su Linux, che contiene un disco rigido virtuale del sistema operativo (VHD) e zero o più dischi rigidi virtuali di dati. | ![icone della macchina virtuale](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Panoramica della pubblicazione

Il video seguente, [Optimize Your Azure Marketplace Offer](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player) (Ottimizzare l'offerta in Microsoft Azure Marketplace), presenta un'ampia panoramica di Microsoft Azure Marketplace, incluse le procedure per la pubblicazione nel marketplace (usando una soluzione di macchina virtuale), come ottimizzare l'esperienza utente con la pagina dei prodotti e l'esperienza Test Drive facoltativa, come vengono generati i lead dell'utente e come è possibile usarli e ottimizzare il coinvolgimento dei clienti.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026]


## <a name="vm-publishing-process-flow"></a>Flusso del processo di pubblicazione di una macchina virtuale

Il diagramma seguente illustra ad alto livello la procedura di pubblicazione di un'offerta di macchina virtuale. 

![Processo di pubblicazione di una VM](./media/publishvm_001.png)

1. Creare l'offerta: vengono configurati tutti i dettagli e tutte le informazioni sull'offerta, ad esempio la descrizione dell'offerta, i materiali di marketing, le clausole legali, le informazioni di supporto e le specifiche relative alle risorse.

2. Creare le risorse commerciali e tecniche: creare le risorse commerciali (documenti legali e materiali di marketing) e tecniche per la soluzione associata (in questo caso, la macchina virtuale e i dischi collegati). 

3. Creare lo SKU: creare lo SKU o gli SKU associati all'offerta e inviarli.  Per ogni immagine che si intende pubblicare è necessario uno SKU univoco. 
 
4. Certificare e pubblicare l'offerta: dopo il completamento delle risorse tecniche e dell'offerta, è possibile procedere all'invio di questa. L'invio avvia il processo di pubblicazione, durante il quale la soluzione viene testata, convalidata, certificata e quindi "lanciata" nel marketplace.  

## <a name="next-steps"></a>Passaggi successivi

Prima prendere in considerazione questi passaggi, è necessario soddisfare i [requisiti tecnici e commerciali](./cpp-prerequisites.md) per la pubblicazione di una macchina virtuale in Microsoft Azure Marketplace. 
