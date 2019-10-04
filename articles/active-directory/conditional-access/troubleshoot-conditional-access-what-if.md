---
title: Risolvere i problemi di accesso condizionale usando lo strumento What If-Azure Active Directory
description: Dove trovare i criteri di accesso condizionale applicati e perché
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
ms.openlocfilehash: b1d463bc889385f8f157ebb9892acc7a4a8b2a2e
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278532"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Risoluzione dei problemi di accesso condizionale mediante lo strumento What If

Lo [strumento What If](what-if-tool.md) nell'accesso condizionale è potente quando si tenta di comprendere il motivo per cui un criterio è stato o non è stato applicato a un utente in una circostanza specifica o se un criterio verrebbe applicato in uno stato noto.

Lo strumento What If si trova nel **portale di Azure** > **Azure Active Directory** > **What If**di**accesso** > condizionale.

![Strumento di What If dell'accesso condizionale in stato predefinito](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

## <a name="gathering-information"></a>Raccolta di informazioni

Lo strumento What If richiede solo un **utente** per iniziare. 

Le informazioni aggiuntive seguenti sono facoltative, ma consentono di restringere l'ambito per casi specifici.

* App o azioni cloud
* Indirizzo IP 
* Country
* Piattaforma del dispositivo.
* App client (anteprima)
* Stato del dispositivo (anteprima) 
* Rischio di accesso

Queste informazioni possono essere raccolte dall'utente, dal dispositivo o dal log degli accessi Azure AD.

## <a name="generating-results"></a>Generazione dei risultati

Immettere i criteri raccolti nella sezione precedente e selezionare **What If** per generare un elenco di risultati. 

In qualsiasi momento, è possibile selezionare **Reimposta** per cancellare eventuali input dei criteri e tornare allo stato predefinito.

## <a name="evaluating-results"></a>Valutazione dei risultati

### <a name="policies-that-will-apply"></a>Criteri che verranno applicati

Questo elenco indicherà i criteri di accesso condizionale applicati in base alle condizioni. L'elenco includerà i controlli di concessione e di sessione che si applicano. Gli esempi includono la richiesta di autenticazione a più fattori per l'accesso a un'applicazione specifica.

### <a name="policies-that-will-not-apply"></a>Criteri che non verranno applicati

Questo elenco indicherà i criteri di accesso condizionale che non si applicano se le condizioni sono state applicate. L'elenco includerà tutti i criteri e il motivo per cui non si applicano. Gli esempi includono utenti e gruppi che possono essere esclusi da un criterio.

## <a name="use-case"></a>Caso d'uso

Molte organizzazioni creano criteri basati sui percorsi di rete, consentendo posizioni attendibili e posizioni di blocco in cui l'accesso non deve essere eseguito.

Per convalidare che una configurazione è stata eseguita in modo appropriato, un amministratore può utilizzare lo strumento What If per simulare l'accesso, da un percorso che deve essere consentito e da un percorso che deve essere negato.

![Strumento What If che mostra i risultati con accesso a blocchi](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

In questo caso, all'utente viene impedito l'accesso a qualsiasi app cloud durante il viaggio verso la Corea del Nord, perché Contoso ha bloccato l'accesso da tale posizione.

Questo test potrebbe essere espanso per incorporare altri punti dati per limitare l'ambito.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'accesso condizionale?](overview.md)
* [Che cos'è Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Informazioni sulle identità dei dispositivi](../devices/overview.md)
* [Funzionamento: di Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
