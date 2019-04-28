---
title: Recuperare l'elenco di POP Verizon corrente per la rete CDN di Azure | Microsoft Docs
description: Informazioni su come recuperare l'elenco di POP Verizon corrente tramite l'API REST.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 2b34f6bcbf37a48fb49a2a64d727fc9330b0d735
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627253"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Recuperare l'elenco di POP Verizon corrente per la rete CDN di Azure

È possibile usare l'API REST per recuperare il set di indirizzi IP per i server POP (point of presence) di Verizon. Questi server POP effettuano richieste ai server di origine associati agli endpoint della rete per la distribuzione di contenuti di Azure (CDN) in un profilo Verizon (**Rete CDN Standard di Azure fornita da Verizon** o **Rete CDN Premium di Azure fornita da Verizon**). Si noti che questo set di indirizzi IP è diverso dagli indirizzi IP che un client visualizza quando si effettuano richieste ai POP. 

Per la sintassi dell'operazione dell'API REST per il recupero dell'elenco POP, vedere [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list) (Nodi perimetrali - Elenco).

## <a name="typical-use-case"></a>Caso d'uso tipico

Per motivi di sicurezza, è possibile usare questo elenco IP per fare sì che le richieste al server di origine vengano effettuate solo da un POP Verizon valido. Ad esempio, se viene individuato il nome host o l'indirizzo IP del server di origine di un endpoint di rete CDN, è possibile effettuare richieste direttamente al server di origine, ignorando le funzionalità di scalabilità e sicurezza fornite dalla rete CDN di Azure. Impostando gli indirizzi IP nell'elenco restituito come gli unici indirizzi IP consentiti in un server di origine, è possibile impedire che si verifichi questo scenario. Per assicurarsi di disporre dell'elenco POP più recente, recuperarlo almeno una volta al giorno. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'API REST, vedere [API REST della rete CDN di Azure](https://docs.microsoft.com/rest/api/cdn/).
