---
title: Ripristino di emergenza per la condivisione di dati di Azure
description: Ripristino di emergenza per la condivisione di dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218698"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Ripristino di emergenza per la condivisione di dati di Azure

Questo articolo illustra come configurare un ambiente di ripristino di emergenza per la condivisione di dati di Azure. Le interruzioni del data center di Azure sono rare, ma possono durare da pochi minuti a ore. Le interruzioni del Data Center possono causare interruzioni negli ambienti che si basano sui dati condivisi dal provider di dati. Seguendo i passaggi descritti in questo articolo, i provider di dati possono continuare a condividere i dati con i consumer di dati in caso di interruzione del data center per l'area primaria che ospita la condivisione di dati. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Raggiungimento della continuità aziendale per la condivisione di dati di Azure

Per prepararsi a un'interruzione del data center, il provider di dati può avere un ambiente di condivisione dati di cui è stato effettuato il provisioning in un'area secondaria. È possibile adottare misure per garantire un failover uniforme nel caso in cui si verifichi un'interruzione del data center. 

I provider di dati possono eseguire il provisioning di risorse di condivisione dati di Azure secondarie in un'area Queste risorse di condivisione dati possono essere configurate per includere le condivisioni e i set di dati presenti nella risorsa di condivisione dati di Azure primaria. Possono invitare i consumer di dati nelle condivisioni secondarie quando si configura l'ambiente di ripristino di emergenza o in un secondo momento, ad esempio come parte dei passaggi di failover manuale.

Se i consumer di dati hanno sottoscrizioni di condivisione attive in un ambiente secondario di cui è stato effettuato il provisioning per finalità di ripristino di emergenza, possono abilitare la pianificazione snapshot come parte del failover Se i consumer di dati non vogliono sottoscrivere un'area secondaria per scopi di ripristino di emergenza, possono essere invitati alla condivisione secondaria in un secondo momento. 

I consumer di dati possono avere una sottoscrizione di condivisione attiva inattiva per finalità di ripristino di emergenza oppure i provider di dati possono invitare tali abbonamenti in un secondo momento come parte delle procedure di failover manuale. 

## <a name="related-information"></a>Informazioni correlate

- [Continuità aziendale e ripristino di emergenza](../best-practices-availability-paired-regions.md)
- [Introdurre la disponibilità elevata nella strategia di continuità aziendale e ripristino di emergenza](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).