---
title: Cos'è Azure Active Directory Identity Protection?
description: Rilevare, correggere, esaminare e analizzare i rischi con Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2dbb47371197d07b45f9144e2be66e992df4c97
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996526"
---
# <a name="what-is-identity-protection"></a>Informazioni su Identity Protection

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

| Tipo di rilevamento dei rischi | Descrizione |
| --- | --- |
| Trasferimento atipico | Accesso da una posizione insolita in base agli accessi recenti dell'utente. |
| Indirizzo IP anonimo | Accesso da indirizzo IP anonimo (ad esempio Tor Browser, VPN per navigazione in anonimato). |
| Proprietà di accesso insolite | Accesso con proprietà non osservate di recente per l'utente specificato. |
| Indirizzo IP collegato a malware | Accesso da indirizzo IP collegato a malware. |
| Credenziali perse | Indica che le credenziali valide dell'utente sono andate perse. |
| Password spraying | Indica che è in corso un attacco unificato di forza bruta contro più nomi utente usando password comuni. |
| Intelligence per le minacce di Azure AD | Le origini di intelligence Microsoft per le minacce interne ed esterne hanno identificato uno schema di attacco noto. |

Per maggiori dettagli su questi rischi e su come/quando vengono calcolati, vedere l'articolo [Che cosa sono i rischi?](concept-identity-protection-risks.md).

I segnali di rischio possono attivare interventi di correzione, ad esempio richiedere agli utenti di eseguire Azure Multi-Factor Authentication, reimpostare la password usando la reimpostazione della password self-service o bloccare l'operazione finché un amministratore non interviene.

## <a name="risk-investigation"></a>Analisi degli eventi di rischio

Gli amministratori possono esaminare i rilevamenti e, se necessario, intervenire manualmente. Sono tre i report chiave a disposizione degli amministratori per le analisi in Identity Protection:

- Utenti a rischio
- Accessi a rischio
- Rilevamenti dei rischi

Per altre informazioni, vedere l'articolo [Procedura: Analizzare i rischi](howto-identity-protection-investigate-risk.md).

### <a name="risk-levels"></a>Livelli di rischio

Identity Protection classifica il rischio in tre livelli: basso, medio e alto. 

Microsoft non fornisce dettagli specifici sul modo in cui viene calcolato il rischio, ma ogni livello comporta una maggiore probabilità che l'utente o l'accesso sia compromesso. Ad esempio, un'istanza di proprietà di accesso insolite per un utente potrebbe non essere pericolosa quanto le credenziali perse per un altro utente.

## <a name="exporting-risk-data"></a>Esportazione dei dati relativi ai rischi

I dati ottenuti con Identity Protection possono essere esportati in altri strumenti per l'archiviazione, oltre che per ulteriori analisi e per stabilire correlazioni. Le API basate su Microsoft Graph consentono alle organizzazioni di raccogliere questi dati per elaborarle ulteriormente in un altro strumento, ad esempio uno strumento Security Information and Event Management. Per informazioni su come accedere all'API di Identity Protection, vedere l'articolo [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](howto-identity-protection-graph-api.md).

Per informazioni sull'integrazione delle informazioni di Identity Protection con Azure Sentinel, vedere l'articolo [Connettere i dati da Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Autorizzazioni

Per poter accedere, Identity Protection richiede che gli utenti abbiano uno dei ruoli seguenti: Ruolo con autorizzazioni di lettura per la sicurezza, Operatore per la sicurezza, Ruolo con autorizzazioni di lettura globali o Amministratore globale.

| Ruolo | Operazione consentita | Non è possibile |
| --- | --- | --- |
| Amministratore globale | Accesso completo a Identity Protection |   |
| Amministratore della sicurezza | Accesso completo a Identity Protection | Reimpostare la password di un utente |
| Operatore per la sicurezza | Visualizzazione di tutti i report di Identity Protection e del pannello Panoramica <br><br> Ignorare i rischi per gli utenti, confermare l'accesso sicuro, confermare la compromissione | Configurare o cambiare i criteri <br><br> Reimpostare la password di un utente <br><br> Configurare gli avvisi |
| Ruolo con autorizzazioni di lettura per la sicurezza | Visualizzazione di tutti i report di Identity Protection e del pannello Panoramica | Configurare o cambiare i criteri <br><br> Reimpostare la password di un utente <br><br> Configurare gli avvisi <br><br> Inviare feedback sui rilevamenti |

Attualmente, il ruolo di operatore per la sicurezza non può accedere al report degli accessi a rischio.

Gli amministratori dell'accesso condizionale possono anche creare criteri che tengono conto del rischio di accesso come condizione. Per altre informazioni, vedere l'articolo [Accesso condizionale: condizioni](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Funzionalità | Dettagli  | Azure AD Free/App di Microsoft 365 | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| Criteri di rischio | Criteri di rischio utente (tramite Identity Protection)  | No | No |Sì | 
| Criteri di rischio | Criteri di rischio di accesso (tramite Identity Protection o accesso condizionale)  | No |  No |Sì |
| Report sulla sicurezza | Panoramica |  No | No |Sì |
| Report sulla sicurezza | Utenti a rischio  | Informazioni limitate | Informazioni limitate | Accesso completo|
| Report sulla sicurezza | Accessi a rischio  | Informazioni limitate | Informazioni limitate | Accesso completo|
| Report sulla sicurezza | Rilevamenti dei rischi   | No | Informazioni limitate| Accesso completo|
| Notifiche | Avvisi relativi a utenti a rischio rilevati  | No | No |Sì |
| Notifiche | Digest settimanale| No | No | Sì | 
| | Criteri di registrazione MFA | No | No | Sì |

Altre informazioni su questi report avanzati sono reperibili nell'articolo [Procedura: Analizzare i rischi](howto-identity-protection-investigate-risk.md#navigating-the-reports).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della sicurezza](concept-identity-protection-security-overview.md)

- [Che cosa sono i rischi?](concept-identity-protection-risks.md)

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)
