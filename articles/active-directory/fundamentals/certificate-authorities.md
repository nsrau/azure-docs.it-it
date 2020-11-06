---
title: Autorità di certificazione Azure Active Directory
description: Elenco di certificati attendibili usati in Azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0ab8eac97a6b02377d38653a990a2f0d5ff81ba
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94334959"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Autorità di certificazione utilizzate da Azure Active Directory

> [!IMPORTANT]
> Le informazioni contenute in questa pagina sono rilevanti solo per le entità che specificano in modo esplicito un elenco di autorità di certificazione (CAs) accettabili. Questa procedura, nota come blocco del certificato, deve essere evitata, a meno che non esistano altre opzioni.

Qualsiasi entità che tenta di accedere ai servizi di identità Azure Active Directory (Azure AD) tramite i protocolli TLS/SSL verrà visualizzata con i certificati delle autorità di certificazione elencate di seguito. Se l'entità considera attendibile tali autorità di certificazione, può usare i certificati per verificare l'identità e la legittimità dei servizi di identità e stabilire connessioni sicure.

Le autorità di certificazione possono essere classificate in CA radice e CA intermedie. In genere, le CA radice hanno una o più autorità di certificazione intermedie associate. Questo articolo elenca le CA radice utilizzate dai servizi di identità Azure AD e le CA intermedie associate a ognuna di queste radici. Per ogni CA sono inclusi gli URI (Uniform Resource Identifier) per scaricare i file di accesso alle informazioni dell'autorità (AIA) e del punto di distribuzione dell'elenco di revoche di certificati (CDP) associati. Quando appropriato, viene anche fornito un URI per l'endpoint del protocollo di stato del certificato online (OCSP).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>CA usate nei cloud di Azure per enti pubblici degli Stati Uniti e Azure

Diversi servizi possono utilizzare CA radice o intermedie diverse. Pertanto, è possibile che vengano richieste tutte le voci elencate di seguito.

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| CA radice| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 1° agosto 2013 <br>15 gennaio 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>CA intermedie associate

| Emissione e CA intermedia| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - | 
| Microsoft Azure TLS emittente CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29 luglio 2020<br>27 giugno 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS emittente CA 02| 0c6ae97cced59983 8690a00a9ea53214| 29 luglio 2020<br>27 giugno 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS emittente CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 29 luglio 2020<br>27 giugno 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS emittente CA 06| 02e79171fb8021e93fe 2d983834c50c0| 29 luglio 2020<br>27 giugno 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| CA radice| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 12 maggio, 2000<br>12 maggio, 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>CA intermedie associate

| Emissione e CA intermedia| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21 luglio 2020<br>8 ottobre 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21 luglio 2020<br>20 maggio 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| CA radice| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 9 novembre, 2006<br>9 novembre, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>CA intermedie associate

| Emissione e CA intermedia| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 8 marzo 2013 8 marzo 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |22 settembre 2020<br>22 settembre 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>CA usate in Azure Cina 21Vianet cloud

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| CA radice| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| Nov. 9, 2006<br>Nov. 9, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>CA intermedia associata

| Emissione e CA intermedia| Numero di serie| Data di scadenza della data di rilascio| Identificazione personale SHA1| URI |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4 marzo 2020<br>4 marzo 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sulle catene di crittografia Microsoft 365](https://docs.microsoft.com/microsoft-365/compliance/encryption-office-365-certificate-chains?view=o365-worldwide)
