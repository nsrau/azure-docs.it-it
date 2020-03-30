---
title: Ripristino di emergenza per la condivisione dati di AzureDisaster recovery for Azure Data Share
description: Ripristino di emergenza per la condivisione dati di AzureDisaster recovery for Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483180"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Ripristino di emergenza per la condivisione dati di AzureDisaster recovery for Azure Data Share

In this article, we'll walk through how to configure a disaster recovery environment for Azure Data Share. Le interruzioni del data center di Azure sono rare, ma possono durare da pochi minuti a ore. Le interruzioni del data center possono causare interruzioni agli ambienti che si basano su dati condivisi dal provider di dati. Seguendo i passaggi descritti in questo articolo, i provider di dati possono continuare a condividere i dati con i consumer di dati in caso di interruzione del data center per l'area primaria che ospita la condivisione dati. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Raggiungere la continuità aziendale per la condivisione dati di AzureAchieving business continuity for Azure Data Share

Per essere preparati per un'interruzione del data center, il provider di dati può avere un ambiente di condivisione dati di cui è stato eseguito il provisioning in un'area secondaria. È possibile adottare misure che garantiscano un failover senza problemi nel caso in cui si verifichi un'interruzione del data center. 

I provider di dati possono eseguire il provisioning delle risorse secondarie di Condivisione dati di Azure in un'area aggiuntiva. Queste risorse di condivisione dati possono essere configurate per includere set di dati presenti nell'ambiente di condivisione dati primario. I consumer di dati possono essere aggiunti alla condivisione dati durante la configurazione dell'ambiente di ripristino di emergenza o aggiunti in un secondo momento (ad es. come parte dei passaggi di failover manuale).

Se i consumer di dati dispongono di una sottoscrizione di condivisione attiva in un ambiente secondario di cui è stato eseguito il provisioning per scopi di ripristino di emergenza, possono abilitare la pianificazione dello snapshot come parte di un failover. Se i dati in cui gli utenti non desiderano sottoscrivere un'area secondaria ai fini del ripristino di emergenza, possono essere invitati nella condivisione dati secondaria in un secondo momento. 

Gli consumer di dati possono avere una sottoscrizione di condivisione attiva inattiva per scopi di ripristino di emergenza oppure i provider di dati possono aggiungerli in un secondo momento come parte delle procedure di failover manuale. 

## <a name="related-information"></a>Informazioni correlate

- [Continuità aziendale e ripristino di emergenza](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Introdurre la disponibilità elevata nella strategia di continuità aziendale e ripristino di emergenza](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).




