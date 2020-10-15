---
title: Novità di cache di Azure per Redis
description: Aggiornamenti recenti per cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492451"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Novità di cache di Azure per Redis

## <a name="azure-tls-certificate-change"></a>Modifica del certificato TLS di Azure

Microsoft sta aggiornando i servizi di Azure per l'uso di certificati server TLS da un diverso set di autorità di certificazione (CAs). Questa modifica viene implementata in fasi dal 13 agosto 2020 al 26 ottobre 2020 (stimato). Questa modifica è stata apportata da Azure perché [i certificati della CA corrente non sono conformi a uno dei requisiti di base del forum ca/browser](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). Il problema è stato segnalato il 1 ° luglio 2020 e si applica a più provider di infrastruttura a chiave pubblica (PKI) diffusi in tutto il mondo. La maggior parte dei certificati TLS usati dai servizi di Azure provengono attualmente dall'infrastruttura a chiave pubblica di *Baltimore CyberTrust Root* . Il servizio cache di Azure per il servizio Redis continuerà a essere concatenato alla radice Baltimore CyberTrust. I certificati del server TLS verranno tuttavia emessi dalle nuove autorità di certificazione intermedie (ICAs) a partire dal 12 ottobre 2020.

> [!NOTE]
> Questa modifica è limitata ai servizi nelle aree pubbliche di [Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Esclude il sovrano (ad esempio, Cina) o i cloud governativi.
>
>

### <a name="does-this-change-affect-me"></a>Quali sono gli effetti di questa modifica?

Si prevede che la maggior parte delle cache di Azure per i clienti Redis non sia interessata dalla modifica. È possibile che l'applicazione abbia un effetto se specifica esplicitamente un elenco di certificati accettabili, una procedura nota come "blocco del certificato". Se viene aggiunto a un certificato intermedio o foglia anziché alla radice Baltimore CyberTrust, è necessario **intraprendere azioni immediate** per modificare la configurazione del certificato.

Nella tabella seguente vengono fornite informazioni sui certificati sottoposti a rollback. A seconda del certificato usato dall'applicazione, potrebbe essere necessario aggiornarlo per evitare la perdita di connettività alla cache di Azure per l'istanza di Redis.

| Tipo di CA | Corrente | Post-Rolling (12 ottobre 2020) | Azione |
| ----- | ----- | ----- | ----- |
| Radice | Identificazione personale: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Scadenza: lunedì, 12 maggio, 2025, 4:59:00 PM<br><br> Nome soggetto:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Senza modifiche | Nessuno |
| Intermedi | Identificazioni personali<br> CN = Microsoft IT TLS CA 1<br> Identificazione personale: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Identificazione personale: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Identificazione personale: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Identificazione personale: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Scadenza: venerdì, 20 maggio, 2024 5:52:38 AM<br><br> Nome soggetto:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | Identificazioni personali<br> CN = Microsoft RSA TLS CA 01<br> Identificazione personale: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Identificazione personale: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Scadenza: martedì 8 ottobre 2024 12:00:00 AM;<br><br> Nome soggetto:<br> O = Microsoft Corporation<br> C = US<br> | Obbligatoria |

### <a name="what-actions-should-i-take"></a>Quali azioni è necessario eseguire?

Se l'applicazione usa l'archivio certificati del sistema operativo o blocca la radice Baltimore tra gli altri, non è necessaria alcuna azione. D'altra parte, se l'applicazione blocca un certificato TLS intermedio o foglia, è consigliabile aggiungere le radici seguenti:

| Certificato | Identificazione personale |
| ----- | ----- |
| [CA radice Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [DigiCert radice globale G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> È previsto che i certificati intermedi e foglia vengano modificati di frequente. Si consiglia di non assumere una dipendenza. Aggiungere invece l'applicazione a un certificato radice poiché viene eseguito il Rolling meno frequente.
>
>

Per continuare a bloccare i certificati intermedi, aggiungere quanto segue all'elenco dei certificati intermedi bloccati, che include alcuni altri elementi per ridurre al minimo le modifiche future:

| Nome comune della CA | Identificazione personale |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS emittente CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS emittente CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS emittente CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS emittente CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Se l'applicazione convalida il certificato nel codice, sarà necessario modificarlo in modo che riconosca le proprietà (ad esempio, autorità di certificazione, identificazione personale) dei certificati appena aggiunti. Questa verifica aggiuntiva dovrebbe coprire tutti i certificati aggiunti, in modo da ottenere una prova più futura.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori domande, contattare il [supporto tecnico](https://azure.microsoft.com/support/options/).  
