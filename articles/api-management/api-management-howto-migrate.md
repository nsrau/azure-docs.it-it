---
title: Come eseguire la migrazione di Gestione API di Azure tra areeHow to migrate Azure API Management across regions
description: Informazioni su come eseguire la migrazione di un'istanza di Gestione API da un'area a un'altra.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073472"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Come eseguire la migrazione di Gestione API di Azure tra areeHow to migrate Azure API Management across regions
Per eseguire la migrazione delle istanze di Gestione API da un'area di Azure a un'altra, è possibile usare la funzionalità di [backup e ripristino.](api-management-howto-disaster-recovery-backup-restore.md) È consigliabile scegliere lo stesso piano tariffario Gestione API nelle aree di origine e di destinazione. 

> [!NOTE]
> Il backup e il ripristino non funzioneranno durante la migrazione tra diversi tipi di cloud. A tale riguardo, è necessario esportare la risorsa [come modello](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Quindi, adattare il modello esportato per l'area di Azure di destinazione e ricreare la risorsa. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opzione 1: Usare un nome di istanza di Gestione API diversoOption 1: Use a different API Management instance name

1. Nell'area di destinazione creare una nuova istanza di Gestione API con lo stesso piano tariffario dell'istanza di Gestione API di origine. La nuova istanza deve avere un nome diverso. 
1. Eseguire il backup dell'istanza di Gestione API esistente in un account di archiviazione.
1. Ripristinare il backup creato nel passaggio 2 nella nuova istanza di Gestione API creata nella nuova area nel passaggio 1.Restore the backup created in Step 2 to the new API Management instance created in the new region in Step 1.
1. Se si dispone di un dominio personalizzato che punta all'istanza di Gestione API dell'area di origine, aggiornare il cNAME del dominio personalizzato in modo che punti alla nuova istanza di Gestione API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opzione 2: Usare lo stesso nome di istanza di Gestione APIOption 2: Use the same API Management instance name

> [!NOTE]
> Questa opzione comporterà tempi di inattività durante la migrazione.

1. Eseguire il backup dell'istanza di Gestione API nell'area di origine in un account di archiviazione.
1. Eliminare Gestione API nell'area di origine. 
1. Creare una nuova istanza di Gestione API nell'area di destinazione con lo stesso nome di quella nell'area di origine.
1. Ripristinare il backup creato nel passaggio 1 nella nuova istanza di Gestione API nell'area di destinazione.  


## <a name="next-steps"></a><a name="next-steps"> </a>Passaggi successivi
* Per ulteriori informazioni sulla funzionalità di backup e ripristino, vedere come implementare il [ripristino di emergenza.](api-management-howto-disaster-recovery-backup-restore.md)
* Per informazioni sulla migrazione delle risorse di Azure, vedere Indicazioni sulla migrazione tra aree di Azure.For information on migration Azure resources, see [Azure cross-region migration guidance.](https://github.com/Azure/Azure-Migration-Guidance)