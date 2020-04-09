---
title: Aggiornamenti TLS hub di notifica
description: Informazioni sul supporto per TLS negli hub di notifica di Azure.Learn about support for TLS in Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885753"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Per garantire un livello di sicurezza superiore, Notification Hubs disabiliterà il supporto per TLS versioni 1.0 e 1.1 il 31 maggio 2020 (esteso dal 30 aprile 2020). Questi protocolli meno recenti forniscono crittografia debole e sono vulnerabili agli attacchi BEAST e POODLE. Le applicazioni distribuite nei dispositivi che eseguono Android versione 5 o successiva, o iOS versione 5 o successiva, non sono interessate da questa modifica in quanto tali sistemi operativi supportano TLS 1.2 e il client e il server negozieranno la versione più elevata supportata reciprocamente del protocollo al momento della connessione.

È consigliabile esaminare tutte le applicazioni che usano hub di notifica di Azure per assicurarsi che utilizzino le librerie e gli stack TLS più applicabili che supportano TLS 1.2.

## <a name="update-apps"></a>Aggiornare le app

Puoi assicurarti che le tue app iOS utilizzino TLS 1.2 usando la funzionalità di sicurezza di rete di Apple chiamata App Transport Security (ATS). ATS non può essere utilizzato per SDK precedenti a iOS 9.0 o macOS 10.11, e si può leggere ulteriormente su di esso dalla [documentazione di Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Per le applicazioni Android che utilizzano istanze SSLSocket, impostare protocolli abilitati su ogni istanza di SSLSocket come indicato in [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

La tabella nella pagina di supporto di [Compatibilità protocollo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) consente di mappare i sistemi operativi con versioni TLS compatibili.

Per ulteriori informazioni, vedere la panoramica del supporto per i [protocolli TLS in Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica degli hub di notifica](notification-hubs-push-notification-overview.md)