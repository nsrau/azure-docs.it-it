---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629512"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se l'archivio dati è configurato in uno dei modi seguenti, è necessario configurare un [runtime di integrazione self-hosted](../articles/data-factory/create-self-hosted-integration-runtime.md) per la connessione all'archivio dati:

- L'archivio dati si trova all'interno di una rete locale, all'interno di una rete virtuale di Azure o all'interno di un cloud privato virtuale di Amazon.
- L'archivio dati è un servizio dati cloud gestito in cui l'accesso è limitato agli indirizzi IP consentiti nelle regole del firewall.
