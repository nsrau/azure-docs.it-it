---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116907"
---
Una nuova funzionalità del client VPN di Windows 10, Always On, è la possibilità di mantenere una connessione VPN. Con Always On, il profilo VPN attivo può connettersi automaticamente e rimanere connesso in base ai trigger, ad esempio l'accesso dell'utente, il cambiamento dello stato della rete o lo schermo del dispositivo attivo.

È possibile usare i gateway con Windows 10 Always On per stabilire tunnel utente persistenti e tunnel di dispositivo in Azure.You can use gateways with Windows 10 Always On to establish persistent user tunnels and device tunnels to Azure. Questo articolo consente di configurare un tunnel utente VPN Always On.

Le connessioni VPN Always On includono uno dei due tipi di tunnel:

* **Tunnel del dispositivo:** si connette ai server VPN specificati prima che gli utenti accedano al dispositivo. Gli scenari di connettività di accesso preliminare e la gestione dei dispositivi usano un tunnel del dispositivo.

* **Tunnel utente:** si connette solo dopo che gli utenti hanno effettuato l'accesso al dispositivo. Utilizzando i tunnel utente, è possibile accedere alle risorse dell'organizzazione tramite server VPN.

I tunnel dei dispositivi e i tunnel utente operano indipendentemente dai profili VPN. Possono essere connessi contemporaneamente e possono utilizzare diversi metodi di autenticazione e altre impostazioni di configurazione VPN, a seconda dei casi.
