---
title: Concetti di base di Attestazione di Azure
description: Concetti di base di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a4ab8372e23e3621f7d73f8dbc38957c809acc9c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236920"
---
# <a name="basic-concepts"></a>Concetti di base

Di seguito sono riportati alcuni concetti di base relativi ad Attestazione di Microsoft Azure.

## <a name="json-web-token-jwt"></a>Token Web JSON (JWT)

[JSON Web Token](https://jwt.io/) (JWT) è un metodo [RFC7519](https://tools.ietf.org/html/rfc7519) basato su standard aperti per la trasmissione sicura di informazioni tra entità come oggetto JSON (JavaScript Object Notation). Queste informazioni possono essere verificate e considerate attendibili perché sono firmate digitalmente. I token JWT possono essere firmati con un segreto o con una coppia di chiavi pubblica/privata.

## <a name="json-web-key-jwk"></a>Chiave Web JSON

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) è una struttura di dati JSON che rappresenta una chiave crittografica. Questa specifica definisce anche una struttura di dati JSON JWK Set che rappresenta un set di chiavi JWK.

## <a name="attestation-provider"></a>Provider di attestazioni

Il provider di attestazioni appartiene al provider di risorse di Azure denominato Microsoft.Attestation. Il provider di risorse è un endpoint del servizio che fornisce il contratto REST di Attestazione di Azure e viene distribuito con [Azure Resource Manager](../azure-resource-manager/management/overview.md). Ogni provider di attestazioni rispetta criteri specifici individuabili. 

I provider di attestazioni vengono creati con un criterio predefinito per ogni tipo di ambiente TEE (si noti che l'enclave VBS non include criteri predefiniti). Per informazioni dettagliate sui criteri predefiniti per SGX, vedere [Esempi di un criterio di attestazione](policy-examples.md).

### <a name="regional-default-provider"></a>Provider predefinito a livello di area

Attestazione di Azure prevede un provider predefinito in ogni area. I clienti possono scegliere di usare il provider predefinito per l'attestazione o di creare i propri provider con criteri personalizzati. I provider predefiniti sono accessibili a qualsiasi utente di Azure AD e il criterio associato a un provider predefinito non può essere modificato.

| Area | URI di attestazione | 
|--|--|
| Regno Unito meridionale | https://shareduks.uks.attest.azure.net | 
| Stati Uniti orientali 2 | https://sharedeus2.eus2.attest.azure.net | 
| Stati Uniti centrali | https://sharedcus.cus.attest.azure.net | 
| Stati Uniti orientali| https://sharedeus.eus.attest.azure.net | 
| Canada centrale | https://sharedcac.cac.attest.azure.net | 

## <a name="attestation-request"></a>Richiesta di attestazione

La richiesta di attestazione è un oggetto JSON serializzato inviato dall'applicazione client al provider di attestazioni. L'oggetto della richiesta per l'enclave SGX ha due proprietà: 
- "Quote": il valore della proprietà "Quote" è una stringa contenente una rappresentazione con codifica Base64URL dell'offerta di attestazione
- "EnclaveHeldData": il valore della proprietà "EnclaveHeldData" è una stringa contenente una rappresentazione con codifica Base64URL dei dati contenuti nell'enclave.

Attestazione di Azure convaliderà l'"offerta" fornita da TEE e si assicurerà che l'hash SHA256 dei dati contenuti nell'enclave forniti venga espresso nei primi 32 byte del campo reportData dell'offerta. 

## <a name="attestation-policy"></a>Criteri di attestazione

Il criterio di attestazione viene usato per elaborare l'evidenza dell'attestazione ed è configurabile dai clienti. Alla base di Attestazione di Azure è presente un motore di criteri che elabora le attestazioni che costituiscono l'evidenza. I criteri vengono usati per determinare se Attestazione di Azure emetterà un token di attestazione in base all'evidenza (o meno) e quindi approverà l'attestatore (o meno). Di conseguenza, se i criteri non vengono tutti soddisfatti, non verranno emessi token JWT.

Se il criterio TEE predefinito nel provider di attestazioni non soddisfa le esigenze, i clienti saranno in grado di creare criteri personalizzati in tutte le aree supportate da Attestazione di Azure. La gestione dei criteri è una funzionalità chiave fornita ai clienti da Attestazione di Azure. I criteri saranno specifici di TEE e potranno essere usati per identificare le enclavi o aggiungere attestazioni al token di output oppure per modificare le attestazioni in un token di output. 

Per informazioni sui contenuti e gli esempi di criteri predefiniti, vedere [Esempi di un criterio di attestazione](policy-examples.md).

## <a name="benefits-of-policy-signing"></a>Vantaggi della firma dei criteri

Un criterio di attestazione è quello che determina in definitiva se un token di attestazione verrà emesso da Attestazione di Azure. Il criterio determina anche le attestazioni da generare nel token di attestazione. È pertanto fondamentale che il criterio valutato dal servizio sia effettivamente quello scritto dall'amministratore e che non sia stato manomesso o modificato da entità esterne. 

Il modello di attendibilità definisce il modello di autorizzazione del provider di attestazioni per la definizione e l'aggiornamento dei criteri.  Sono supportati due modelli, uno basato sull'autorizzazione di Azure AD e l'altro basato sul possesso di chiavi crittografiche gestite dal cliente (definito modello isolato).  Il modello isolato consentirà ad Attestazione di Azure di garantire che i criteri inviati dal cliente non vengano manomessi.

In un modello isolato l'amministratore crea un provider di attestazioni che specifica un set di certificati di firma X.509 attendibili in un file. L'amministratore può quindi aggiungere un criterio firmato al provider di attestazioni. Durante l'elaborazione della richiesta di attestazione, Attestazione di Azure convaliderà la firma del criterio usando la chiave pubblica rappresentata dal parametro "jwk" o "x5c" nell'intestazione.  Attestazione di Azure verificherà inoltre se la chiave pubblica nell'intestazione della richiesta è presente nell'elenco di certificati di firma attendibili associati al provider di attestazioni. In questo modo, la relying party (Attestazione di Azure) può considerare attendibile un criterio firmato usando certificati X.509 riconosciuti. 

Per altre informazioni, vedere [Esempi di certificato del firmatario di criteri](policy-signer-examples.md).

## <a name="attestation-token"></a>Token di attestazione

La risposta di Attestazione di Azure sarà una stringa JSON il cui valore contiene JWT. Attestazione di Azure assemblerà le attestazioni e genererà un token JWT firmato. L'operazione di firma viene eseguita usando un certificato autofirmato con il nome del soggetto corrispondente all'elemento AttestUri del provider di attestazioni.

L'API Get OpenID Metadata restituisce una risposta OpenID Configuration come specificato dal [protocollo OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). L'API recupera i metadati relativi ai certificati di firma usati da Attestazione di Azure.

Esempio di token JWT generato per un'enclave SGX:

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
Attestazioni come "exp", "iat", "iss" e "nbf" vengono definite da [JWT RFC](https://tools.ietf.org/html/rfc7517) e le altre vengono generate da Attestazione di Azure. Per altre informazioni, vedere [Attestazioni emesse da Attestazione di Azure](claim-sets.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
