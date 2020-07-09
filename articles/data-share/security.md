---
title: Panoramica della sicurezza per la condivisione di dati di Azure
description: Panoramica della sicurezza per la condivisione di dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 10f31b74b461941b15f13e45f90b5fbc408c90fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108414"
---
# <a name="security-overview-for-azure-data-share"></a>Panoramica della sicurezza per la condivisione di dati di Azure

Questo articolo fornisce una panoramica della sicurezza del servizio Condivisione dati di Azure.

## <a name="security-overview"></a>Panoramica della sicurezza

Condivisione dati di Azure sfrutta le funzionalità di sicurezza sottostanti offerte da Azure per proteggere i dati inattivi e in movimento. I dati inattivi vengono crittografati, purché la funzionalità sia supportata dall'archivio dati sottostante. Anche i dati in movimento vengono crittografati, così come i metadati su una condivisione dati quando sono inattivi e in movimento. 

I controlli dell'accesso possono essere configurati a livello di risorsa di Condivisione dati di Azure per assicurare che siano accessibili solo da persone autorizzate. 

La condivisione di dati di Azure sfrutta l'identità gestita (precedentemente nota come MSI) per accedere agli archivi dati usati per la condivisione dei dati. Non viene effettuato alcuno scambio di credenziali tra un provider di dati e un consumer. Per altre informazioni sull'identità gestita, vedere [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Per ulteriori informazioni sui ruoli e sulle autorizzazioni necessarie per condividere i dati, fare riferimento a [ruoli e requisiti](concepts-roles-permissions.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).




