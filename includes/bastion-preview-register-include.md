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
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305294"
---
1. Assicurarsi di aver effettuato l'accesso all'account Azure e usare la sottoscrizione che si desidera eseguire l'onboarding per questa versione di anteprima. Per eseguire la registrazione, usare l'esempio seguente:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Registrare di nuovo la sottoscrizione ancora una volta con il *Microsoft. Network* dello spazio dei nomi del provider.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Usare il comando seguente per verificare che il *AllowBastionHost* funzionalità è registrato con la sottoscrizione:

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Potrebbero occorrere alcuni minuti per completare la registrazione.
