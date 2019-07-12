---
title: Ruoli e i requisiti per l'anteprima di Azure Data condivisione
description: Ruoli e i requisiti per l'anteprima di Azure Data condivisione
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807532"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Ruoli e i requisiti per l'anteprima di Azure Data condivisione

Questo articolo descrive i ruoli richiesti per condividere dati con anteprima di condividere i dati in Azure, nonché per accettare e ricevere dati tramite l'anteprima di condividere i dati in Azure. 

## <a name="roles-and-requirements"></a>Ruoli e requisiti

Per condividere o ricevere dati tramite Azure i dati nella condivisione, l'account utente utilizzato per accedere ad Azure deve essere in grado di concedere le autorizzazioni di condivisione dei dati nell'account di archiviazione di condivisione dei dati da o la ricezione di dati in un. In genere si tratta di un'autorizzazione che esiste nel **proprietario** ruolo o un ruolo personalizzato con assegnata l'autorizzazione Microsoft.Authorization/role assegnazioni/scrittura. 

Per condividere o ricevere dati da o a un account di archiviazione di Azure, è necessario essere un proprietario dell'account di archiviazione. Anche se si have creato l'account di archiviazione, questo non concede automaticamente è la proprietà dell'account di archiviazione. Per aggiungere se stessi in per il ruolo di proprietario dell'account di archiviazione di Azure, seguire questa procedura.

1. Passare all'account di archiviazione nel portale di Azure
1. Selezionare **controllo di accesso (IAM)**
1. Fare clic su **Aggiungi**.
1. Aggiungersi come proprietario

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, nel portale di Azure selezionare il nome utente nell'angolo in alto a destra e quindi selezionare **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. 

## <a name="resource-provider-registration"></a>Registrazione del Provider di risorse 

Quando si accettano un invito alla condivisione di dati di Azure, è necessario registrare manualmente il provider di risorse Microsoft.DataShare nella sottoscrizione. Seguire questi passaggi per registrare il provider di risorse Microsoft.DataShare nella sottoscrizione di Azure. 

1. Nel portale di Azure, passare a **sottoscrizioni**
1. Selezionare la sottoscrizione che sta usando per la condivisione dei dati di Azure
1. Fare clic su **i provider di risorse**
1. Cercare Microsoft.DataShare
1. Fare clic su **Registra**

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui ruoli in Azure - [Comprendere le definizioni dei ruoli](../role-based-access-control/role-definitions.md)

