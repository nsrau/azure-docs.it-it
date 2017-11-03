---
title: Scaricare gli elementi di marketplace da Azure | Documenti Microsoft
description: "È possibile scaricare gli elementi di marketplace da Azure a una distribuzione Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Scaricare gli elementi di marketplace da Azure allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come si decide il contenuto da includere nel marketplace dello Stack di Azure, è necessario considerare il contenuto disponibile da Azure marketplace. È possibile scaricare da un elenco di elementi di Azure marketplace che sono stati pre-verificate per l'esecuzione in Azure Stack curato. Spesso vengono aggiunti nuovi elementi a questo elenco, quindi verificare che il nuovo contenuto.

Per scaricare elementi del marketplace, è innanzitutto necessario [registro dello Stack di Azure con Azure](azure-stack-register.md). 

## <a name="download"></a>Scaricare
1. Accedere al portale di amministrazione di Azure Stack (https://portal.local.azurestack.external).
2. Alcuni elementi del marketplace possono essere molto grandi.  Verificare che si dispone di spazio sufficiente nel sistema, fare clic su **i provider di risorse** > **archiviazione**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Fare clic su **più servizi** > **gestione Marketplace**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Fare clic su **Aggiungi da Azure** per visualizzare un elenco di elementi disponibili per il download. È possibile fare clic su ogni elemento nell'elenco per visualizzarne la descrizione e dimensioni del download.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Selezionare l'elemento desiderato nell'elenco e quindi fare clic su **scaricare**. Verrà avviato il download dell'immagine di macchina virtuale per l'elemento selezionato. Tempi di download variano.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Al termine del download, è possibile distribuire il nuovo elemento marketplace come utente o un operatore di Stack di Azure. Fare clic su **+ nuovo**, eseguire la ricerca tra le categorie per il nuovo elemento marketplace e quindi selezionare l'elemento.
7. Fare clic su **crea** per aprire l'esperienza di creazione per l'elemento appena scaricato. Seguire le istruzioni dettagliate per distribuire l'elemento.

## <a name="next-steps"></a>Passaggi successivi

[Creare e pubblicare un elemento di Marketplace](azure-stack-create-and-publish-marketplace-item.md)
