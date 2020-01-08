---
title: Ripristino di emergenza per la condivisione di dati di Azure
description: Ripristino di emergenza per la condivisione di dati di Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483180"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Ripristino di emergenza per la condivisione di dati di Azure

Questo articolo illustra come configurare un ambiente di ripristino di emergenza per la condivisione di dati di Azure. Le interruzioni del data center di Azure sono rare, ma possono durare da pochi minuti a ore. Le interruzioni del Data Center possono causare interruzioni negli ambienti che si basano sui dati condivisi dal provider di dati. Seguendo i passaggi descritti in questo articolo, i provider di dati possono continuare a condividere i dati con i consumer di dati in caso di interruzione del data center per l'area primaria che ospita la condivisione di dati. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Raggiungimento della continuità aziendale per la condivisione di dati di Azure

Per prepararsi a un'interruzione del data center, il provider di dati può avere un ambiente di condivisione dati di cui è stato effettuato il provisioning in un'area secondaria. È possibile adottare misure che garantiranno un failover uniforme nel caso in cui si verifichi un'interruzione del data center. 

I provider di dati possono eseguire il provisioning di risorse di condivisione dati di Azure secondarie in un'area Queste risorse di condivisione dati possono essere configurate in modo da includere i set di dati presenti nell'ambiente di condivisione dati primario. I consumer di dati possono essere aggiunti alla condivisione dati quando si configura l'ambiente di ripristino di emergenza o aggiunti in un momento successivo (ad esempio come parte dei passaggi di failover manuale.

Se i consumer di dati hanno una sottoscrizione di condivisione attiva in un ambiente secondario di cui è stato effettuato il provisioning per finalità di ripristino di emergenza, possono abilitare la pianificazione dello snapshot come parte di un failover. Se i consumer di dati non vogliono sottoscrivere un'area secondaria per scopi di ripristino di emergenza, possono essere invitati nella condivisione di dati secondaria in un momento successivo. 

I consumer di dati possono avere una sottoscrizione di condivisione attiva inattiva per finalità di ripristino di emergenza oppure i provider di dati possono aggiungerli in un momento successivo come parte delle procedure di failover manuale. 

## <a name="related-information"></a>Informazioni correlate

- [Continuità aziendale e ripristino di emergenza](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Crea una disponibilità elevata nella tua strategia di BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).




