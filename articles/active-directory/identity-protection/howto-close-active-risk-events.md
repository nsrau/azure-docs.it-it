---
title: Come chiudere i rilevamenti di rischio attivi in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni sulle opzioni di rilevamento dei rischi attivi di chiusura.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126481"
---
# <a name="how-to-close-active-risk-detections"></a>Procedura: Chiusura rilevamento del rischio attivo

Con i rilevamenti dei [rischi](../reports-monitoring/concept-risk-events.md), Azure Active Directory rileva gli indicatori per gli account utente potenzialmente compromessi. In qualità di amministratore, si desidera che tutti i rilevamenti dei rischi siano chiusi, in modo che gli utenti interessati non siano più a rischio.

Questo articolo offre una panoramica delle opzioni aggiuntive che è necessario per chiudere i rilevamenti dei rischi attivi.

## <a name="options-to-close-risk-detections"></a>Opzioni per chiudere i rilevamenti di rischio 

Lo stato di un rilevamento dei rischi è **attivo** o **chiuso**. Tutti i rilevamenti dei rischi attivi contribuiscono al calcolo di un valore denominato livello di rischio utente. Il livello di rischio utente è un indicatore (basso, medio, elevato) della probabilità che un account sia stato compromesso. 

Per chiudere i rilevamenti dei rischi attivi, sono disponibili le opzioni seguenti:

- Richiedere la reimpostazione della password con criteri di rischio utente
- Reimpostazione manuale della password
- Ignora tutti i rilevamenti di rischio 
- Chiudi manualmente i singoli rilevamenti di rischi

## <a name="require-password-reset-with-a-user-risk-policy"></a>Richiedere la reimpostazione della password con criteri di rischio utente

Configurando i criteri di accesso condizionale per il [rischio utente](howto-user-risk-policy.md), è possibile richiedere una modifica della password se è stato rilevato automaticamente un livello di rischio utente specificato. 

![Reimposta password](./media/howto-close-active-risk-events/13.png)

La reimpostazione della password chiude tutti gli eventi di rischio attivi dell'utente correlato e ripristina l'identità a uno stato sicuro. L'uso di criteri di rischio utente è il metodo preferito per chiudere i rilevamenti dei rischi attivi perché questo metodo è automatizzato. Non è infatti necessaria alcuna interazione tra l'utente interessato e l'help desk o l'amministratore.

L'utilizzo dei criteri di rischio utente, tuttavia, non è sempre applicabile, come nei casi seguenti:

- Utenti non registrati per l'autenticazione a più fattori (MFA).
- Utenti con rilevamento dei rischi attivi eliminati.
- Un'indagine che rivela che un rilevamento dei rischi segnalato è stato eseguito dall'utente legittimo.

## <a name="manual-password-reset"></a>Reimpostazione manuale della password

Se la richiesta di reimpostazione della password tramite criteri di rischio utente non è un'opzione, è possibile ottenere tutti i rilevamenti dei rischi per un utente chiuso con una reimpostazione manuale della password.

![Reimposta password](./media/howto-close-active-risk-events/04.png)

La finestra di dialogo correlata fornisce due metodi diversi per reimpostare una password:

![Reimposta password](./media/howto-close-active-risk-events/05.png)

**Generare una password provvisoria**: generando una password provvisoria, è possibile ripristinare immediatamente un'identità a uno stato sicuro. Questo metodo richiede l'interazione con gli utenti interessati, che devono conoscere la password provvisoria. È possibile, ad esempio, inviare la nuova password provvisoria a un indirizzo di posta elettronica alternativo dell'utente o al responsabile dell'utente. La password è provvisoria e, pertanto, l'utente dovrà modificarla all'accesso successivo.

**Richiedere all'utente di reimpostare la password**: richiedendo agli utenti di reimpostare le password, è possibile eseguire il ripristino in modo autonomo, senza contattare l'help desk o l'amministratore. Come nel caso dei criteri di rischio utente, questo metodo può essere adottato solo per gli utenti registrati per l'autenticazione MFA. Per gli utenti non registrati per l'autenticazione MFA, questa opzione non è disponibile.

## <a name="dismiss-all-risk-detections"></a>Ignora tutti i rilevamenti di rischio

Se la reimpostazione della password non è un'opzione, è anche possibile ignorare tutti i rilevamenti dei rischi. 

![Reimposta password](./media/howto-close-active-risk-events/03.png)

Quando si fa clic su **Dismiss all events**(Ignora tutti gli eventi), tutti gli eventi vengono chiusi e l'utente interessato non è più a rischio. Questo metodo, tuttavia, non ha alcun impatto sulla password esistente e, pertanto, non ripristina l'identità correlata a uno stato sicuro. Il caso d'uso preferito per questo metodo è un utente eliminato con rilevamento dei rischi attivi. 

## <a name="close-individual-risk-detections-manually"></a>Chiudi manualmente i singoli rilevamenti di rischi

È possibile chiudere manualmente i singoli rilevamenti di rischi. Chiudendo manualmente i rilevamenti dei rischi, è possibile ridurre il livello di rischio utente. In genere, i rilevamenti di rischio vengono chiusi manualmente in risposta a un'analisi correlata. Ad esempio, quando si comunica con un utente, viene rivelato che non è più necessario un rilevamento attivo dei rischi. 
 
Quando si chiudono manualmente i rilevamenti dei rischi, è possibile scegliere di eseguire una delle azioni seguenti per modificare lo stato di un rilevamento del rischio:

![Azioni](./media/howto-close-active-risk-events/06.png)

- **Risolvi** : se dopo aver analizzato un rilevamento dei rischi, è stata intrapresa un'azione correttiva appropriata al di fuori di Identity Protection e si ritiene che il rilevamento dei rischi venga considerato chiuso, contrassegnare l'evento come risolto. Gli eventi risolti impostano lo stato del rilevamento dei rischi su chiuso e il rilevamento dei rischi non contribuirà più al rischio dell'utente.
- **Contrassegno come falso positivo** : in alcuni casi, è possibile esaminare un rilevamento dei rischi e rilevare che è stato erroneamente contrassegnato come rischioso. È possibile ridurre il numero di tali occorrenze contrassegnando il rilevamento dei rischi come falso positivo. Questo permetterà agli algoritmi di Machine Learning di migliorare la classificazione di eventi simili in futuro. Lo stato dell'evento falso positivo viene impostato come Chiuso e non viene più conteggiato nel rischio utente.
- **Ignora** : se non è stata eseguita alcuna azione di correzione, ma si vuole che il rilevamento dei rischi venga rimosso dall'elenco attivo, è possibile contrassegnare un rilevamento dei rischi ignore e lo stato dell'evento verrà chiuso. Gli eventi ignorati non vengono conteggiati nel rischio utente. Questa opzione deve essere usata solo in circostanze particolari.
- Riattivare: i rilevamenti dei rischi chiusi manualmente (scegliendo Risolvi, falsi positivi o Ignora) possono essere riattivati, impostando lo stato dell'evento su attivo. Il rilevamento dei rischi riattivati contribuisce al calcolo del livello di rischio utente. Non è possibile riattivare i rilevamenti dei rischi chiusi tramite correzione (ad esempio una reimpostazione della password sicura).

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
