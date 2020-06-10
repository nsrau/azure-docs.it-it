---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966375"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se l'archivio dati è configurato in uno dei modi seguenti, è necessario configurare un [runtime di integrazione self-hosted](../articles/data-factory/create-self-hosted-integration-runtime.md) per connettersi a questo archivio dati:

- L'archivio dati si trova all'interno di una rete locale, di Rete virtuale di Azure o di Amazon Virtual Private Cloud.
- L'archivio dati è un servizio dati cloud gestito in cui l'accesso è limitato agli indirizzi IP inclusi nell'elenco elementi consentiti nelle regole del firewall.
