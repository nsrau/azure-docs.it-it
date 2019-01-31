---
title: Come chiudere eventi di rischio attivi in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni sulle opzioni disponibili per chiudere eventi di rischio attivi.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 222f17541de902ee2180b66563857ccd2193ba54
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178308"
---
# <a name="how-to-close-active-risk-events"></a>Procedura: Chiudere gli eventi di rischio attivi

Con gli [eventi di rischio](../reports-monitoring/concept-risk-events.md), Azure Active Directory rileva indicatori di account utente potenzialmente compromessi. È possibile che un amministratore voglia chiudere tutti gli eventi di rischio in modo che gli utenti interessati non siano più a rischio.

Questo articolo offre una panoramica delle opzioni aggiuntive disponibili per chiudere gli eventi di rischio attivi.

## <a name="options-to-close-risk-events"></a>Opzioni per chiudere gli eventi di rischio 

Lo stato di un evento di rischio può essere **attivo** o **chiuso**. Tutti gli eventi di rischio attivi contribuiscono al calcolo di un valore denominato "livello di rischio utente". Il livello di rischio utente è un indicatore (basso, medio, elevato) della probabilità che un account sia stato compromesso. 

Per chiudere gli eventi di rischio attivi, sono disponibili le opzioni seguenti:

- Richiedere la reimpostazione della password con criteri di rischio utente

- Reimpostazione manuale della password
 
- Ignorare tutti gli eventi di rischio 

- Chiudere manualmente singoli eventi di rischio



## <a name="require-password-reset-with-a-user-risk-policy"></a>Richiedere la reimpostazione della password con criteri di rischio utente

Configurando i [criteri di accesso condizionale per il rischio utente](howto-user-risk-policy.md), è possibile richiedere una modifica della password ogni volta che viene automaticamente rilevato un determinato livello di rischio utente. 

![Reimposta password](./media/howto-close-active-risk-events/13.png)

La reimpostazione della password chiude tutti gli eventi di rischio attivi dell'utente correlato e ripristina l'identità a uno stato sicuro. L'adozione dei criteri di rischio utente è il metodo preferito per chiudere gli eventi di rischio attivi, poiché è un metodo automatizzato. Non è infatti necessaria alcuna interazione tra l'utente interessato e l'help desk o l'amministratore.

L'utilizzo dei criteri di rischio utente, tuttavia, non è sempre applicabile, come nei casi seguenti:

- Utenti non registrati per l'autenticazione a più fattori (MFA).
- Utenti con eventi di rischio attivi precedentemente eliminati.
- Un'analisi rivela che un evento di rischio segnalato è stato eseguito dall'utente legittimo.


## <a name="manual-password-reset"></a>Reimpostazione manuale della password

Se non è possibile richiedere una reimpostazione della password usando i criteri di rischio utente, è possibile chiudere tutti gli eventi di rischio relativi a un utente mediante una reimpostazione manuale della password.

![Reimposta password](./media/howto-close-active-risk-events/04.png)


La finestra di dialogo correlata fornisce due metodi diversi per reimpostare una password:

![Reimposta password](./media/howto-close-active-risk-events/05.png)


**Generare una password provvisoria**: generando una password provvisoria, è possibile ripristinare immediatamente un'identità a uno stato sicuro. Questo metodo richiede l'interazione con gli utenti interessati, che devono conoscere la password provvisoria. È possibile, ad esempio, inviare la nuova password provvisoria a un indirizzo di posta elettronica alternativo dell'utente o al responsabile dell'utente. La password è provvisoria e, pertanto, l'utente dovrà modificarla all'accesso successivo.


**Richiedere all'utente di reimpostare la password**: richiedendo agli utenti di reimpostare le password, è possibile eseguire il ripristino in modo autonomo, senza contattare l'help desk o l'amministratore. Come nel caso dei criteri di rischio utente, questo metodo può essere adottato solo per gli utenti registrati per l'autenticazione MFA. Per gli utenti non registrati per l'autenticazione MFA, questa opzione non è disponibile.


## <a name="dismiss-all-risk-events"></a>Ignorare tutti gli eventi di rischio

Se la reimpostazione della password non è un'opzione attuabile, è possibile ignorare tutti gli eventi di rischio. 

![Reimposta password](./media/howto-close-active-risk-events/03.png)

Quando si fa clic su **Dismiss all events**(Ignora tutti gli eventi), tutti gli eventi vengono chiusi e l'utente interessato non è più a rischio. Questo metodo, tuttavia, non ha alcun impatto sulla password esistente e, pertanto, non ripristina l'identità correlata a uno stato sicuro. Viene applicato, in genere, nel caso di un utente eliminato con eventi di rischio attivi. 


## <a name="close-individual-risk-events-manually"></a>Chiudere manualmente singoli eventi di rischio

È possibile chiudere manualmente singoli eventi di rischio. In questo modo, infatti, è possibile abbassare il livello di rischio utente. In genere, gli eventi di rischio vengono chiusi manualmente in risposta a un'indagine correlata, ad esempio quando, parlando con un utente, si stabilisce che un evento di rischio attivo non è più necessario. 
 
Quando si chiude un evento di rischio manualmente, è possibile eseguire una di queste azioni per modificare lo stato dell'evento di rischio:

![Azioni](./media/howto-close-active-risk-events/06.png)

- **Risolvi** : se è stata eseguita l'azione di correzione appropriata all'esterno di Identity Protection dopo l'analisi di un evento di rischio e si ritiene che l'evento sia da considerare chiuso, contrassegnarlo come risolto. Gli eventi risolti impostano lo stato dell'evento di rischio come chiuso e l'evento di rischio non viene più conteggiato nel rischio utente.

- **Contrassegna come falso positivo** : in alcuni casi è possibile analizzare un evento di rischio e scoprire che è stato erroneamente contrassegnato come rischioso. È possibile ridurre il numero di tali occorrenze contrassegnando l'evento di rischio come falso positivo. Questo permetterà agli algoritmi di Machine Learning di migliorare la classificazione di eventi simili in futuro. Lo stato dell'evento falso positivo viene impostato come Chiuso e non viene più conteggiato nel rischio utente.

- **Ignora** : se non sono state intraprese azioni di correzione, ma si vuole rimuovere l'evento di rischio dall'elenco attivo, è possibile contrassegnarlo come da ignorare e il relativo stato verrà impostato come chiuso. Gli eventi ignorati non vengono conteggiati nel rischio utente. Questa opzione deve essere usata solo in circostanze particolari.

- **Riattiva**: gli eventi di rischio che sono stati chiusi manualmente mediante Risolvi, Contrassegna come falso positivo o Ignora, possono essere riattivati impostando nuovamente lo stato dell'evento su Attivo. Gli eventi di rischio riattivati vengono conteggiati nel calcolo del livello di rischio utente. Gli eventi di rischio chiusi tramite correzione, ad esempio la reimpostazione della password di protezione, non possono essere riattivati.
  

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
