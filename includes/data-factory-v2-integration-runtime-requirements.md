---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544835"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se l'archivio dati si trova all'interno di una rete locale, di una rete virtuale di Azure o di un cloud privato virtuale di Amazon, è necessario configurare un [runtime di integrazione self-hosted](../articles/data-factory/create-self-hosted-integration-runtime.md) per la connessione.

Se l'archivio dati è un servizio dati cloud gestito, in cui l'accesso è limitato agli indirizzi IP consentiti nelle regole del firewall, è possibile usare il runtime di integrazione di Azure e aggiungere i [relativi indirizzi IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) nell'elenco Consenti. 

Per informazioni sui meccanismi di sicurezza di rete supportati da Data Factory per accedere agli archivi dati in generale, vedere [strategie di accesso ai dati](../articles/data-factory/data-access-strategies.md) .
