---
title: Peering Internet - Domande frequenti
titleSuffix: Azure
description: Peering Internet - Domande frequenti
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775498"
---
# <a name="internet-peering---faqs"></a>Peering Internet - Domande frequenti

È possibile rivedere le informazioni riportate di seguito per domande generali.

**Qual è la differenza tra il peering Internet e il servizio peering?**

Peering Service è un servizio che intende fornire connettività IP pubblica di livello aziendale a Microsoft per i clienti aziendali. Internet di livello enterprise include la connettività tramite ISP con connettività a Microsoft ad alta velocità effettiva e ridondanza per una connettività A utenti HA. Inoltre, il traffico utente è ottimizzato per la latenza al Microsoft Edge più vicino. Il servizio di peering si basa sulla connettività peering con il gestore partner. La connettività peering con il partner deve essere peering diretto anziché peering di Exchange. Il peering diretto deve avere ridondanza locale e geografica.

**Che cos'è il peering legacy?**

La connessione di peering configurata tramite Azure PowerShell viene gestita come risorsa di Azure.Peering connection set up using Azure PowerShell is managed as an Azure resource. Le connessioni di peering configurate in passato vengono archiviate nel sistema come peering legacy che è possibile scegliere di convertire per gestire come risorsa di Azure.Peering connections set up in the past are stored in our system as legacy peering which you may choose to convert to manage as an Azure resource.

**Quando viene chiamato New-AzPeeringDirectConnectionObject, quali indirizzi IP vengono assegnati ai dispositivi Microsoft e Peer?**

Quando si chiama il cmdlet New-AzPeeringDirectConnectionObject, viene immesso un indirizzo /31 (a.b.c.d/31) o un indirizzo /30 (a.b.c.d/30). Il primo indirizzo IP (a.b.c.d.0) viene assegnato al dispositivo di Peer e il secondo indirizzo IP (a.b.c.d.1) viene assegnato al dispositivo Microsoft.

**Che cos'è MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 nel cmdlet New-AzPeeringDirectConnectionObject?**

MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 rappresentano il numero massimo di prefissi IPv4 e IPv6 che un peer accetta. Questi parametri possono essere modificati in qualsiasi momento.