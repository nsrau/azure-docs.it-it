---
title: Panoramica dei criteri SSL per il gateway applicazione di Azure | Microsoft Docs
description: Informazioni sul modo in cui il gateway applicazione di Azure consente di configurare criteri SSL
services: application gateway
documentationcenter: na
author: amsriva
manager: 
editor: 
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: ec36af282bbfdc22ff88082412dd18cd2a85f245
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-ssl-policy-overview"></a>Panoramica dei criteri SSL per il gateway applicazione

È possibile usare il gateway applicazione di Azure per centralizzare la gestione dei certificati SSL e ridurre il sovraccarico di crittografia e decrittografia da una server farm back-end. Questa gestione centralizzata dei certificati SSL permette anche di specificare criteri SSL centrali adatti ai requisiti di sicurezza dell'organizzazione. In questo modo, è possibile soddisfare i requisiti di conformità, nonché le linee guida e le procedure consigliate per la sicurezza.

I criteri SSL includono il controllo della versione del protocollo SSL e dei pacchetti di crittografia, insieme all'ordine in cui usare i pacchetti di crittografia durante un handshake SSL. Il gateway applicazione offre due meccanismi per il controllo dei criteri SSL. È possibile usare criteri predefiniti o personalizzati.

## <a name="predefined-ssl-policy"></a>Criteri SSL predefiniti

Il gateway applicazione offre tre criteri di sicurezza predefiniti. È possibile configurare il gateway con uno qualsiasi di questi criteri per ottenere il livello di sicurezza appropriato. I nomi dei criteri sono contrassegnati con l'anno e il mese in cui sono stati configurati. Ogni criterio offre versioni del protocollo SSL e pacchetti di crittografia diversi. Per garantire una sicurezza SSL ottimale, è consigliabile usare i criteri SSL più recenti.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Proprietà  |Valore  |
|---|---|
|NOME     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Predefinito| True (se non vengono specificati criteri predefiniti) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Proprietà  |Valore  |
|   ---      |  ---       |
|NOME     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Predefinito| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Proprietà  |Valore  |
|---|---|
|NOME     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Predefinito| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Criteri SSL personalizzati

Se è necessario configurare un criterio SSL predefinito per i propri requisiti, definire criteri SSL personalizzati. I criteri SSL personalizzati forniscono il controllo completo sulla versione minima del protocollo SSL supportata, nonché i pacchetti di crittografia supportati e il rispettivo ordine di priorità.
 
### <a name="ssl-protocol-versions"></a>Versioni del protocollo SSL

* Per impostazione predefinita, i protocolli SSL 2.0 e 3.0 sono disabilitati per tutti i gateway applicazione. Queste versioni del protocollo non sono configurabili.
* La definizione di criteri SSL personalizzati offre la possibilità di selezionare uno qualsiasi di questi tre protocolli come versione minima del protocollo SSL per il gateway: TLSv1_0, TLSv1_1 e TLSv1_2.
* Se non sono definiti criteri SSL, sono abilitati tutti e tre i protocolli (TLSv1_0, TLSv1_1 e TLSv1_2).

### <a name="cipher-suites"></a>Pacchetti di crittografia

Il gateway applicazione supporta i pacchetti di crittografia seguenti, tra i quali è possibile scegliere i criteri personalizzati. L'ordinamento dei pacchetti di crittografia determina l'ordine di priorità durante la negoziazione SSL.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare criteri SSL, vedere [Configurare criteri SSL nel gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).
