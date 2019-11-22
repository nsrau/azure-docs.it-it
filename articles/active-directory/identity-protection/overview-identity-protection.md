---
title: Cos'è Azure Active Directory Identity Protection?
description: Rilevare, correggere, esaminare e analizzare i rischi con Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887701"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Cos'è Azure Active Directory Identity Protection?

Identity Protection è uno strumento che consente alle organizzazioni di eseguire tre attività principali:

- Automatizzare il rilevamento e la correzione dei rischi basati sull'identità.
- Esaminare i rischi usando i dati disponibili nel portale.
- Esportare i dati di rilevamento dei rischi in utilità di terze parti per analizzarli ulteriormente.

Identity Protection usa le informazioni acquisite da Microsoft dalla propria posizione in organizzazioni che usano Azure AD, nello spazio consumer con account Microsoft e nelle piattaforme di gioco con Xbox per proteggere gli utenti. Microsoft analizza 6,5 mila miliardi di segnali al giorno per identificare le minacce e proteggere i clienti.

I segnali generati e inviati a Identity Protection possono essere ulteriormente inseriti in strumenti come l'accesso condizionale per prendere decisioni relative agli accessi o restituiti a uno strumento Security Information and Event Management per un'analisi più approfondita a seconda dei criteri applicati dall'organizzazione.

## <a name="why-is-automation-important"></a>Perché l'automazione è importante?

Nel [post di blog di ottobre 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, a capo del team Identity Security and Protection di Microsoft, spiega perché l'automazione è così importante quando si gestiscono numerosi eventi:

> Ogni giorno i sistemi di apprendimento automatico ed euristico forniscono punteggi di rischio per 18 miliardi di tentativi di accesso eseguiti da oltre 800 milioni di account distinti, 300 milioni dei quali sono visibilmente eseguiti da utenti malintenzionati, ovvero entità quali criminalità e hacker.
>
> In occasione dell'evento Ignite l'anno scorso ho parlato dei tre attacchi più frequenti ai nostri sistemi di gestione dell'identità. Ecco il volume recente di questi attacchi
>   
>   - **Ripetizione della violazione**: 4,6 miliardi di attacchi rilevati nel mese di maggio 2018
>   - **Password spraying**: 350.000 nel mese di aprile 2018
>   - **Phishing**: questo valore è difficilmente quantificabile con esattezza, ma abbiamo riscontrato 23 milioni di eventi di rischio nel mese di marzo 2018, molti dei quali sono correlati al phishing

## <a name="risk-detection-and-remediation"></a>Rilevamento e correzione dei rischi

Identity Protection consente di identificare i rischi suddivisi nelle classificazioni seguenti:

| Tipo di rilevamento dei rischi | DESCRIZIONE |
| --- | --- |
| Trasferimento atipico | Accesso da una posizione insolita in base agli accessi recenti dell'utente. |
| Indirizzo IP anonimo | Accesso da indirizzo IP anonimo (ad esempio Tor Browser, VPN per navigazione in anonimato). |
| Proprietà di accesso insolite | Accesso con proprietà non osservate di recente per l'utente specificato. |
| Indirizzo IP collegato a malware | Accesso da indirizzo IP collegato a malware |
| Credenziali perse | Questo rilevamento dei rischi indica che le credenziali valide dell'utente sono andate perse |
| Intelligence per le minacce di Azure AD | Le origini di intelligence Microsoft per le minacce interne ed esterne hanno identificato uno schema di attacco noto. |

Per maggiori dettagli su questi rischi e su come/quando vengono calcolati, vedere l'articolo [Che cosa sono i rischi?](concept-identity-protection-risks.md).

I segnali di rischio possono attivare interventi di correzione, ad esempio richiedere agli utenti di eseguire Azure Multi-Factor Authentication, reimpostare la password usando la reimpostazione della password self-service o bloccare l'operazione finché un amministratore non interviene.

## <a name="risk-investigation"></a>Analisi degli eventi di rischio

Gli amministratori possono esaminare i rilevamenti e, se necessario, intervenire manualmente. Sono tre i report chiave a disposizione degli amministratori per le analisi in Identity Protection:

- Utenti a rischio
- Accessi a rischio
- Rilevamenti dei rischi

Per altre informazioni, vedere l'articolo [Procedura: Analizzare i rischi](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Esportazione dei dati relativi ai rischi

I dati ottenuti con Identity Protection possono essere esportati in altri strumenti per l'archiviazione, oltre che per ulteriori analisi e per stabilire correlazioni. Le API basate su Microsoft Graph consentono alle organizzazioni di raccogliere questi dati per elaborarle ulteriormente in un altro strumento, ad esempio uno strumento Security Information and Event Management. Per informazioni su come accedere all'API di Identity Protection, vedere l'articolo [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](howto-identity-protection-graph-api.md).

Per informazioni sull'integrazione delle informazioni di Identity Protection con Azure Sentinel, vedere l'articolo [Connettere i dati da Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Autorizzazioni

Per poter accedere, Identity Protection richiede che gli utenti abbiano uno dei ruoli seguenti: Ruolo con autorizzazioni di lettura per la sicurezza, Operatore per la sicurezza, Ruolo con autorizzazioni di lettura globali o Amministratore globale.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Funzionalità | Dettagli | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Gratuito |
| --- | --- | --- | --- | --- |
| Criteri di rischio | Criteri di rischio utente (tramite Identity Protection) | Sì | No | No |
| Criteri di rischio | Criteri di rischio di accesso (tramite Identity Protection o accesso condizionale) | Sì | No | No |
| Report sulla sicurezza | Panoramica | Sì | No | No |
| Report sulla sicurezza | Utenti a rischio | Accesso completo | Informazioni limitate | Informazioni limitate |
| Report sulla sicurezza | Accessi a rischio | Accesso completo | Informazioni limitate | Informazioni limitate |
| Report sulla sicurezza | Rilevamenti dei rischi | Accesso completo | Informazioni limitate | No |
| Notifiche | Avvisi relativi a utenti a rischio rilevati | Sì | No | No |
| Notifiche | Digest settimanale | Sì | No | No |
| | Criteri di registrazione MFA | Sì | No | No |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della sicurezza](concept-identity-protection-security-overview.md)

- [Che cosa sono i rischi?](concept-identity-protection-risks.md)

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)
