---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68249928"
---
Un ID della risorsa di Azure AD indica i destinatari per i quali è possibile usare un token emesso per consentire l'accesso a una risorsa di Azure. Nel caso di Archiviazione di Azure, l'ID della risorsa può essere specifico di un singolo account di archiviazione o essere valido per qualsiasi account di archiviazione. La tabella seguente descrive i valori che è possibile specificare per l'ID della risorsa:

|ID risorsa  |Description  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Endpoint servizio per un determinato account di archiviazione. Usare questo valore per acquisire un token per autorizzare le richieste solo all'account di archiviazione di Azure e al servizio specifici. Sostituire il valore racchiuso tra parentesi quadre con il nome del proprio account di archiviazione.      |
|`https://storage.azure.com/`     | Usare questo valore per acquisire un token per autorizzare le richieste a qualsiasi account di archiviazione di Azure.        |
