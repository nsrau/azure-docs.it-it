---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529390"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se l'archivio dati si trova all'interno di una rete locale, di una rete virtuale di Azure o di un cloud privato virtuale di Amazon, è necessario configurare un [runtime di integrazione self-hosted](../articles/data-factory/create-self-hosted-integration-runtime.md) per connettersi.

Se l'archivio dati è un servizio dati del cloud gestito, è possibile usare Azure Integration Runtime. Se l'accesso è limitato agli indirizzi IP consentiti nelle regole del firewall, è possibile scegliere di aggiungere [IP di Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) nell'elenco elementi consentiti. 

Per altre informazioni sui meccanismi di sicurezza di rete e sulle opzioni supportate da Data Factory, vedere [strategie di accesso ai dati](../articles/data-factory/data-access-strategies.md).
