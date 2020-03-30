---
title: Panoramica dei criteri TLS per il gateway applicazione di Azure
description: Informazioni su come configurare i criteri TLS per il gateway applicazione di Azure e ridurre l'overhead di crittografia e decrittografia da una server farm back-end.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257377"
---
# <a name="application-gateway-tls-policy-overview"></a>Panoramica dei criteri TLS del gateway applicazioneApplication Gateway TLS policy overview

È possibile usare Il gateway applicazione di Azure per centralizzare la gestione dei certificati TLS/SSL e ridurre il sovraccarico di crittografia e decrittografia da una server farm back-end. Questa gestione TLS centralizzata consente inoltre di specificare un criterio TLS centrale adatto ai requisiti di sicurezza dell'organizzazione. In questo modo, è possibile soddisfare i requisiti di conformità, nonché le linee guida e le procedure consigliate per la sicurezza.

Il criterio TLS include il controllo della versione del protocollo TLS, nonché dei pacchetti di crittografia e l'ordine in cui le crittografie vengono utilizzate durante un handshake TLS. Il gateway applicazione offre due meccanismi per il controllo dei criteri TLS. È possibile usare criteri predefiniti o personalizzati.

## <a name="predefined-tls-policy"></a>Criteri TLS predefiniti

Il gateway applicazione offre tre criteri di sicurezza predefiniti. È possibile configurare il gateway con uno qualsiasi di questi criteri per ottenere il livello di sicurezza appropriato. I nomi dei criteri sono contrassegnati con l'anno e il mese in cui sono stati configurati. Ogni criterio offre diverse versioni del protocollo TLS e suite di crittografia. Si consiglia di utilizzare i criteri TLS più recenti per garantire la migliore sicurezza TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Proprietà  |valore  |
|---|---|
|Nome     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Predefinito| True (se non vengono specificati criteri predefiniti) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Proprietà  |valore  |
|   ---      |  ---       |
|Nome     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Predefinito| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Proprietà  |valore  |
|---|---|
|Nome     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Predefinito| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Criteri TLS personalizzati

Se è necessario configurare un criterio TLS predefinito in base alle proprie esigenze, è necessario definire criteri TLS personalizzati. Con un criterio TLS personalizzato, si ha il controllo completo sulla versione minima del protocollo TLS da supportare, nonché sulle suite di crittografia supportate e sul relativo ordine di priorità.
 
### <a name="tlsssl-protocol-versions"></a>Versioni del protocollo TLS/SSL

* Per impostazione predefinita, i protocolli SSL 2.0 e 3.0 sono disabilitati per tutti i gateway applicazione. Queste versioni del protocollo non sono configurabili.
* Un criterio TLS personalizzato consente di selezionare uno dei tre protocolli seguenti come versione minima del protocollo TLS per il gateway: TLSv1_0, TLSv1_1 e TLSv1_2.
* Se non è definito alcun criterio TLS, vengono abilitati tutti e tre i protocolli (TLSv1_0, TLSv1_1 e TLSv1_2).

### <a name="cipher-suites"></a>Pacchetti di crittografia

Il gateway applicazione supporta i pacchetti di crittografia seguenti, tra i quali è possibile scegliere i criteri personalizzati. L'ordine dei pacchetti di crittografia determina l'ordine di priorità durante la negoziazione TLS.


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

> [!NOTE]
> Le suite di crittografia TLS utilizzate per la connessione si basano anche sul tipo di certificato utilizzato. Nelle connessioni da client a gateway applicazione, le suite di crittografia utilizzate sono basate sul tipo di certificati server nel listener del gateway applicazione. Nel gateway applicazione alle connessioni al pool back-end, i pacchetti di crittografia utilizzati si basano sul tipo di certificati server nei server del pool back-end.

## <a name="known-issue"></a>Problema noto
Il gateway applicazione v2 non supporta attualmente le seguenti crittografie:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Passaggi successivi

Se si desidera ottenere informazioni sulla configurazione di un criterio TLS, vedere [Configurare le versioni](application-gateway-configure-ssl-policy-powershell.md)dei criteri TLS e i pacchetti di crittografia nel gateway applicazione .
