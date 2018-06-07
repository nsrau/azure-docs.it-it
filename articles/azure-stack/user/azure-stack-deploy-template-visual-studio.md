---
title: Distribuire modelli in Azure Stack con Visual Studio | Documentazione Microsoft
description: Informazioni su come distribuire modelli in Azure Stack con Visual Studio.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 6cd722fedc0483e37ce6ee491d74a7c985111353
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605124"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuire modelli in Azure Stack tramite Visual Studio

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile utilizzare Visual Studio per distribuire modelli di Azure Resource Manager allo Stack di Azure.

## <a name="to-deploy-a-template"></a>Per distribuire un modello

1. [Installazione e connessione](azure-stack-install-visual-studio.md) ad Azure Stack con Visual Studio.
2. Aprire Visual Studio.
3. Selezionare **File**, quindi selezionare **New**. In **nuovo progetto**, selezionare **gruppo di risorse di Azure**.
4. Immettere un **nome** per il nuovo progetto e quindi selezionare **OK**.
5. In **Seleziona modello di Azure**, selezionare **Guida introduttiva di Azure Stack** dall'elenco a discesa.
6. Selezionare **101-crea--account di archiviazione**, quindi selezionare **OK**.
7. Nel nuovo progetto, espandere la **modelli** nodo **Esplora** per visualizzare i modelli disponibili.
8. In **Esplora soluzioni**, selezionare il nome del progetto e quindi selezionare **Distribuisci**. Selezionare **nuova distribuzione**.
9. In **Distribuisci a gruppo di risorse**, utilizzare il **sottoscrizione** elenco a discesa per selezionare la sottoscrizione di Microsoft Azure Stack.
10. Dal **gruppo di risorse** elenco, scegliere un gruppo di risorse esistente o crearne uno nuovo.
11. Dal **percorso del gruppo di risorse** elenco, scegliere un percorso e quindi selezionare **Distribuisci**.
12. In **modifica parametri**, specificare i valori per i parametri (che variano da modello), quindi selezionare **salvare**.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con la riga di comando](azure-stack-deploy-template-command-line.md)
* [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
