---
title: Peering Internet-domande frequenti
titleSuffix: Azure
description: Peering Internet-domande frequenti
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775498"
---
# <a name="internet-peering---faqs"></a>Peering Internet-domande frequenti

Per domande generali, è possibile consultare le informazioni riportate di seguito.

**Qual è la differenza tra il peering Internet e il servizio di peering?**

Il servizio di peering è un servizio che intende fornire connettività IP pubblico di livello aziendale a Microsoft per i clienti aziendali. Internet di livello aziendale include connettività tramite ISP con connettività con velocità effettiva elevata a Microsoft e ridondanza per una connettività a disponibilità elevata. Inoltre, il traffico utente è ottimizzato per la latenza al Microsoft Edge più vicino. Il servizio di peering si basa sulla connettività del peering con il vettore partner. La connettività di peering con il partner deve essere il peering diretto anziché il peering di Exchange. Il peering diretto deve avere la ridondanza locale e geografica.

**Che cos'è il peering legacy?**

La connessione di peering configurata con Azure PowerShell viene gestita come una risorsa di Azure. Le connessioni di peering impostate in passato vengono archiviate nel sistema come peering legacy, che è possibile scegliere di convertire per gestire come risorsa di Azure.

**Quando viene chiamato New-AzPeeringDirectConnectionObject, quali indirizzi IP vengono assegnati ai dispositivi Microsoft e peer?**

Quando si chiama il cmdlet New-AzPeeringDirectConnectionObject, viene immesso un indirizzo/31 (a. b. c. d/31) o un indirizzo/30 (a. b. c. d/30). Il primo indirizzo IP (a. b. c. d + 0) viene assegnato al dispositivo del peer e il secondo indirizzo IP (a. b. c. d + 1) viene assegnato al dispositivo Microsoft.

**Che cosa sono i parametri MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 nel cmdlet New-AzPeeringDirectConnectionObject?**

I parametri MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 rappresentano il numero massimo di prefissi IPv4 e IPv6 che i peer desiderano accettare da Microsoft. Questi parametri possono essere modificati in qualsiasi momento.