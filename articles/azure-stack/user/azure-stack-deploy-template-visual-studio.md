---
title: Distribuire modelli in Azure Stack con Visual Studio | Documentazione Microsoft
description: Informazioni su come distribuire modelli in Azure Stack con Visual Studio.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 8fc32dc50d96d202dfc982cbdc52d8e479c3a3eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuire modelli in Azure Stack tramite Visual Studio

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare Visual Studio per distribuire i modelli di gestione risorse di Azure al kit di sviluppo dello Stack di Azure.

1. [Installazione e connessione](azure-stack-install-visual-studio.md) ad Azure Stack con Visual Studio.
2. Aprire Visual Studio.
3. Fare clic su **File**, quindi su **Nuovo** e nella finestra di dialogo **Nuovo progetto** selezionare **Gruppo di risorse di Azure**.
4. Immettere un **nome** per il nuovo progetto e quindi fare clic su **OK**.
5. Nella finestra di dialogo **Selezione del modello di Azure**, modificare l'elenco a discesa *Mostra modelli da questo percorso* in **Azure Stack Quickstart**
6. Fare clic su **101-crea--account di archiviazione**, quindi fare clic su **OK**.  
7. È possibile visualizzare un elenco dei modelli disponibili nel nuovo progetto espandendo il nodo **Modelli** nel riquadro **Esplora soluzioni**.
8. Nel riquadro **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto, selezionare **Distribuisci**, quindi fare clic su **Nuova distribuzione**.
9. Nella finestra di dialogo **Distribuzione in un gruppo di risorse** nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di Microsoft Azure Stack.
10. Scegliere un gruppo di risorse esistente dall'elenco **Gruppo di risorse** o crearne uno nuovo.
11. Scegliere un percorso dall'elenco **Località del gruppo di risorse** e quindi fare clic su **Distribuisci**.
12. Nella finestra di dialogo **Modifica parametri** immettere i valori per i parametri (che variano in base al modello), quindi fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire modelli con la riga di comando](azure-stack-deploy-template-command-line.md)

[Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)

