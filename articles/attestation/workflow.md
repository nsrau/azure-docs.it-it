---
title: Flusso di lavoro di Attestazione di Azure
description: Il flusso di lavoro di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236962"
---
# <a name="workflow"></a>Flusso di lavoro

Attestazione di Microsoft Azure riceve l'evidenza dalle enclavi e la valuta rispetto alla baseline di sicurezza di Azure e a criteri configurabili. Al completamento della verifica, Attestazione di Azure genera un token di attestazione per confermare l'attendibilità dell'enclave.

In un flusso di lavoro di Attestazione di Azure sono coinvolti gli attori seguenti:

- **Relying party**: il componente che si basa su Attestazione di Azure per verificare la validità dell'enclave. 
- **Client**: il componente che raccoglie informazioni da un'enclave e invia richieste ad Attestazione di Azure. 
- **Attestazione di Azure**: il componente che accetta l'evidenza dell'enclave dal client, la convalida e restituisce il token di attestazione al client


## <a name="enclave-validation-work-flow"></a>Flusso di lavoro di convalida dell'enclave

Ecco i passaggi generali di un tipico flusso di lavoro di attestazione dell'enclave SGX enclave (usando Attestazione di Azure):

1. Il client raccoglie l'evidenza da un'enclave. L'evidenza è costituita da informazioni sull'ambiente dell'enclave e sulla libreria client in esecuzione nell'enclave.
1. Il client ha un URI che fa riferimento a un'istanza di Attestazione di Azure. Il client esegue l'autenticazione con Azure AD e ottiene un token di accesso.
1. Il client invia l'evidenza ad Attestazione di Azure insieme al token di accesso. Le informazioni esatte inviate al provider variano in base al tipo di enclave.
1. Attestazione di Azure convalida le informazioni inviate e le valuta rispetto a un criterio configurato. Se la verifica ha esito positivo, Attestazione di Azure emette un token di attestazione e lo restituisce al client. Se questo passaggio ha esito negativo, Attestazione di Azure segnala un errore al client. 
1. Il client invia il token di attestazione alla relying party. La relying party chiama l'endpoint di metadati con chiave pubblica di Attestazione di Azure per recuperare i certificati di firma. La relying party verifica quindi la firma del token di attestazione e garantisce l'attendibilità dell'enclave. 

![Flusso di convalida dell'enclave](./media/validation-flow.png)


## <a name="next-steps"></a>Passaggi successivi
- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
