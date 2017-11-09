---
title: Ridistribuire Stack Azure | Documenti Microsoft
description: Ridistribuire Azure dello Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Ridistribuire Azure Stack
Per ridistribuire dello Stack di Azure, è necessario ricominciare da zero come descritto di seguito.

## <a name="steps-to-redeploy-azure-stack"></a>Passaggi necessari per ridistribuire Azure Stack
1. Nell'host del kit di sviluppo, aprire una console di PowerShell con privilegi elevata > passare allo script asdk installer.ps1 > eseguirlo > fare clic su **riavviare**.
2. Selezionare il sistema operativo di base (non **Azure Stack**) e fare clic su **Avanti**.
3. Dopo il riavvio dell'host di kit di sviluppo, eliminare il file CloudBuilder.vhdx che è stato utilizzato come parte della distribuzione precedente.
4. [Distribuire il kit di sviluppo](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Passaggi successivi
[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)

