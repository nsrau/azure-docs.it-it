---
title: Utilizzo di metodi di autenticazione & report di insights (anteprima) - Azure Active Directory
description: Creazione di report in modalità self-service password di Azure AD reset e utilizzo del metodo di autenticazione multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561034"
---
# <a name="authentication-methods-usage--insights-preview"></a>Utilizzo di metodi di autenticazione & insights (anteprima)

Utilizzo & insights consente di comprendere come funzionano i metodi di autenticazione per le funzionalità, ad esempio Azure multi-Factor Authentication e reimpostazione della password self-service all'interno dell'organizzazione. Questa funzionalità di creazione report offre all'organizzazione con i mezzi per comprendere quali sono i metodi in corso la registrazione e la modalità in uso.

## <a name="permissions-and-licenses"></a>Le autorizzazioni e licenze

Utilizzo e informazioni dettagliate sono reperibili i ruoli seguenti:

- Amministratore globale
- Ruolo con autorizzazioni di lettura per la sicurezza
- Amministratore della sicurezza
- Lettore di report

Nessuna licenza aggiuntiva è necessaria per l'utilizzo per l'accesso e informazioni dettagliate. Azure multi-Factor Authentication e informazioni sulle licenze di reimpostazione della password self-service è reperibile nella [Azure Active Directory sito sui prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Funzionamento

Per accedere alle informazioni e utilizzo del metodo di autenticazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory** > **reimpostazione della Password** > **utilizzo & insights**.
1. Dal **registrazione** oppure **utilizzo** panoramiche, è possibile scegliere di aprire la pre-filtrato i report per filtrare in base alle esigenze.

![Panoramica sull'utilizzo e insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Per accedere direttamente a utilizzo e informazioni dettagliate, visitare [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Questo collegamento consente di accedere alla panoramica della registrazione.

Gli utenti registrati, gli utenti abilitati e riquadri in grado di supportare utenti mostrano i dati di registrazione seguenti per gli utenti:

- Registrato: Un utente viene considerato registrato se essi (o un amministratore) have registrato i metodi di autenticazione sufficienti per soddisfare i criteri dell'organizzazione SSPR o multi-Factor Authentication.
- Abilitato: Un utente viene considerato abilitato se sono inclusi nell'ambito per i criteri di reimpostazione. Se SSPR è abilitato per un gruppo, quindi l'utente è considerato disponibile se si trovano in tale gruppo. Se SSPR è abilitato per tutti gli utenti, tutti gli utenti nel tenant (esclusi i guest) sono considerati abilitata.
- In grado di supportare: Un utente viene considerato capace se vengono sia registrati e abilitati. Questo stato indica che essi possono eseguire SSPR in qualsiasi momento se necessario.

Facendo clic su uno di questi riquadri o i dati dettagliati visualizzati in essi si accede a un elenco pre-filtrato dei dettagli di registrazione.

Il **registrazioni** grafico sul **registrazione** scheda Mostra il numero di autenticazione riuscito e non le registrazioni di metodo dal metodo di autenticazione. Il **Reimposta** grafico sulle **utilizzo** scheda Mostra il numero di esito positivo e flusso di reimpostazione della non riusciti di autenticazione durante la password dal metodo di autenticazione.

Facendo clic su uno dei grafici saranno consentono di ottenere un elenco di registrazione pre-filtrato o eventi di reimpostazione.

Utilizzo del controllo nell'angolo superiore, destro, è possibile modificare l'intervallo di date per i dati di controllo visualizzati nei grafici le registrazioni e reimposta a 24 ore, 7 giorni o 30 giorni.

I dati di registrazione dal 

### <a name="registration-details"></a>Dettagli di registrazione

Facendo clic sui **gli utenti registrati**, **utenti abilitati**, o **gli utenti in grado di supportare** insights o riquadri verrà consentono di ottenere i dettagli di registrazione.

Report dettagli della registrazione Visualizza le informazioni seguenti per ogni utente:

- NOME
- Nome utente
- Lo stato della registrazione (All, registrato, non è registrato)
- Stato abilitato (All, abilitato, non è abilitato)
- Lo stato in grado di supportare (All, Capable, non in grado di supportare)
- Metodi (notifica dell'App, codice dell'App, telefonata, SMS, posta elettronica, domande di sicurezza)

Utilizzo dei controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

### <a name="reset-details"></a>Reimpostare i dettagli

Facendo clic sui grafici delle registrazioni o la reimpostazione si accede ai dettagli della reimpostazione.

Report dettagli della reimpostazione Mostra gli eventi di registrazione e reimpostazione degli ultimi 30 giorni inclusi:

- NOME
- Nome utente
- Funzionalità (All, registrazione, Reimposta)
- Metodo di autenticazione (notifica dell'App, codice dell'App, chiamata telefonica, chiamata di Office, SMS, posta elettronica, domande di sicurezza)
- Stato (tutti, esito positivo, errore)

Utilizzo dei controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

## <a name="limitations"></a>Limitazioni

I dati visualizzati in questi rapporti verranno ritardati fino a 60 minuti. Esiste un campo "Ultimo aggiornamento" nel portale di Azure per identificare i dati come recenti siano.

Dati di utilizzo e insights non sono una sostituzione per il report delle attività di Azure multi-Factor Authentication o le informazioni contenute nel report degli accessi di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Utilizzo con il report di utilizzo di metodi di autenticazione API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Scelta dei metodi di autenticazione per l'organizzazione](concept-authentication-methods.md)
- [Combinare l'esperienza di registrazione](concept-registration-mfa-sspr-combined.md)
