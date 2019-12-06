---
title: Come funziona l'autenticazione a più fattori di Azure-Azure Active Directory
description: Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0982f6fb70cd6866af48feab640d5dc36bcb6b28
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848681"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Funzionamento di Azure Multi-Factor Authentication

La sicurezza della verifica in due passaggi sta nel suo approccio a livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di un attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del metodo di autenticazione aggiuntivo. In genere richiede due o più dei metodi di autenticazione seguenti:

* Un'informazione nota (in genere una password)
* Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
* Una caratteristica fisica dell'utente (biometrica)

<center>

![l'immagine di metodi di autenticazione concettuali](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso un utilizzo semplice agli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md). Gli utenti possono o meno visualizzare una richiesta di autenticazione MFA in base alle decisioni di configurazione prese da un amministratore.

## <a name="how-to-get-multi-factor-authentication"></a>Come ottenere Multi-Factor Authentication

Multi-Factor Authentication è inclusa in quanto parte delle offerte seguenti:

* **Azure Active Directory Premium** o **Microsoft 365 business** : uso completo di Azure multi-factor authentication usando i criteri di accesso condizionale per richiedere l'autenticazione a più fattori.

* **Azure ad free** o licenze di **Office 365** autonome: usare i [criteri di protezione di base per l'accesso condizionale](../conditional-access/concept-baseline-protection.md) predefinito per richiedere l'autenticazione a più fattori per gli utenti e gli amministratori.

* **Amministratori globali di Azure Active Directory**: per proteggere gli account di amministratore globale è disponibile un subset di funzionalità di Azure multi-Factor Authentication.

> [!NOTE]
> I nuovi clienti non possono più acquistare Azure Multi-Factor Authentication come offerta autonoma valida dal 1 ° settembre 2018. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

## <a name="supportability"></a>Supporto

Poiché la maggior parte degli utenti è abituata a usare le password solo per l'autenticazione, è importante che l'organizzazione si metta in comunicazione con tutti gli utenti per informarli in merito al processo. La consapevolezza può ridurre la probabilità che gli utenti contattino l'help desk per problemi di lieve entità riguardanti l'autenticazione a più fattori. Esistono tuttavia alcuni scenari in cui è necessario disabilitare temporaneamente MFA. Per sapere come gestire questi scenari, usare le linee guida seguenti:

* Istruire il personale di supporto per le situazioni in cui l'utente non riesce ad accedere perché non ha accesso ai suoi metodi di autenticazione oppure perchè questi ultimi non funzionano correttamente.
   * Usando i criteri di accesso condizionale per il Servizio autenticazione a più fattori di Azure, il personale di supporto può aggiungere un utente a un gruppo escluso dai criteri che richiedono l'autenticazione a più fattori.
* Provare a usare i percorsi denominati di accesso condizionale per ridurre al minimo le richieste di verifica in due passaggi. Con questa funzionalità gli amministratori possono ignorare la verifica in due passaggi per gli utenti che accedono da un percorso di rete attendibile sicuro, ad esempio un segmento di rete usato per l'onboarding di un nuovo utente.
* Distribuire [Azure ad Identity Protection](../active-directory-identityprotection.md) e attivare la verifica in due passaggi in base al rilevamento del rischio.

## <a name="next-steps"></a>Passaggi successivi

- [Procedura dettagliata per la distribuzione di Multi-Factor Authentication di Azure](howto-mfa-getstarted.md)
