---   
title: Panoramica dei criteri SSL per il gateway applicazione di Azure | Microsoft Docs
description: Informazioni su come il gateway applicazione di Azure consente di configurare i criteri SSL
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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 938591bc2422f3137492e86fc60f7d5529596424
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
   

# <a name="application-gateway-ssl-policy-overview"></a>Panoramica dei criteri SSL per il gateway applicazione

Il gateway applicazione consente di centralizzare la gestione dei certificati SSL e ridurre il carico di crittografia e decrittografia di una server farm back-end. Questa gestione centralizzata di SSL permette anche di specificare criteri SSL centrali adatti ai requisiti di sicurezza dell'organizzazione. In questo modo è possibile rispettare sia i requisiti di conformità che le linee guida e le procedure consigliate per la sicurezza.

I criteri SSL includono il controllo sia della versione del protocollo e dei pacchetti di crittografia che dell'ordine in cui i pacchetti di crittografia vengono usati durante un handshake SSL. Per consentire ai clienti di controllare i criteri SSL, il gateway applicazione offre due meccanismi: i criteri predefiniti e quelli personalizzati.

## <a name="predefined-ssl-policy"></a>Criteri SSL predefiniti

Il gateway applicazione offre tre criteri di sicurezza predefiniti. È possibile configurare il gateway con uno qualsiasi di questi criteri per ottenere il livello di sicurezza appropriato. I nomi dei criteri sono contrassegnati con l'anno e il mese in cui sono stati configurati. Ogni criterio offre versioni del protocollo SSL e pacchetti di crittografia diversi. Per garantire una sicurezza SSL ottimale, è consigliabile usare i criteri SSL più recenti. Il gateway applicazione consente attualmente agli utenti di scegliere uno dei criteri predefiniti seguenti.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Proprietà  |Valore  |
|---|---|
|Nome     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| True (se non vengono specificati criteri predefiniti) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Proprietà  |Valore  |
|   ---      |  ---       |
|Nome     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_0        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Proprietà  |Valore  |
|---|---|
|Nome     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_2        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Criteri SSL personalizzati

Se è necessario configurare un criterio SSL predefinito per i propri requisiti, si devono definire criteri SSL personalizzati. In questo caso si ha il controllo completo sulla versione minima del protocollo SSL da supportare nonché sui pacchetti di crittografia supportati e sul relativo ordine di priorità.
 
Versioni del protocollo SSL:

* Per impostazione predefinita, SSL 2.0 e 3.0 sono disabilitati per tutti i gateway applicazione. Queste versioni del protocollo non sono configurabili.
* La definizione di criteri SSL personalizzati offre la possibilità di selezionare uno qualsiasi dei tre protocolli seguenti come versione minima del protocollo SSL per il gateway: TLSv1_0, TLSv1_1 e TLSv1_2.
* Se non è definito alcun criterio SSL, sono abilitati tutti e tre (TLSv1_0, TLSv1_1 e TLSv1_2).

Pacchetto di crittografia:

Il gateway applicazione supporta i pacchetti di crittografia seguenti, selezionabili per i propri criteri personalizzati. L'ordinamento dei pacchetti di crittografia determina l'ordine di priorità durante la negoziazione SSL.

Pacchetti di crittografia disponibili:

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

[Configurare i criteri SSL in un gateway applicazione](application-gateway-configure-ssl-policy-powershell.md)

