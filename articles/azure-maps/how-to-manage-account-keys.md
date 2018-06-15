---
title: Come gestire l'account e le chiavi dell'account Mappe di Azure | Microsoft Docs
description: È possibile usare il portale di Azure per gestire l'account Mappe di Azure e le chiavi di accesso.
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 153fb87b0e2b576fd67a24bb833165f2a1c93c56
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599661"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Come gestire l'account e le chiavi di Mappe di Azure

È possibile gestire l'account e le chiavi di Mappe di Azure tramite il portale di Azure. Dopo avere creato un account e una chiave, è possibile implementare le API nel proprio sito Web o nella propria applicazione per dispositivi mobili.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-new-account"></a>Creare un nuovo account

1. Accedere al [portale di Azure](http://portal.azure.com).

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Cercare e selezionare **Mappe**, quindi fare clic su **Crea**.

3. Immettere le informazioni del nuovo account. 

![Immettere le informazioni dell'account nel portale](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Gestire le chiavi nella pagina dell'account

Dopo la creazione di un account, si ottengono due chiavi generate in modo casuale. Queste chiavi vengono usate per l'autenticazione con le API di Mappe quando si vuole recuperare i dati delle mappe o creare una nuova istanza di mappa JavaScript. 

È possibile trovare le chiavi nel portale di Azure. Passare al proprio account e quindi selezionare **Chiavi** dal menu.

![Gestire le chiavi dell'account nel portale](./media/how-to-manage-account-keys/account-keys-portal.png)

In questa pagina è possibile copiare le chiavi o generarne di nuove. 

## <a name="delete-an-account"></a>Eliminare un account

È possibile eliminare un account dal portale di Azure. Passare alla pagina di panoramica account e selezionare **Elimina**.

![Eliminare l'account nel portale](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come usare l'[API di gestione mappe](https://docs.microsoft.com/rest/api/maps-management/accounts) per creare, aggiornare ed eliminare account di Mappe. 