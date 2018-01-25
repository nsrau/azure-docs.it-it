---
title: Ridistribuire Stack Azure | Documenti Microsoft
description: Ridistribuire Azure dello Stack.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Ridistribuire Azure Stack
Se si riceve un errore durante la distribuzione dello Stack di Azure, è possibile eseguire nuovamente l'installazione tramite il comando PowerShell seguente: `.\InstallAzureStackpoc.ps1 -rerun`. Questo comando riavvia il programma di installazione di Azure Stack in corrispondenza del punto che non è riuscito in precedenza senza la necessità di ricominciare dall'inizio. Se viene visualizzato nuovamente lo stesso errore di installazione, potrebbe essere necessario eseguire una ridistribuzione completa per indirizzare il problema. 

Per ridistribuire dello Stack di Azure, è necessario ricominciare da zero come descritto di seguito.

## <a name="steps-to-redeploy-azure-stack"></a>Passaggi necessari per ridistribuire Azure Stack
1. Nell'host del kit di sviluppo, aprire una console di PowerShell con privilegi elevata > passare allo script asdk installer.ps1 > eseguirlo > fare clic su **riavviare**.
2. Selezionare il sistema operativo di base (non **Azure Stack**) e fare clic su **Avanti**.
3. Dopo il riavvio dell'host di kit di sviluppo, eliminare il file CloudBuilder.vhdx che è stato utilizzato come parte della distribuzione precedente.
4. [Distribuire il kit di sviluppo](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Passaggi successivi
[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)

