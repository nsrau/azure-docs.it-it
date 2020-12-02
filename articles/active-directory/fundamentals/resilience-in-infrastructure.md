---
title: Resilienza della compilazione nell'infrastruttura IAM con Azure Active Directory
description: Una guida per architetti e amministratori IT senza comportare la resilienza dell'infrastruttura IAM.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f65ab02e06319519548eaa2c02120691a0ceef02
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498558"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Resilienza della compilazione nell'infrastruttura di gestione delle identità e dell'accesso

Azure Active Directory è un sistema di gestione delle identità e degli accessi cloud globale che fornisce servizi critici, ad esempio l'autenticazione e l'autorizzazione per le risorse dell'organizzazione. In questo documento vengono fornite indicazioni per comprendere, contenere e mitigare il rischio di un'ininterrottezza dei servizi di autenticazione o autorizzazione per le risorse basate su Azure Active Directory (Azure AD). 

Il set di documenti è progettato per

* Architetti di identità

* Proprietari del servizio di identità

* Team operativi delle identità

Vedere anche la documentazione per [gli sviluppatori di applicazioni](https://aka.ms/azureadresilience/developer) e per i [sistemi Azure ad B2C](resilience-b2c.md).

## <a name="what-is-resilience"></a>Che cos'è la resilienza?

Nel contesto dell'infrastruttura di gestione delle identità, la resilienza è la capacità di superare le interruzioni dei servizi, ad esempio l'autenticazione e l'autorizzazione, l'errore di altri componenti, con un minimo o nessun effetto sull'azienda, sugli utenti e sulle operazioni. L'effetto dell'intralcio può essere grave e la resilienza richiede una pianificazione diligente.

## <a name="why-worry-about-disruption"></a>Perché preoccuparsi delle problematiche?

Ogni chiamata al sistema di autenticazione è soggetta a rotture se un componente della chiamata ha esito negativo. Quando l'autenticazione viene interrotta, a causa degli errori dei componenti sottostanti, gli utenti non accederanno alle applicazioni. Pertanto, la riduzione del numero di chiamate di autenticazione e il numero di dipendenze in tali chiamate è importante per la resilienza. Gli sviluppatori di applicazioni possono asserire un certo controllo sulla frequenza con cui vengono richiesti i token. È possibile, ad esempio, collaborare con gli sviluppatori per assicurarsi che utilizzino Azure AD identità gestite per le applicazioni, laddove possibile. 

In un sistema di autenticazione basato su token come Azure AD, un'applicazione dell'utente (client) deve acquisire un token di sicurezza dal sistema di identità prima di poter accedere a un'applicazione o a un'altra risorsa. Durante il periodo di validità, un client può presentare lo stesso token più volte per accedere all'applicazione.

Quando il token presentato all'applicazione scade, l'applicazione rifiuta il token e il client deve acquisire un nuovo token da Azure AD. L'acquisizione di un nuovo token richiede potenzialmente l'interazione dell'utente, ad esempio la richiesta di credenziali o la soddisfazione di altri requisiti del sistema di autenticazione. Riducendo la frequenza delle chiamate di autenticazione con token di lunga durata si diminuiscono le interazioni non necessarie. Tuttavia, è necessario bilanciare la vita del token con i rischi creati da un minor numero di valutazioni dei criteri. Per ulteriori informazioni sulla gestione delle durate dei token, vedere questo articolo sull' [ottimizzazione delle richieste di riautenticazione](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

## <a name="ways-to-increase-resilience"></a>Modi per aumentare la resilienza
Il diagramma seguente illustra sei metodi concreti per aumentare la resilienza. Ogni metodo è illustrato in dettaglio negli articoli collegati nella sezione passaggi successivi di questo articolo.
  
![Diagramma che illustra la panoramica della resilienza amministratore](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza della compilazione con la gestione delle credenziali](resilience-in-credentials.md)

* [Resilienza di compilazione con gli Stati dei dispositivi](resilience-with-device-states.md)

* [Resilienza della compilazione usando la valutazione di accesso continuo (CAE)](resilience-with-continuous-access-evaluation.md)

* [Resilienza di compilazione nell'autenticazione utente esterna](resilience-b2b-authentication.md)

* [Resilienza di compilazione nell'autenticazione ibrida](resilience-in-hybrid.md)

* [Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione](resilience-on-premises-access.md)

Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
