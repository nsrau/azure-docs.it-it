---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966375"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se l'archivio dati è configurato in uno dei modi seguenti, è necessario configurare un [Integration Runtime self-hosted](../articles/data-factory/create-self-hosted-integration-runtime.md) per connettersi a questo archivio dati:

- L'archivio dati si trova all'interno di una rete locale, all'interno di una rete virtuale di Azure o all'interno di un cloud privato virtuale di Amazon.
- L'archivio dati è un servizio dati cloud gestito in cui l'accesso è limitato agli indirizzi IP consentiti nelle regole del firewall.
