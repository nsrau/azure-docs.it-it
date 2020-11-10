---
title: Come creare un criterio di attestazione di Azure
description: Informazioni su come creare un criterio di attestazione.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e36de62b79788e2efdc3e9abf711924c4fba0c4
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341808"
---
# <a name="how-to-author-an-attestation-policy"></a>Come creare un criterio di attestazione

Il criterio di attestazione è un file caricato in Attestazione di Microsoft Azure. Attestazione di Azure offre la flessibilità per caricare un criterio in un formato specifico dell'attestazione. In alternativa, è possibile caricare anche una versione codificata del criterio, in formato JSW (JSON Web Signature). L'amministratore dei criteri è responsabile della scrittura dei criteri di attestazione. Nella maggior parte degli scenari di attestazione, la relying party funge da amministratore dei criteri. Il client che effettua la chiamata di attestazione invia l'evidenza dell'attestazione, che il servizio analizza e converte in attestazioni in ingresso (set di proprietà, valore). Il servizio elabora quindi le attestazioni in base a quanto definito nel criterio e restituisce il risultato calcolato.

Il criterio contiene regole che determinano i criteri di autorizzazione, le proprietà e il contenuto del token di attestazione. Un file di criteri di esempio è simile al seguente:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Un file di criteri è costituito da tre segmenti, come illustrato sopra:

- **version** :  il numero di versione della grammatica seguita. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Attualmente l'unica versione supportata è 1.0.

- **authorizationrules** : una raccolta di regole di attestazione che verrà controllata per prima, per determinare se Attestazione di Azure deve procedere a **issuancerules**. Le regole di attestazione si applicano nell'ordine in cui sono definite.

- **issuancerules** : una raccolta di regole di attestazione che verranno valutate per aggiungere altre informazioni al risultato dell'attestazione definito nel criterio. Le regole di attestazione si applicano nell'ordine in cui sono definite e sono anche facoltative.

Per altre informazioni, vedere [Attestazioni e regole di attestazione](claim-rule-grammar.md).
   
## <a name="drafting-the-policy-file"></a>Bozza del file di criteri

1. Creare un nuovo file.
1. Aggiungere la versione al file.
1. Aggiungere le sezioni per **authorizationrules** e **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Le regole di autorizzazione contengono l'azione deny() senza alcuna condizione, per garantire che non vengano elaborate regole di rilascio. In alternativa, la regola di autorizzazione può includere anche l'azione permit() per consentire l'elaborazione di regole di rilascio.
  
4. Aggiungere regole di attestazione alle regole di autorizzazione

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Se il set di attestazioni in ingresso contiene un'attestazione corrispondente al tipo, al valore e all'autorità emittente, l'azione permit() indica al motore dei criteri di elaborare **issuancerules**.
  
5. Aggiungere regole di attestazione a **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Il set di attestazioni in uscita conterrà un'attestazione con:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  I criteri complessi possono essere creati in modo analogo. Per altre informazioni, vedere [Esempi di criteri di attestazione](policy-examples.md).
  
6. Salvare il file.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Creazione del file di criteri nel formato JWS (JSON Web Signature)

Dopo aver creato un file di criteri, per caricare un criterio in formato JWS, seguire questa procedura.

1. Generare il criterio JWS, RFC 7515 con i criteri (con codifica UTF-8) come payload
     - L'identificatore del payload per i criteri con codifica Base64Url deve essere "AttestationPolicy".
     
     Token JWT di esempio:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (Facoltativo) Firmare i criteri. Attestazione di Azure supporta gli algoritmi seguenti:
     - **Nessuna** : il payload dei criteri non viene firmato.
     - **RS256** : algoritmo supportato per la firma del payload dei criteri

3. Caricare il file JWS e convalidare il criterio.
     - Se il file di criteri è privo di errori di sintassi, viene accettato dal servizio.
     - Se il file di criteri contiene errori di sintassi, viene rifiutato dal servizio.

## <a name="next-steps"></a>Passaggi successivi
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
- [Attestare un'enclave SGX con esempi di codice](/samples/browse/?expanded=azure&terms=attestation)