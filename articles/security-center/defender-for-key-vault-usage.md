---
title: Come rispondere ad Azure Defender per Key Vault avvisi
description: Informazioni sui passaggi necessari per rispondere agli avvisi da Azure Defender per Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dd153eb1b0e96165bccc807a72bc3614ded49428
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301687"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Rispondere agli avvisi di Azure Defender per Key Vault
Quando si riceve un avviso da Azure Defender per Key Vault, è consigliabile esaminare e rispondere all'avviso come descritto di seguito. Azure Defender per Key Vault protegge le applicazioni e le credenziali, quindi anche se si ha familiarità con l'applicazione o l'utente che ha attivato l'avviso, è importante verificare la situazione che circonda ogni avviso.  

Ogni avviso di Azure Defender per Key Vault include gli elementi seguenti:

- ID dell'oggetto.
- Nome dell'entità utente o indirizzo IP della risorsa sospetta

> [!TIP]
> In base al *tipo* di accesso che si è verificato, è possibile che alcuni campi non siano disponibili. Ad esempio, se l'accesso all'insieme di credenziali delle chiavi è stato eseguito da un'applicazione, non verrà visualizzato un nome dell'entità utente associato. Se il traffico proviene dall'esterno di Azure, non viene visualizzato un ID oggetto.

## <a name="step-1-contact"></a>Passaggio 1. Contatto

1. Verificare se il traffico è stato originato dall'interno del tenant di Azure. Se il firewall dell'insieme di credenziali delle chiavi è abilitato, è probabile che sia stato concesso l'accesso all'utente o all'applicazione che ha attivato l'avviso.
1. Se non è possibile verificare l'origine del traffico, continuare con il [passaggio 2. Mitigazione immediata](#step-2-immediate-mitigation).
1. Se è possibile identificare l'origine del traffico nel tenant, contattare l'utente o il proprietario dell'applicazione. 

> [!CAUTION]
> Azure Defender per Key Vault è progettato per consentire di identificare attività sospette causate da credenziali rubate. **Non** chiudere l'avviso semplicemente perché si riconosce l'utente o l'applicazione. Contattare il proprietario dell'applicazione o l'utente e verificare che l'attività sia legittima. Se necessario, è possibile creare una regola di eliminazione per eliminare il rumore. Per ulteriori informazioni, vedere la pagina relativa all' [eliminazione degli avvisi da Azure Defender](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Passaggio 2. Mitigazione immediata 
Se non si riconosce l'utente o l'applicazione o se si ritiene che l'accesso non sia stato autorizzato:

- Se il traffico proviene da un indirizzo IP non riconosciuto:
    1. Abilitare il firewall Azure Key Vault come descritto in [configurare Azure Key Vault firewall e reti virtuali](../key-vault/general/network-security.md).
    1. Configurare il firewall con risorse attendibili e reti virtuali.

- Se l'origine dell'avviso è un'applicazione non autorizzata o un utente sospetto:
    1. Aprire le impostazioni dei criteri di accesso dell'insieme di credenziali delle chiavi.
    1. Rimuovere l'entità di sicurezza corrispondente o limitare le operazioni che possono essere eseguite dall'entità di sicurezza.  

- Se l'origine dell'avviso ha un ruolo Azure Active Directory nel tenant:
    1. Contattare l'amministratore.
    1. Determinare se è necessario ridurre o revocare le autorizzazioni Azure Active Directory.

## <a name="step-3-identify-impact"></a>Passaggio 3. Identificare l'effetto 
Quando l'impatto è stato mitigato, esaminare i segreti nell'insieme di credenziali delle chiavi interessato:
1. Aprire la pagina "sicurezza" nel Azure Key Vault e visualizzare l'avviso attivato.
1. Selezionare l'avviso specifico che è stato attivato.
    Esaminare l'elenco dei segreti a cui è stato eseguito l'accesso e il timestamp.
1. Facoltativamente, se sono abilitati i log di diagnostica dell'insieme di credenziali delle chiavi, esaminare le operazioni precedenti per l'IP chiamante, l'entità utente o l'ID oggetto corrispondente.  

## <a name="step-4-take-action"></a>Passaggio 4. Intervieni 
Dopo aver compilato l'elenco dei segreti, delle chiavi e dei certificati accessibili dall'utente o dall'applicazione sospetta, è necessario ruotare immediatamente tali oggetti.

1. I segreti interessati devono essere disabilitati o eliminati dall'insieme di credenziali delle chiavi.
1. Se le credenziali sono state usate per un'applicazione specifica:
    1. Contattare l'amministratore dell'applicazione e chiedere di controllare l'ambiente per qualsiasi uso delle credenziali compromesse perché sono state compromesse.
    1. Se sono state usate le credenziali compromesse, il proprietario dell'applicazione dovrebbe identificare le informazioni a cui è stato eseguito l'accesso e attenuare l'effetto.


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato il processo di risposta a un avviso di Azure Defender per Key Vault. Per informazioni correlate, vedere le pagine seguenti:

- [Introduzione ad Azure Defender per Key Vault](defender-for-key-vault-introduction.md)
- [Non visualizzare avvisi da Azure Defender](alerts-suppression-rules.md)
- [Esporta avvisi di sicurezza](continuous-export.md)