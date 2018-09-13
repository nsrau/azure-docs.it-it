---
title: Distribuire modelli usando il portale di Azure Stack | Microsoft Docs
description: Informazioni su come usare il portale di Azure Stack per distribuire modelli.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: b2f4fbca2dcbdd537302746c7b6109e467ea31d7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720226"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Distribuire modelli tramite il portale di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare il portale per distribuire modelli di Azure Resource Manager di Azure Stack.

## <a name="to-deploy-a-template"></a>Distribuire un modello

1. Accedere al portale, selezionare **+ crea una risorsa**, quindi selezionare **Custom**.
2. Selezionare **distribuzione modello**.
3. Selezionare **modifica modello**e quindi incollare il codice JSON del modello nella finestra del codice. Selezionare **Salva**.
4. Selezionare **Upravit parametry**, specificare i valori per i parametri che vengono visualizzati e quindi selezionare **OK**.
5. Selezionare **sottoscrizione**. Scegliere la sottoscrizione da usare e quindi selezionare **OK**.
6. Selezionare **gruppo di risorse**. Scegliere un gruppo di risorse esistente o crearne uno nuovo e quindi selezionare **OK**.
7. Selezionare **Create**. Un nuovo riquadro nel dashboard terrà traccia dello stato di avanzamento della distribuzione del modello.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
