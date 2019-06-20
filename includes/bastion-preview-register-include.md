---
title: File di inclusione
description: File di inclusione
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d05d425cc9bfb206207801f15a25e17d60dc0aaf
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191746"
---
1. Assicurarsi di aver effettuato l'accesso all'account Azure e usare la sottoscrizione che si desidera eseguire l'onboarding per questa versione di anteprima. Per eseguire la registrazione, usare l'esempio seguente:

    ```azurepowershell-interactive
    Register-AzureRmProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Registrare di nuovo la sottoscrizione ancora una volta con il *Microsoft. Network* dello spazio dei nomi del provider.

    ```azurepowershell-interactive
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Usare il comando seguente per verificare che il *AllowBastionHost* funzionalità è registrato con la sottoscrizione:

    ```azurepowershell-interactive
    Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Potrebbero occorrere alcuni minuti per completare la registrazione.