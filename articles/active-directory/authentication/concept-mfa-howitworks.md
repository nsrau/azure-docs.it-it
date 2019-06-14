---
title: 'Azure multi-Factor Authentication - come funziona: Azure Active Directory'
description: Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8f27d727768f3f1135f8929310d5cfeffe874b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113528"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Come funziona: Azure Multi-Factor Authentication

La sicurezza della verifica in due passaggi sta nel suo approccio a livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di un attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del metodo di autenticazione aggiuntivo. In genere richiede due o più dei metodi di autenticazione seguenti:

* Un'informazione nota (in genere una password)
* Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
* Una caratteristica fisica dell'utente (biometrica)

<center>

![Immagine di metodi di autenticazione concettuale](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso un utilizzo semplice agli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md). Gli utenti possono o meno visualizzare una richiesta di autenticazione MFA in base alle decisioni di configurazione prese da un amministratore.

## <a name="how-to-get-multi-factor-authentication"></a>Come ottenere Multi-Factor Authentication

Multi-Factor Authentication è inclusa in quanto parte delle offerte seguenti:

* **Azure Active Directory Premium** oppure **Microsoft 365 Business** -uso di Azure multi-Factor Authentication tramite criteri di accesso condizionale per richiedere l'autenticazione a più fattori con funzionalità complete.

* **Azure AD Free**, **Azure AD Basic**, o autonomo **Office 365** licenze - uso ha creato preventivamente [i criteri di protezione della linea di base di accesso condizionale](../conditional-access/concept-baseline-protection.md) in modo da richiedere multi-factor authentication per gli utenti e amministratori.

* **Amministratori globali di Azure Active Directory**: per proteggere gli account di amministratore globale è disponibile un subset di funzionalità di Azure multi-Factor Authentication.

> [!NOTE]
> I nuovi clienti non possono più acquistare Azure Multi-Factor Authentication come offerta autonoma valida dal 1 ° settembre 2018. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

## <a name="supportability"></a>Supporto

Poiché la maggior parte degli utenti è abituata a usare le password solo per l'autenticazione, è importante che l'organizzazione si metta in comunicazione con tutti gli utenti per informarli in merito al processo. La consapevolezza può ridurre la probabilità che gli utenti contattino l'help desk per problemi di lieve entità riguardanti l'autenticazione a più fattori. Esistono tuttavia alcuni scenari in cui è necessario disabilitare temporaneamente MFA. Per sapere come gestire questi scenari, usare le linee guida seguenti:

* Istruire il personale di supporto per le situazioni in cui l'utente non riesce ad accedere perché non ha accesso ai suoi metodi di autenticazione oppure perchè questi ultimi non funzionano correttamente.
   * Usando i criteri di accesso condizionale per il servizio Azure MFA, il personale di supporto può aggiungere un utente a un gruppo che è escluso da un criterio che richiedono il MFA.
* È consigliabile usare località denominate come un modo per ridurre al minimo la verifica in due passaggi richiede l'accesso condizionale. Con questa funzionalità, gli amministratori possono ignorare la verifica in due passaggi per gli utenti che accedono da una posizione sicura rete attendibile, ad esempio una rete segmento usato per l'onboarding di nuovi utenti.
* Distribuire [Azure AD Identity Protection](../active-directory-identityprotection.md) e attivare la verifica in due passaggi basata su eventi di rischio.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuzione passo per passo di Azure multi-Factor Authentication](howto-mfa-getstarted.md)
