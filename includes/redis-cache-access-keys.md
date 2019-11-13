---
title: File di inclusione
description: File di inclusione
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720401"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Recuperare il nome host, le porte e le chiavi di accesso dal portale di Azure

Per connettersi a un'istanza della cache di Azure per Redis, i client della cache devono conoscere il nome host, le porte e una chiave per la cache. Alcuni client possono fare riferimento a questi elementi usando nomi leggermente diversi. È possibile ottenere il nome host, le porte e le chiavi dal [portale di Azure](https://portal.azure.com).

- Per ottenere le chiavi di accesso, selezionare **Chiavi di accesso** nel riquadro di spostamento di sinistra della cache. 
  
  ![Chiavi Cache Redis di Azure](media/redis-cache-access-keys/redis-cache-keys.png)

- Per ottenere il nome host e le porte, selezionare **Proprietà** nel riquadro di spostamento di sinistra della cache. Il formato del nome host è *\<Nome DNS>.redis.cache.windows.net*.

  ![Domande frequenti su Cache Redis di Azure](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

