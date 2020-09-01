---
title: Domande frequenti
description: Risposte alle domande frequenti sull'attestazione Microsoft Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236647"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Domande frequenti sull'attestazione Microsoft Azure

Questo articolo fornisce le risposte ad alcune delle domande più comuni sull' [attestazione di Azure](overview.md).

Se il problema di Azure non viene risolto in questo articolo, è anche possibile inviare una richiesta di supporto tecnico di Azure nella [pagina del supporto tecnico](https://azure.microsoft.com/support/options/)di Azure.

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Che cos'è il servizio di memorizzazione nella cache di Azure PCK e il suo ruolo nell'attestazione dell'enclave

Il servizio di memorizzazione nella cache di Azure PCK definisce la linea di base di sicurezza di Azure per i nodi di [Azure Confidential Computing (ACC)](../confidential-computing/overview.md) di Intel e memorizza nella cache i dati. Le informazioni memorizzate nella cache verranno usate ulteriormente dall'attestazione di Azure per la convalida degli ambienti di esecuzione attendibile (TEEs).  

Servizio di memorizzazione nella cache di Azure PCK:
   - Offre disponibilità elevata 
   - Riduce le dipendenze dai servizi ospitati esternamente e dalla connettività Internet.
   - Recupera le versioni più recenti di certificati Intel, CRL, Trusted computing base (TCB) e l'identità dell'enclave dei nodi ACC di Intel. Il servizio conferma quindi la linea di base di sicurezza di Azure a cui fa riferimento l'attestazione di Azure durante la convalida delle TEEs, riducendo in modo considerevole gli errori di attestazione dovuti all'invalidazione o alla revoca dei certificati Intel  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Attestazione SGX supportata dall'attestazione di Azure in ambienti non Azure

L'attestazione di Azure dipende dalla linea di base di sicurezza indicata dal servizio di memorizzazione nella cache di Azure PCK per convalidare i tee. Il servizio di memorizzazione nella cache di Azure PCK è attualmente progettato per supportare solo nodi di calcolo riservati di Azure. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Quali convalide esegue l'attestazione di Azure per l'attestazione delle enclave SGX

L'attestazione di Azure è un framework unificato per l'attestazione remota di diversi tipi di TEEs. Attestazione di Azure:

   - Convalida se la radice attendibile di una virgoletta enclave firmata appartiene a Intel.
   - Verifica se l'offerta enclave soddisfa la linea di base di sicurezza di Azure come definito dal servizio di memorizzazione nella cache di Azure PCK.
   - Verifica se l'hash SHA256 dei dati contenuti nell'enclave (EHD) nell'oggetto richiesta di attestazione corrisponde ai primi 32 byte del campo reportData nella virgoletta dell'enclave.
   - Consente ai clienti di creare un provider di attestazione e configurare un criterio personalizzato. Oltre alle convalide sopra riportate, l'attestazione di Azure valuta la quota dell'enclave rispetto ai criteri. I criteri definiscono le regole di autorizzazione per l'enclave e stabiliscono anche le regole di rilascio per la generazione del token di attestazione. Per verificare se il software previsto è in esecuzione in un'enclave, i clienti possono aggiungere le regole di autorizzazione per verificare se i campi **mrsigner** e **mrenclave** nell'enclave corrispondono ai valori dei file binari del cliente.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>In che modo un verificatore può ottenere la garanzia per l'attestazione SGX supportata dall'attestazione di Azure

In generale, per i modelli di attestazione con Intel come radice di attendibilità, il client di attestazione comunica con le API enclave per recuperare l'evidenza dell'enclave. Le API enclave chiamano internamente il servizio di memorizzazione nella cache di Intel PCK per recuperare i certificati Intel del nodo da attestare. I certificati vengono usati per firmare la prova dell'enclave, generando così una garanzia in remoto attestabile.  

Lo stesso processo può essere implementato per l'attestazione di Azure. Tuttavia, per sfruttare i vantaggi offerti dal servizio di memorizzazione nella cache di Azure PCK, dopo l'installazione della macchina virtuale ACC è consigliabile installare la [libreria DCAP di Azure](https://www.nuget.org/packages/Microsoft.Azure.DCAP). In base al contratto con Intel, quando viene installata la libreria Azure DCAP, le richieste di generazione dell'evidenza dell'enclave vengono reindirizzate dal servizio di memorizzazione nella cache di Intel PCK al servizio di memorizzazione nella cache di Azure PCK. La libreria DCAP di Azure è supportata negli ambienti basati su Windows e Linux.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Come passare all'attestazione di Azure da altri modelli di attestazione

- Dopo l'installazione della macchina virtuale di Azure Confidential computing, installare la libreria DCAP di Azure ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) per sfruttare i vantaggi offerti dal servizio di memorizzazione nella cache PCK di Azure.
- È necessario creare il client di attestazione remota che può recuperare l'evidenza dell'enclave e inviare richieste all'attestazione di Azure. Vedere [esempi di codice](/samples/browse/?expanded=azure&terms=attestation) per riferimento 
- È possibile inviare le richieste di attestazione all'endpoint API REST di provider predefiniti o provider di attestazioni personalizzati 
- Le API di attestazione di Azure sono protette dall'autenticazione Azure AD. Di conseguenza, il client che richiama le API di attestazione deve essere in grado di ottenere e passare un token di accesso Azure AD valido nella richiesta di attestazione 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Come è possibile verificare l'integrità del token di attestazione in relying party

Il token di attestazione generato dall'attestazione di Azure è firmato con un certificato autofirmato. I certificati vengono esposti tramite un [endpoint di metadati OpenID](/rest/api/attestation/metadataconfiguration/get). Il componente può recuperare i certificati di firma da questo endpoint ed eseguire la verifica della firma del token di attestazione. Il tempo di validità del token di attestazione è di 8 ore. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Come identificare il certificato da usare per la verifica della firma dall'endpoint di metadati OpenID

Più certificati esposti nell'endpoint di metadati OpenID corrispondono a diversi casi di utilizzo, ad esempio l'attestazione SGX, supportati dall'attestazione di Azure. In base agli standard specificati da [RFC 7515](https://tools.ietf.org/html/rfc7515), il certificato con ID chiave (Kid) corrispondente al parametro *Kid* nell'intestazione del token di attestazione deve essere usato per la verifica della firma. Se non viene trovato alcun elemento **figlio** corrispondente, si prevede di provare tutti i certificati esposti dall'endpoint di metadati OpenID.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Il relying party può condividere i segreti con gli ambienti di esecuzione attendibili convalidati (TEEs)

La chiave pubblica generata all'interno di un'enclave può essere espressa nella proprietà Data (EHD) dell'enclave dell'oggetto richiesta di attestazione inviata dal client all'attestazione di Azure. Dopo aver confermato se l'hash SHA256 di EHD è espresso nel campo reportData dell'offerta, l'attestazione di Azure include EHD nel token di attestazione. Relying party può usare EHD dalla risposta di attestazione verificata per crittografare i segreti e condividerli con l'enclave. Per ulteriori informazioni, vedere [concetti di base di attestazione di Azure](basic-concepts.md) .
