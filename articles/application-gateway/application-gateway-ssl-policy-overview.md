---
title: Panoramica dei criteri TLS per applicazione Azure gateway
description: Informazioni su come configurare i criteri TLS per applicazione Azure gateway e ridurre il sovraccarico di crittografia e decrittografia da un server farm back-end.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 16c6dd28d47573c2ad5b0d5a331b0dc48e7aacef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253631"
---
# <a name="application-gateway-tls-policy-overview"></a>Panoramica dei criteri TLS del gateway applicazione

È possibile usare applicazione Azure gateway per centralizzare la gestione dei certificati TLS/SSL e ridurre il sovraccarico di crittografia e decrittografia da un server farm back-end. Questa gestione centralizzata di TLS consente inoltre di specificare un criterio TLS centrale adatto ai requisiti di sicurezza dell'organizzazione. In questo modo, è possibile soddisfare i requisiti di conformità, nonché le linee guida e le procedure consigliate per la sicurezza.

I criteri TLS includono il controllo della versione del protocollo TLS, nonché i pacchetti di crittografia e l'ordine in cui vengono usate le crittografie durante un handshake TLS. Il gateway applicazione offre due meccanismi per il controllo dei criteri TLS. È possibile usare criteri predefiniti o personalizzati.

## <a name="predefined-tls-policy"></a>Criteri TLS predefiniti

Il gateway applicazione offre tre criteri di sicurezza predefiniti. È possibile configurare il gateway con uno qualsiasi di questi criteri per ottenere il livello di sicurezza appropriato. I nomi dei criteri sono contrassegnati con l'anno e il mese in cui sono stati configurati. Ogni criterio offre diverse versioni del protocollo TLS e pacchetti di crittografia. Si consiglia di usare i criteri TLS più recenti per garantire la migliore sicurezza TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Proprietà  |Valore  |
|---|---|
|Nome     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Predefinito| True (se non vengono specificati criteri predefiniti) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Proprietà  |Valore  |
|   ---      |  ---       |
|Nome     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Predefinito| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Proprietà  |Valore  |
|---|---|
|Nome     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Predefinito| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Criteri TLS personalizzati

Se è necessario configurare un criterio TLS predefinito per i propri requisiti, è necessario definire un criterio TLS personalizzato. Con un criterio TLS personalizzato, si ha il controllo completo sulla versione minima del protocollo TLS da supportare, nonché sui pacchetti di crittografia supportati e sul relativo ordine di priorità.
 
### <a name="tlsssl-protocol-versions"></a>Versioni del protocollo TLS/SSL

* Per impostazione predefinita, i protocolli SSL 2.0 e 3.0 sono disabilitati per tutti i gateway applicazione. Queste versioni del protocollo non sono configurabili.
* I criteri TLS personalizzati offrono la possibilità di selezionare uno dei tre protocolli seguenti come versione minima del protocollo TLS per il gateway: TLSv1_0, TLSv1_1 e TLSv1_2.
* Se non è definito alcun criterio TLS, verranno abilitati tutti e tre i protocolli (TLSv1_0, TLSv1_1 e TLSv1_2).

### <a name="cipher-suites"></a>Pacchetti di crittografia

Il gateway applicazione supporta i pacchetti di crittografia seguenti, tra i quali è possibile scegliere i criteri personalizzati. L'ordinamento dei pacchetti di crittografia determina l'ordine di priorità durante la negoziazione TLS.


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
> I pacchetti di crittografia TLS usati per la connessione sono basati anche sul tipo di certificato usato. Nelle connessioni da client a gateway applicazione, i pacchetti di crittografia usati sono basati sul tipo di certificati del server nel listener del gateway applicazione. Nel gateway applicazione per le connessioni al pool back-end, i pacchetti di crittografia usati sono basati sul tipo di certificati del server nei server del pool back-end.

## <a name="known-issue"></a>Problema noto
Il gateway applicazione V2 non supporta attualmente le crittografie seguenti:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio TLS, vedere [configurare le versioni dei criteri TLS e i pacchetti di crittografia nel gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).
