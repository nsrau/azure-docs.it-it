---
title: Risolvere i problemi relativi all'accesso condizionale usando lo strumento Cosa if - Azure Active DirectoryTroubleshoot Conditional Access using the What If tool - Azure Active Directory
description: Dove trovare i criteri di accesso condizionale applicati e i motivi
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
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175785"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Risoluzione dei problemi relativi all'accesso condizionale tramite lo strumento What If

Lo [strumento What If](what-if-tool.md) in Accesso condizionale è potente quando si tenta di comprendere il motivo per cui un criterio è stato o non è stato applicato a un utente in una circostanza specifica o se un criterio si applicherebbe in uno stato noto.

Lo strumento What If si trova nel **portale** > di Azure**Azure Azure Active Directory** > **Conditional Access** > **What If**.

![Strumento Elementi di analisi condizionali nello stato predefinitoConditional Access What If tool at default state](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Lo strumento What If attualmente non valuta i criteri in modalità solo report.

## <a name="gathering-information"></a>Raccolta di informazioni

Lo strumento What If richiede solo un **utente** per iniziare. 

Le seguenti informazioni aggiuntive sono facoltative, ma contribuiranno a restringere l'ambito per casi specifici.

* App o azioni cloud
* Indirizzo IP 
* Country
* Piattaforma del dispositivo.
* App client (anteprima)Client apps (preview)
* Stato del dispositivo (anteprima)Device state (preview) 
* Rischio di accesso

Queste informazioni possono essere raccolte dall'utente, dal dispositivo o dal log degli accessi di Azure AD.

## <a name="generating-results"></a>Generazione di risultati

Immettere i criteri raccolti nella sezione precedente e selezionare **What If** per generare un elenco di risultati. 

In qualsiasi momento, è possibile selezionare **Reimposta** per cancellare qualsiasi input di criteri e tornare allo stato predefinito.

## <a name="evaluating-results"></a>Valutazione dei risultati

### <a name="policies-that-will-apply"></a>Criteri che si applicheranno

Questo elenco mostrerà quali criteri di accesso condizionale verranno applicati in base alle condizioni. L'elenco includerà sia i controlli di concessione che quelli di sessione applicabili. Gli esempi includono la richiesta dell'autenticazione a più fattori per accedere a un'applicazione specifica.

### <a name="policies-that-will-not-apply"></a>Criteri che non si applicheranno

In questo elenco verranno visualizzati i criteri di accesso condizionale che non si applicano se si applicano le condizioni. L'elenco includerà tutti i criteri e il motivo per cui non si applicano. Gli esempi includono utenti e gruppi che possono essere esclusi da un criterio.

## <a name="use-case"></a>Caso d'uso

Molte organizzazioni creano criteri basati su percorsi di rete, consentendo percorsi attendibili e bloccando percorsi in cui non deve verificarsi l'accesso.

Per verificare che una configurazione sia stata effettuata in modo appropriato, un amministratore potrebbe utilizzare lo strumento What If per simulare l'accesso, da una posizione che deve essere consentita e da una posizione che deve essere negata.

![Strumento What If che mostra i risultati con Blocca accesso](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

In questo caso, all'utente verrebbe impedito di accedere a qualsiasi app cloud durante il viaggio in Corea del Nord poiché Contoso ha bloccato l'accesso da tale posizione.

Questo test potrebbe essere espanso per incorporare altri punti dati per restringere l'ambito.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'accesso condizionale?](overview.md)
* [Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Informazioni sulle identità dei dispositivi](../devices/overview.md)
* [Funzionamento di Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
