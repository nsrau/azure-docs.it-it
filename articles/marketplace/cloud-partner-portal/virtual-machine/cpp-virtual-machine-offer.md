---
title: Offerta di macchine virtuali in Azure MarketplaceVirtual machine offer in the Azure Marketplace
description: Panoramica del processo per la pubblicazione di un'offerta di macchina virtuale in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: 0f2ae9fe6f006b5418ebee82b08a44188b7c58d3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273070"
---
# <a name="virtual-machine-offer"></a>Offerta di una macchina virtuale

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo a spostare la gestione delle offerte di Macchine virtuali di Azure nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare un'offerta di macchina virtuale](https://aka.ms/CreateAzureVMoffer) di Azure per gestire le offerte migrate.

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Questa sezione descrive come pubblicare una nuova offerta di macchina virtuale in [Azure Marketplace](https://azuremarketplace.microsoft.com). Viene fornito il supporto per macchine virtuali basate su Windows e basate su Linux, che contiene un disco rigido virtuale del sistema operativo (VHD) e zero o più dischi rigidi virtuali di dati. | ![icone della macchina virtuale](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Panoramica della pubblicazione

Il video seguente, [Optimize Your Azure Marketplace Offer](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player) (Ottimizzare l'offerta in Microsoft Azure Marketplace), presenta un'ampia panoramica di Microsoft Azure Marketplace, incluse le procedure per la pubblicazione nel marketplace (usando una soluzione di macchina virtuale), come ottimizzare l'esperienza utente con la pagina dei prodotti e l'esperienza Test Drive facoltativa, come vengono generati i lead dell'utente e come è possibile usarli e ottimizzare il coinvolgimento dei clienti.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Flusso del processo di pubblicazione di una macchina virtuale

Il diagramma seguente illustra ad alto livello la procedura di pubblicazione di un'offerta di macchina virtuale. 

![Processo di pubblicazione di una VM](./media/publishvm_001.png)

1. Creare l'offerta: vengono configurati tutti i dettagli e tutte le informazioni sull'offerta, ad esempio la descrizione dell'offerta, i materiali di marketing, le clausole legali, le informazioni di supporto e le specifiche relative alle risorse.

2. Creare le risorse commerciali e tecniche: creare le risorse commerciali (documenti legali e materiali di marketing) e tecniche per la soluzione associata (in questo caso, la macchina virtuale e i dischi collegati). 

3. Creare lo SKU: creare lo SKU o gli SKU associati all'offerta e inviarli.  Per ogni immagine che si intende pubblicare è necessario uno SKU univoco. 
 
4. Certificare e pubblicare l'offerta: dopo il completamento delle risorse tecniche e dell'offerta, è possibile procedere all'invio di questa. L'invio avvia il processo di pubblicazione, durante il quale la soluzione viene testata, convalidata, certificata e quindi "lanciata" nel marketplace.  

## <a name="next-steps"></a>Passaggi successivi

Prima prendere in considerazione questi passaggi, è necessario soddisfare i [requisiti tecnici e commerciali](./cpp-prerequisites.md) per la pubblicazione di una macchina virtuale in Microsoft Azure Marketplace. 
