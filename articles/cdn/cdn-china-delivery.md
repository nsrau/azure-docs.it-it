---
title: Distribuzione dei contenuti con rete CDN di Azure per la Cina | Microsoft Docs
description: Informazioni sull'uso della rete CDN di Azure per distribuire contenuti agli utenti cinesi.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 59788f301bb501103babd55a2ac37102932f4dcf
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259690"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Distribuzione dei contenuti con rete CDN di Azure per la Cina

La rete per la distribuzione di contenuti (CDN) globale di Azure può distribuire contenuti agli utenti cinesi mediante località POP (Point of Presence) vicine alla Cina o POP che offrano le prestazioni migliori per le richieste provenienti dalla Cina. Tuttavia, se la Cina rappresenta un mercato significativo per i clienti che quindi necessitano di prestazioni elevate, l'uso di una rete CDN di Azure per la Cina costituisce un'alternativa migliore.

La rete CDN di Azure per la Cina è diversa dalla rete CDN di Azure globale in quanto recapita i contenuti da POP situati in Cina grazie alla collaborazione con numerosi provider locali. Viste le normative e le direttive di conformità cinesi, è necessario registrare una sottoscrizione separata per usare la rete CDN di Azure per la Cina e i siti Web devono avere una licenza ICP. L'uso del portale e delle API per abilitare e gestire la distribuzione dei contenuti è uguale per le due reti CDN di Azure.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Confronto tra la rete CDN di Azure globale e quella per la Cina

La rete CDN di Azure globale e la rete CDN di Azure per la Cina hanno le caratteristiche seguenti:

- Rete CDN di Azure globale:

     - Portale: https://portal.azure.com  

     - Esegue la distribuzione dei contenuti fuori dalla Cina

     - Quattro piani tariffari: Microsoft standard, Verizon standard, Verizon premium e Akamai standard

     - [Documentazione](https://docs.microsoft.com/en-us/azure/cdn/)

- Rete CDN di Azure per la Cina:

     - Portale: https://portal.azure.cn

     - Esegue la distribuzione dei contenuti in Cina

     - Due piani tariffari: Standard e Premium

     - [Documentazione](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete CDN di Azure per la Cina, vedere:

- [Content Delivery Network features](https://www.azure.cn/en-us/home/features/cdn/) (Funzionalità della rete CDN)

- [Overview of Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview) (Panoramica della rete CDN di Azure)

- [Use the Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use) (Usare la rete CDN di Azure)

- [Azure service availability in China](https://docs.microsoft.com/en-us/azure/china/china-get-started-service-availability) (Disponibilità dei servizi Azure in Cina)



