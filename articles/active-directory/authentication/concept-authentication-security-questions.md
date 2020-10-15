---
title: Metodo di autenticazione per domande di sicurezza-Azure Active Directory
description: Per informazioni sull'uso di domande di sicurezza, vedere Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42ba860c3174ad8725dd15ac3045d270524b110e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965472"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Metodi di autenticazione in Azure Active Directory-domande di sicurezza

Le domande di sicurezza non vengono usate come metodo di autenticazione durante un evento di accesso. Possono invece essere usate durante il processo di reimpostazione della password self-service per confermare l'identità dell'utente. Gli account amministratore non possono usare le domande di sicurezza come metodo di verifica con la reimpostazione della password self-service.

Quando gli utenti si registrano per la reimpostazione della password self-service, viene richiesto loro di scegliere i metodi di autenticazione da usare. Se decidono di usare le domande di sicurezza, scelgono tra una serie di domande e quindi inseriscono le proprie risposte.

![Screenshot del portale di Azure che mostra i metodi di autenticazione e le opzioni per le domande di sicurezza](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. All'amministratore non è consentito leggere o modificare le domande e le risposte di un utente.

Le domande di sicurezza possono risultare meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande di un altro utente. Se vengono usate le domande di sicurezza con la reimpostazione della password self-service, è consigliabile combinare questo metodo con un altro. A un utente può essere richiesto di usare l'app Microsoft Authenticator oppure l'autenticazione tramite telefono per confermare la propria identità durante il processo di reimpostazione della password self-service e di scegliere le domande di sicurezza solo se non ha il telefono o il dispositivo registrato a portata di mano.

## <a name="predefined-questions"></a>Domande predefinite

Le domande di sicurezza predefinite seguenti sono disponibili per essere usate come metodo di verifica con la reimpostazione della password self-service. Tutte queste domande di sicurezza vengono convertite e localizzate nel set completo di linguaggi Microsoft 365 in base alle impostazioni locali del browser dell'utente:

* In quale città hai incontrato tuo marito o il tuo partner?
* In quale città si sono incontrati i tuoi genitori?
* In quale città vive tuo fratello/sorella più vicino/a?
* In quale città è nato tuo padre?
* In quale città hai avuto il tuo primo lavoro?
* In quale città è nata tua madre?
* In quale città hai trascorso il Capodanno del 2000?
* Qual è il cognome del tuo insegnante preferito delle scuole superiori?
* Qual è il nome di una delle università a cui hai inviato una richiesta di iscrizione ma che non hai frequentato?
* In quale luogo si è tenuto il tuo primo ricevimento di matrimonio?
* Qual è il secondo nome di tuo padre?
* Qual è il tuo piatto preferito?
* Qual è il nome e il cognome della nonna materna?
* Qual è il secondo nome di tua madre?
* Qual è l'anno e il mese di nascita di tuo/a fratello/sorella maggiore? (ad esempio, novembre 1985)
* Qual è il secondo nome di tuo/a fratello/sorella maggiore?
* Qual è il nome e il cognome del nonno paterno?
* Qual è il secondo nome di tuo/a fratello/sorella minore?
* Quale scuola hai frequentato in prima media?
* Qual è il nome e il cognome del tuo/a migliore amico/a di infanzia?
* Qual è il nome e il cognome del primo/a fidanzato/a?
* Qual è il nome del tuo insegnante preferito delle scuole elementari?
* Qual è la marca e il modello della tua prima auto o moto?
* Qual è il nome della prima scuola che hai frequentato?
* Qual è il nome dell'ospedale in cui sei nato?
* Qual è il nome della via della tua prima casa di infanzia?
* Qual è il nome del tuo supereroe preferito dell'infanzia?
* Qual è il nome del tuo animale di peluche preferito?
* Qual è il nome del primo animale domestico?
* Qual era il tuo soprannome da bambino?
* Qual è il tuo sport preferito alle scuole superiori?
* Qual è stato il tuo primo lavoro?
* Quali erano le ultime quattro cifre del tuo numero di telefono quando eri bambino/a?
* Cosa volevi fare da grande quando eri piccolo?
* Qual è la persona più famosa che hai mai incontrato?

## <a name="custom-security-questions"></a>Domande di sicurezza personalizzate

Per una maggiore flessibilità, è possibile definire domande di sicurezza personalizzate. La lunghezza massima di una domanda di sicurezza personalizzata è di 200 caratteri.

Le domande di sicurezza personalizzate non vengono localizzate automaticamente come avviene con le domande di sicurezza predefinite. Tutte le domande personalizzate vengono visualizzate nella lingua in cui vengono immesse nell'interfaccia utente di amministrazione, anche se le impostazioni locali del browser dell'utente sono diverse. Se si desidera disporre domande localizzate, è consigliabile usare le domande predefinite.

## <a name="security-question-requirements"></a>Requisiti della domanda di sicurezza

Sia per le domande di sicurezza predefinite che per quelle personalizzate, si applicano i requisiti e le limitazioni seguenti:

* Il limite minimo di caratteri della risposta è 3 caratteri.
* Il limite massimo di caratteri della risposta è 40 caratteri.
* Gli utenti non possono rispondere alla stessa domanda due volte.
* Gli utenti non possono dare la stessa risposta a due diverse domande.
* È possibile usare qualsiasi set di caratteri per definire domande e risposte, compresi i caratteri Unicode.
* Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l' [esercitazione per la reimpostazione della password self-service (SSPR)][tutorial-sspr].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Altre informazioni sulla configurazione dei metodi di autenticazione con l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
