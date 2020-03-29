---
title: Metodi di autenticazione utilizzo & informazioni dettagliate - Azure Active DirectoryAuthentication methods usage & insights - Azure Active Directory
description: Creazione di report sulla reimpostazione della password self-service di Azure AD e sull'utilizzo del metodo di autenticazione Multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b448e6ce7c8b4522d5e7bdbafb39eccca982fdee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848511"
---
# <a name="authentication-methods-usage--insights-preview"></a>Metodi di autenticazione utilizzo & informazioni dettagliate (anteprima)Authentication methods usage & insights (preview)

Le informazioni dettagliate sull'utilizzo & consentono di comprendere il funzionamento dei metodi di autenticazione per funzionalità come Azure Multi-Factor Authentication e la reimpostazione della password self-service nell'organizzazione. Questa funzionalità di reporting fornisce all'organizzazione i mezzi per comprendere quali metodi vengono registrati e come vengono utilizzati.

## <a name="permissions-and-licenses"></a>Autorizzazioni e licenze

I ruoli seguenti possono accedere all'utilizzo e alle informazioni dettagliate:The following roles can access usage and insights:

- Amministratore globale
- Ruolo con autorizzazioni di lettura per la sicurezza
- Amministratore della sicurezza
- Lettore di report

Non sono necessarie licenze aggiuntive per accedere all'utilizzo e alle informazioni dettagliate. Le informazioni sulle licenze per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR) sono disponibili nel sito dei prezzi di [Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="how-it-works"></a>Funzionamento

Per accedere all'utilizzo e alle informazioni dettagliate del metodo di autenticazione:To access authentication method usage and insights:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare alla**reimpostazione della password di** >  **Azure Active Directory** > **Utilizzo & informazioni dettagliate.**
1. Dalle panoramiche **di registrazione** o **utilizzo,** è possibile scegliere di aprire i report prefiltrati da filtrare in base alle proprie esigenze.

![Panoramica delle informazioni dettagliate sull'& sull'utilizzo](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Per accedere direttamente all'utilizzo [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)& informazioni dettagliate, passare a . Questo link ti porterà alla panoramica della registrazione.

I riquadri Utenti registrati, Utenti abilitati e Utenti in grado mostrano i seguenti dati di registrazione per gli utenti:

- Registrato: un utente viene considerato registrato se (o un amministratore) ha registrato un numero sufficiente di metodi di autenticazione per soddisfare i criteri SSPR o Multi-Factor Authentication dell'organizzazione.
- Abilitato: un utente viene considerato abilitato se si trova nell'ambito del criterio SSPR. Se SSPR è abilitato per un gruppo, l'utente viene considerato abilitato se fa parte di tale gruppo. Se SSPR è abilitato per tutti gli utenti, tutti gli utenti nel tenant (esclusi gli ospiti) vengono considerati abilitati.
- Capable: un utente è considerato capace se è sia registrato che abilitato. Questo stato significa che possono eseguire SSPR in qualsiasi momento, se necessario.

Cliccando su uno di questi riquadri o le intuizioni mostrate in essi vi porterà a un elenco pre-filtrato di dettagli di registrazione.

Il grafico **Registrazioni** nella scheda **Registrazione** mostra il numero di registrazioni del metodo di autenticazione riuscite e non riuscite in base al metodo di autenticazione. Il grafico **Reimposta** nella scheda **Utilizzo** mostra il numero di autenticazioni riuscite e non riuscite durante il flusso di reimpostazione della password tramite il metodo di autenticazione.

Cliccando su uno dei grafici si porterà a un elenco pre-filtrato di eventi di registrazione o reset.

Utilizzando il controllo nell'angolo superiore destro, è possibile modificare l'intervallo di date per i dati di controllo visualizzati nei grafici Registrazioni e Reimposta a 24 ore, 7 giorni o 30 giorni.

### <a name="registration-details"></a>Dettagli di registrazione

Cliccando su **Utenti registrati**, **Utenti abilitati**, o Utenti **in grado** piastrelle o approfondimenti vi porterà ai dettagli di registrazione.

Il report dei dettagli di registrazione mostra le seguenti informazioni per ogni utente:

- Nome
- Nome utente
- Stato registrazione (Tutto, Registrato, Non registrato)
- Stato Abilitato (Tutto, Abilitato, Non abilitato)
- Stato capace (Tutto, Capace, Non capace)
- Metodi (notifica dell'app, codice dell'app, telefonata, SMS, e-mail, domande di sicurezza)

Utilizzando i controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

### <a name="reset-details"></a>Reimpostare i dettagli

Cliccando sui grafici Registrazioni o Reimposta si raggiungeranno i dettagli di ripristino.

Il rapporto dei dettagli di reimpostazione mostra gli eventi di registrazione e reimpostazione degli ultimi 30 giorni, tra cui:

- Nome
- Nome utente
- Caratteristica (Tutto, Registrazione, Reimposta)
- Metodo di autenticazione (notifica dell'app, codice dell'app, telefonata, chiamata di Office, SMS, e-mail, domande di sicurezza)
- Stato (Tutto, Operazione riuscita, Errore)

Utilizzando i controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

## <a name="limitations"></a>Limitazioni

I dati mostrati in questi rapporti saranno ritardati fino a 60 minuti. Nel portale di Azure è presente un campo "Ultimo aggiornamento" per identificare la recente analisi dei dati.

I dati di utilizzo e di informazioni dettagliate non sostituiscono i report attività di Azure Multi-Factor Authentication o le informazioni contenute nel report di accesso di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Utilizzo dei metodi di autenticazione dell'API del report di utilizzo](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Scelta dei metodi di autenticazione per l'organizzazione](concept-authentication-methods.md)
- [Esperienza di registrazione combinata](concept-registration-mfa-sspr-combined.md)
