---
title: Recuperare l'elenco di indirizzi IP POP correnti per la rete CDN di Azure | Microsoft Docs
description: Informazioni su come recuperare l'elenco POP corrente.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 7b98bb446fc400007e4061d09db8084960702943
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668288"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperare l'elenco di indirizzi IP POP correnti per la rete CDN di Azure

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperare l'elenco di indirizzi IP POP Verizon corrente per la rete CDN di Azure

È possibile usare l'API REST per recuperare il set di indirizzi IP per i server POP (point of presence) di Verizon. Questi server POP effettuano richieste ai server di origine associati agli endpoint della rete per la distribuzione di contenuti di Azure (CDN) in un profilo Verizon (**Rete CDN Standard di Azure fornita da Verizon** o **Rete CDN Premium di Azure fornita da Verizon**). Si noti che questo set di indirizzi IP è diverso dagli indirizzi IP che un client visualizza quando si effettuano richieste ai POP. 

Per la sintassi dell'operazione dell'API REST per il recupero dell'elenco POP, vedere [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list) (Nodi perimetrali - Elenco).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperare l'elenco Microsoft POP IP corrente per la rete CDN di Azure

Per bloccare l'applicazione in modo che accetti il traffico solo dalla rete CDN di Azure di Microsoft, è necessario configurare gli ACL IP per il back-end. È anche possibile limitare il set di valori accettati per l'intestazione ' X-Inoltred-host ' inviata dalla rete CDN di Azure da Microsoft. Questi passaggi sono descritti di seguito:

Configurare ACLing IP per i back-end in modo da accettare il traffico dalla rete CDN di Azure dallo spazio degli indirizzi IP back-end di Microsoft e dai servizi di infrastruttura di Azure. 

* Rete CDN di Azure dello spazio IP back-end IPv4 di Microsoft: 147.243.0.0/16
* Rete CDN di Azure dello spazio IP back-end IPv6 di Microsoft: 2a01:111:2050::/44

Per usare i tag di servizio con la rete CDN di Azure di Microsoft, usare il tag front door di Azure. Gli intervalli IP e i tag di servizio per i servizi Microsoft sono disponibili [qui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso d'uso tipico

Per motivi di sicurezza, è possibile usare questo elenco IP per fare sì che le richieste al server di origine vengano effettuate solo da un POP Verizon valido. Ad esempio, se viene individuato il nome host o l'indirizzo IP del server di origine di un endpoint di rete CDN, è possibile effettuare richieste direttamente al server di origine, ignorando le funzionalità di scalabilità e sicurezza fornite dalla rete CDN di Azure. Impostando gli indirizzi IP nell'elenco restituito come gli unici indirizzi IP consentiti in un server di origine, è possibile impedire che si verifichi questo scenario. Per assicurarsi di disporre dell'elenco POP più recente, recuperarlo almeno una volta al giorno. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'API REST, vedere [API REST della rete CDN di Azure](https://docs.microsoft.com/rest/api/cdn/).
