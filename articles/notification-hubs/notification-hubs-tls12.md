---
title: Aggiornamenti TLS di hub di notifica
description: Informazioni sul supporto di TLS in hub di notifica di Azure.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908527"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Per garantire un livello di sicurezza superiore, hub di notifica Disabilita il supporto per le versioni TLS 1,0 e 1,1 il 30 aprile 2020. Questi protocolli meno recenti forniscono una crittografia debole e sono vulnerabili agli attacchi di bestia e barboncino. Le applicazioni distribuite nei dispositivi che eseguono Android versione 5 o versioni successive o iOS versione 5 o successive non sono interessate da questa modifica, perché questi sistemi operativi supportano TLS 1,2 e il client e il server negozieranno la versione più elevata supportata dal reciproco protocollo alla connessione.

Si consiglia di rivedere tutte le applicazioni che usano Hub di notifica di Azure per assicurarsi che usino le librerie e gli stack TLS più idonei che supportano TLS 1,2.

## <a name="update-apps"></a>Aggiornare le app

È possibile assicurarsi che le app iOS stiano usando TLS 1,2 usando la funzionalità di sicurezza di rete di Apple denominata app Transport Security (ATS). Non è possibile usare ATS per gli SDK precedenti a iOS 9,0 o macOS 10,11 ed è possibile leggerne ulteriormente la [documentazione di Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Per le applicazioni Android che usano istanze di SSLSocket, impostare i protocolli abilitati in ogni istanza di SSLSocket come indicato in [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

La tabella nella pagina supporto per la [compatibilità del protocollo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) consente di eseguire il mapping dei sistemi operativi con versioni di TLS compatibili.

Per ulteriori informazioni, vedere la panoramica del [supporto per i protocolli TLS in Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'Hub di notifica](notification-hubs-push-notification-overview.md)