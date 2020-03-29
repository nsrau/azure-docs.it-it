---
title: Aggiornamenti TLS hub di notifica
description: Informazioni sul supporto per TLS negli hub di notifica di Azure.Learn about support for TLS in Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908527"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Per garantire un livello di sicurezza superiore, Notification Hubs disattiverà il supporto per TLS versioni 1.0 e 1.1 il 30 aprile 2020. Questi protocolli meno recenti forniscono crittografia debole e sono vulnerabili agli attacchi BEAST e POODLE. Le applicazioni distribuite nei dispositivi che eseguono Android versione 5 o successiva, o iOS versione 5 o successiva, non sono interessate da questa modifica in quanto tali sistemi operativi supportano TLS 1.2 e il client e il server negozieranno la versione più alta supportata reciprocamente del protocollo al momento della connessione.

È consigliabile esaminare tutte le applicazioni che usano hub di notifica di Azure per assicurarsi che utilizzino le librerie e gli stack TLS più applicabili che supportano TLS 1.2.

## <a name="update-apps"></a>Aggiornare le app

Puoi assicurarti che le tue app iOS utilizzino TLS 1.2 usando la funzionalità di sicurezza di rete di Apple chiamata App Transport Security (ATS). ATS non può essere utilizzato per SDK precedenti a iOS 9.0 o macOS 10.11, e si può leggere ulteriormente su di esso dalla [documentazione di Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Per le applicazioni Android che utilizzano istanze SSLSocket, impostare protocolli abilitati su ogni istanza di SSLSocket come indicato in [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

La tabella nella pagina di supporto di [Compatibilità protocollo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) consente di mappare i sistemi operativi con versioni TLS compatibili.

Per ulteriori informazioni, vedere la panoramica del supporto per i [protocolli TLS in Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica degli hub di notifica](notification-hubs-push-notification-overview.md)