---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812754"
---
1. Nella pagina della rete WAN virtuale selezionare **Configurazioni VPN utente**.
1. Nella parte superiore della pagina Selezionare **Download user VPN config** (Scarica configurazione VPN utente). Quando si scarica la configurazione a livello di rete WAN, si ottiene un profilo VPN utente basato su Gestione traffico. Per altre informazioni sui profili globali o sui profili basati su hub, vedere [Profili degli hub](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile). Gli scenari di failover sono semplificati con il profilo globale.

   Se per qualche motivo un hub non è disponibile, il profilo predefinito di Gestione traffico fornito dal servizio assicura la connettività tramite un hub diverso alle risorse di Azure per gli utenti da punto a sito. È sempre possibile scaricare una configurazione VPN specifica dell'hub passando all'hub. In **VPN utente (da punto a sito)** scaricare il profilo **VPN utente** dell'hub virtuale.
1. Una volta creato il file, selezionare il collegamento per scaricarlo.
1. Usare il file del profilo per configurare i client VPN.
