---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249928"
---
Un ID risorsa di Azure AD indica il gruppo di destinatari per il quale è possibile usare un token rilasciato per fornire l'accesso a una risorsa di Azure.An Azure AD resource ID indicates the audience for which a token that is issued can be used to provide access to an Azure resource. Nel caso di Archiviazione di Azure, l'ID risorsa può essere specifico di un singolo account di archiviazione oppure può essere applicato a qualsiasi account di archiviazione. Nella tabella seguente vengono descritti i valori che è possibile specificare per l'ID risorsa:

|ID risorsa  |Descrizione  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Endpoint del servizio per un determinato account di archiviazione. Usare questo valore per acquisire un token per l'autorizzazione delle richieste solo all'account e al servizio di archiviazione di Azure specifici. Sostituire il valore tra parentesi quadre con il nome dell'account di archiviazione.      |
|`https://storage.azure.com/`     | Usare per acquisire un token per l'autorizzazione delle richieste a qualsiasi account di archiviazione di Azure.Use to acquire a token for authorizing requests to any Azure Storage account.        |
