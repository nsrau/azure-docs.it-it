---
title: Impostazione del controllo di accesso in Azure Cosmos DB | Microsoft Docs
description: Informazioni su come impostare il controllo di accesso in Azure Cosmos DB.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038113"
---
# <a name="access-control-in-azure-cosmos-db"></a>Controllo di accesso in Azure Cosmos DB

Per aggiungere l'accesso in lettura dell'account Azure Cosmos DB al proprio account utente, è necessario che il proprietario di una sottoscrizione esegua la procedura seguente nel portale di Azure.

1. Aprire il portale di Azure e selezionare l'account Azure Cosmos DB.
2. Fare clic sulla scheda **Controllo di accesso (IAM)** e quindi su **+ Aggiungi**.
3. Nel riquadro **Aggiungi autorizzazioni**, nella casella **Ruolo**, selezionare **Ruolo Lettore dell'account Cosmos DB**.
4. Nella casella **Assegna accesso a** selezionare **Applicazione, gruppo o utente di Azure AD**.
5. Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso.  È possibile eseguire ricerche nella directory in base al nome visualizzato, all'indirizzo di posta elettronica o all'identificatore dell'oggetto.
    L'applicazione, il gruppo o l'utente selezionato viene visualizzato nell'elenco dei membri selezionati.
6. Fare clic su **Save**.

L'entità può ora leggere le risorse di Azure Cosmos DB.
