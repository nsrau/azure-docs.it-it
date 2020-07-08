---
title: Risolvere i problemi di Accesso condizionale tramite lo strumento What If - Azure Active Directory
description: Come trovare i criteri di Accesso condizionale applicati e il motivo dell'applicazione
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9aaab58acb00305f76b10847a0417d91d724ba8
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737640"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Risoluzione dei problemi di Accesso condizionale tramite lo strumento What If

Lo [strumento What If](what-if-tool.md) in Accesso condizionale è utile per comprendere il motivo per cui determinati criteri sono o non sono stati applicati a un utente in una circostanza specifica o se dei criteri verrebbero applicati in uno stato noto.

Lo strumento What If è disponibile nel **portale di Azure** > **Azure Active Directory** > **Accesso condizionale** > **What If**.

![Strumento What If di Accesso condizionale nello stato predefinito](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Attualmente lo strumento What If non valuta i criteri in modalità Solo report.

## <a name="gathering-information"></a>Raccolta di informazioni

Per iniziare, lo strumento What If richiede solo un **Utente**. 

Le informazioni aggiuntive seguenti sono facoltative, ma possono essere utili per la definizione dell'ambito in casi specifici.

* App o azioni cloud
* Indirizzo IP 
* Paese/Area geografica
* Piattaforma del dispositivo.
* App client (anteprima)
* Stato del dispositivo (anteprima) 
* Rischio di accesso

Queste informazioni possono essere raccolte dall'utente, dal dispositivo dell'utente o dal log degli accessi di Azure AD.

## <a name="generating-results"></a>Generazione dei risultati

Immettere i criteri raccolti nella sezione precedente e selezionare **What If** per generare un elenco di risultati. 

In qualsiasi momento è possibile selezionare **Reimposta** per cancellare eventuali input dei criteri e tornare allo stato predefinito.

## <a name="evaluating-results"></a>Valutazione dei risultati

### <a name="policies-that-will-apply"></a>Criteri applicabili

Questo elenco indica i criteri di accesso condizionale che saranno applicabili in base alle condizioni. L'elenco includerà i controlli di concessione e di sessione applicabili. Gli esempi includono la richiesta dell'autenticazione a più fattori per l'accesso a un'applicazione specifica.

### <a name="policies-that-will-not-apply"></a>Criteri non applicabili

Questo elenco visualizzerà i criteri di Accesso condizionale che non saranno applicabili se le condizioni sono risultate applicabili. L'elenco includerà tutti i criteri e il motivo per cui non sono applicabili. Gli esempi includono utenti e gruppi che possono essere esclusi da un criterio.

## <a name="use-case"></a>Caso d'uso

Molte organizzazioni creano criteri in base ai percorsi di rete, autorizzando i percorsi attendibili e bloccando i percorsi dai quali non è previsto che si esegua l'accesso.

Per verificare che una configurazione sia stata eseguita in modo appropriato, un amministratore può usare lo strumento What If per simulare l'accesso sia da un percorso consentito sia da un percorso non consentito.

![Strumento What If che visualizza i risultati con Blocca accesso](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

In questo caso all'utente viene impedito l'accesso a qualsiasi app cloud durante il suo viaggio in Corea del Nord, perché Contoso ha bloccato l'accesso da tale località.

Questo test potrebbe essere espanso per incorporare altri punti dati al fine di limitare l'ambito.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'accesso condizionale](overview.md)
* [Informazioni su Azure Active Directory Identity Protection](../identity-protection/overview-v2.md)
* [Informazioni sulle identità dei dispositivi](../devices/overview.md)
* [Funzionamento: di Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
