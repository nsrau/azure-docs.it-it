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
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249928"
---
Un ID di risorsa Azure AD indica i destinatari per i quali è possibile usare un token emesso per fornire l'accesso a una risorsa di Azure. Nel caso di archiviazione di Azure, l'ID risorsa può essere specifico per un singolo account di archiviazione o può essere applicato a qualsiasi account di archiviazione. La tabella seguente descrive i valori che è possibile fornire per l'ID risorsa:

|ID risorsa  |Descrizione  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Endpoint del servizio per un determinato account di archiviazione. Usare questo valore per acquisire un token per l'autorizzazione delle richieste solo per l'account di archiviazione di Azure e per il servizio specifici. Sostituire il valore tra parentesi quadre con il nome dell'account di archiviazione.      |
|`https://storage.azure.com/`     | Usare per acquisire un token per autorizzare le richieste a qualsiasi account di archiviazione di Azure.        |
