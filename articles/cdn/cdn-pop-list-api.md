---
title: Recuperare l'elenco di indirizzi IP POP corrente per la rete CDN di Azure Documenti Microsoft
description: Informazioni su come recuperare l'elenco POP corrente.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299245"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperare l'elenco IP POP corrente per la rete CDN di AzureRetrieve the current POP IP list for Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperare l'elenco IP POP Verizon corrente per la rete CDN di AzureRetrieve the current Verizon POP IP list for Azure CDN

È possibile usare l'API REST per recuperare il set di indirizzi IP per i server POP (point of presence) di Verizon. Questi server POP effettuano richieste ai server di origine associati agli endpoint della rete per la distribuzione di contenuti di Azure (CDN) in un profilo Verizon (**Rete CDN Standard di Azure fornita da Verizon** o **Rete CDN Premium di Azure fornita da Verizon**). Si noti che questo set di indirizzi IP è diverso dagli indirizzi IP che un client visualizza quando si effettuano richieste ai POP. 

Per la sintassi dell'operazione dell'API REST per il recupero dell'elenco POP, vedere [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list) (Nodi perimetrali - Elenco).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperare l'elenco IP Pop Microsoft corrente per la rete CDN di AzureRetrieve the current Microsoft POP IP list for Azure CDN

Per bloccare l'applicazione in modo che accetti traffico solo dalla rete CDN di Azure da Microsoft, è necessario configurare gli ACL IP per il back-end. È inoltre possibile limitare il set di valori accettati per l'intestazione 'X-Forwarded-Host' inviata dalla rete CDN di Azure da Microsoft.You may also restrict the set of accepted values for the header 'X-Forwarded-Host' sent by Azure CDN from Microsoft. Questi passaggi sono descritti in dettaglio come di seguito:These steps are detailed out as below:

Configurare l'aclling IP affinché i back-end accettino il traffico dalla rete CDN di Azure dallo spazio di indirizzi IP back-end di Microsoft e solo dai servizi di infrastruttura di Azure.Configure IP ACLing for your backends to accept traffic from Azure CDN from Microsoft's back-end IP address space and Azure's infrastructure services only. 

* Rete CDN di Azure dallo spazio IP back-end IPv4 di Microsoft: 147.243.0.0/16Azure CDN from Microsoft's IPv4 backend IP space: 147.243.0.0/16
* Rete CDN di Azure dallo spazio IP back-end IPv6 di Microsoft: 2a01:111:2050::/44Azure CDN from Microsoft's IPv6 backend IP space: 2a01:111:2050::/44

Gli intervalli IP e i tag di servizio per i servizi Microsoft sono disponibili [qui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso d'uso tipico

Per motivi di sicurezza, è possibile usare questo elenco IP per fare sì che le richieste al server di origine vengano effettuate solo da un POP Verizon valido. Ad esempio, se viene individuato il nome host o l'indirizzo IP del server di origine di un endpoint di rete CDN, è possibile effettuare richieste direttamente al server di origine, ignorando le funzionalità di scalabilità e sicurezza fornite dalla rete CDN di Azure. Impostando gli indirizzi IP nell'elenco restituito come gli unici indirizzi IP consentiti in un server di origine, è possibile impedire che si verifichi questo scenario. Per assicurarsi di disporre dell'elenco POP più recente, recuperarlo almeno una volta al giorno. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'API REST, vedere [API REST della rete CDN di Azure](https://docs.microsoft.com/rest/api/cdn/).
