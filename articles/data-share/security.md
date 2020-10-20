---
title: Panoramica della sicurezza per Condivisione dati di Azure
description: Panoramica della sicurezza per Condivisione dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216556"
---
# <a name="security-overview-for-azure-data-share"></a>Panoramica della sicurezza per Condivisione dati di Azure

Questo articolo fornisce una panoramica della sicurezza del servizio Condivisione dati di Azure.

## <a name="security-overview"></a>Panoramica della sicurezza

Condivisione dati di Azure sfrutta le funzionalità di sicurezza sottostanti offerte da Azure per proteggere i dati inattivi e in movimento. I dati inattivi vengono crittografati, purché la funzionalità sia supportata dall'archivio dati sottostante. Anche i dati in movimento vengono crittografati, così come i metadati su una condivisione dati quando sono inattivi e in movimento. 

I controlli dell'accesso possono essere configurati a livello di risorsa di Condivisione dati di Azure per assicurare che siano accessibili solo da persone autorizzate. 

La condivisione di dati di Azure sfrutta l'identità gestita (precedentemente nota come MSI) per accedere agli archivi dati usati per la condivisione dei dati. Non viene effettuato alcuno scambio di credenziali tra un provider di dati e un consumer. Per altre informazioni sull'identità gestita, vedere [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Per ulteriori informazioni sui ruoli e sulle autorizzazioni necessarie per condividere i dati, fare riferimento a [ruoli e requisiti](concepts-roles-permissions.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).