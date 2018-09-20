---
title: Distribuire modelli in Azure Stack con Visual Studio | Documentazione Microsoft
description: Informazioni su come distribuire modelli in Azure Stack con Visual Studio.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 1d61313018b0cd16594772676172737a7d8fc2cd
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366694"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuire modelli in Azure Stack tramite Visual Studio

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare Visual Studio per distribuire modelli di Azure Resource Manager di Azure Stack.

## <a name="to-deploy-a-template"></a>Distribuire un modello

1. [Installazione e connessione](azure-stack-install-visual-studio.md) ad Azure Stack con Visual Studio.
2. Aprire Visual Studio.
3. Selezionare **File**, quindi selezionare **New**. Nelle **nuovo progetto**, selezionare **gruppo di risorse di Azure**.
4. Immettere un **Name** per il nuovo progetto e quindi selezionare **OK**.
5. Nelle **Seleziona modello di Azure**, selezionare **Azure Stack Quickstart** nell'elenco a discesa.
6. Selezionare **101-create-storage-account**, quindi selezionare **OK**.
7. Nel nuovo progetto, espandere la **modelli** nodo **Esplora soluzioni** per visualizzare i modelli disponibili.
8. Nelle **Esplora soluzioni**, selezionare il nome del progetto e quindi selezionare **Distribuisci**. Selezionare **nuova distribuzione**.
9. Nelle **Distribuisci in gruppo di risorse**, utilizzare il **sottoscrizione** elenco a discesa per selezionare la sottoscrizione di Microsoft Azure Stack.
10. Dal **gruppo di risorse** elenco, scegliere un gruppo di risorse esistente o crearne uno nuovo.
11. Dal **località del gruppo di risorse** elenco, scegliere un percorso e quindi selezionare **Distribuisci**.
12. Nella **modifica parametri**, specificare i valori per i parametri (che variano in base al modello), quindi selezionare **salvare**.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con la riga di comando](azure-stack-deploy-template-command-line.md)
* [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
