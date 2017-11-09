---
title: Distribuire i modelli con il portale di Azure Stack | Documenti Microsoft
description: Informazioni su come usare il portale di Azure Stack per distribuire modelli.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Distribuire modelli tramite il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare il portale per distribuire modelli di gestione risorse di Azure il kit di sviluppo dello Stack di Azure.

Modelli di gestione risorse distribuiscono ed eseguire il provisioning di tutte le risorse per l'applicazione in un'operazione singola, coordinata.

1. Accedere al portale, fare clic su **New**, fare clic su **personalizzato**, quindi fare clic su **distribuzione modello**.
2. Fare clic su **modifica modelli**, quindi incollare il codice del modello JSON il pannello e quindi fare clic su **salvare**.
3. Fare clic su **modificare parametri**, digitare i valori per i parametri elencati e quindi fare clic su **OK**.
4. Fare clic su **sottoscrizione**, scegliere la sottoscrizione a cui si desidera utilizzare e quindi fare clic su **OK**.
5. Fare clic su **gruppo di risorse**, scegliere un gruppo di risorse esistente o crearne uno nuovo e quindi fare clic su **OK**.
6. Fare clic su **Crea**. Un nuovo riquadro nel dashboard terrà traccia dello stato di avanzamento della distribuzione del modello.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)

