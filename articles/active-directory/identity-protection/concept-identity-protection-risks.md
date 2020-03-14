---
title: Che cosa sono i rischi? Azure AD Identity Protection
description: Spiegazione del rischio in Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253689"
---
# <a name="what-is-risk"></a>Che cosa sono i rischi?

I rilevamenti dei rischi in Azure AD Identity Protection includono eventuali azioni sospette identificate relative agli account utente nella directory.

Identity Protection consente alle organizzazioni di accedere a risorse potenti per visualizzare e rispondere rapidamente a tali azioni sospette. 

![Panoramica della sicurezza che Mostra utenti e accessi a rischio](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipi di rischio e rilevamento

Esistono due tipi di **utente** di rischio e di **accesso** e due tipi di rilevamento o calcolo in **tempo reale** e **offline**.

### <a name="user-risk"></a>Rischio utente

Un rischio utente rappresenta la probabilità che un'identità o un account specificato venga compromesso. 

Questi rischi vengono calcolati offline usando le origini di intelligence per le minacce interne ed esterne di Microsoft, inclusi i ricercatori della sicurezza, i professionisti delle forze legali, i team di sicurezza di Microsoft e altre fonti attendibili.

| Rilevamento del rischio | Descrizione |
| --- | --- |
| Credenziali perse | Questo tipo di rilevamento del rischio indica che le credenziali valide dell'utente sono state perse. Quando i criminali informatici compromettono password valide di utenti legittimi, spesso condividono tali credenziali, Questa condivisione viene in genere effettuata pubblicando sul Web scuro, incollando i siti oppure negoziando e vendendo le credenziali sul mercato nero. Quando il servizio credenziali di Microsoft ha acquisito le credenziali utente dal Web scuro, incollare i siti o altre origini, viene verificata la presenza di Azure AD credenziali valide correnti degli utenti per trovare corrispondenze valide. |
| Intelligence per le minacce di Azure AD | Questo tipo di rilevamento del rischio indica che l'attività dell'utente è insolita per l'utente specificato o è coerente con i modelli di attacco noti basati sulle fonti di intelligence per le minacce interne ed esterne di Microsoft. |

### <a name="sign-in-risk"></a>Rischio di accesso

Un rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. 

Questi rischi possono essere calcolati in tempo reale o calcolati offline usando le origini di intelligence per le minacce interne ed esterne di Microsoft, inclusi i ricercatori della sicurezza, i professionisti delle autorità giudiziarie, i team addetti alla sicurezza Microsoft e altre fonti attendibili.

| Rilevamento del rischio | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Indirizzo IP anonimo | Tempo reale | Questo tipo di rilevamento dei rischi indica gli accessi da un indirizzo IP anonimo, ad esempio Tor Browser o una VPN anonima. Questi indirizzi IP sono in genere usati da attori che vogliono nascondere i dati di telemetria relativi agli accessi (indirizzo IP, posizione, dispositivo e così via) con intenzioni potenzialmente dannose. |
| Trasferimento atipico | Offline | Questo tipo di rilevamento dei rischi identifica due accessi provenienti da località geograficamente distanti, in cui almeno una delle posizioni può essere atipica per l'utente, dato il comportamento precedente. Oltre ad altri fattori, l'algoritmo di apprendimento automatico prende in considerazione il tempo che intercorre tra i due accessi e il tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, che indica che un altro utente sta usando le stesse credenziali. <br><br> L'algoritmo ignora i "falsi positivi" evidenti che contribuiscono alle condizioni di impossibilità del trasferimento, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale dei primi 14 giorni o 10 accessi, durante i quali apprende il comportamento di accesso di un nuovo utente. |
| Indirizzo IP collegato a malware | Offline | Questo tipo di rilevamento dei rischi indica gli accessi dagli indirizzi IP infetti da malware noto per la comunicazione attiva con un server bot. Questo rilevamento è determinato dalla correlazione tra gli indirizzi IP del dispositivo dell'utente e gli indirizzi IP che erano in contatto con un server bot mentre il server bot era attivo. |
| Proprietà di accesso insolite | Tempo reale | Questo tipo di rilevamento dei rischi considera la cronologia di accesso precedente (IP, Latitudine/Longitudine e ASN) per la ricerca di accessi anomali. Il sistema archivia le informazioni sui percorsi precedenti usati da un utente e considera tali percorsi "noti". Il rilevamento dei rischi viene attivato quando viene eseguito l'accesso da una posizione non ancora presente nell'elenco di posizioni note. Gli utenti appena creati saranno in "modalità di apprendimento" per un periodo di tempo in cui i rilevamenti dei rischi delle proprietà di accesso non note verranno spenti mentre gli algoritmi apprendono il comportamento dell'utente. La durata della modalità di apprendimento è dinamica e dipende dalla quantità di tempo impiegato dall'algoritmo per raccogliere informazioni sufficienti sui modelli di accesso dell'utente. La durata minima è di cinque giorni. Un utente può tornare alla modalità di apprendimento dopo un lungo periodo di inattività. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. <br><br> È anche possibile eseguire questo rilevamento per l'autenticazione di base (o i protocolli legacy). Poiché questi protocolli non hanno proprietà moderne, ad esempio l'ID client, i dati di telemetria per ridurre i falsi positivi sono limitati. È consigliabile passare all'autenticazione moderna. |
| Utente confermato dall'amministratore compromesso | Offline | Questo rilevamento indica che un amministratore ha selezionato ' conferma utente compromesso ' nell'interfaccia utente Risky Users o usando l'API riskyUsers. Per verificare quale amministratore ha confermato la compromissione dell'utente, controllare la cronologia dei rischi dell'utente (tramite interfaccia utente o API). |
| Indirizzo IP dannoso | Offline | Questo rilevamento indica l'accesso da un indirizzo IP dannoso. Un indirizzo IP viene considerato dannoso in base a frequenze di errore elevate a causa di credenziali non valide ricevute dall'indirizzo IP o da altre origini della reputazione IP. |
| Regole di manipolazione della posta in arrivo sospette | Offline | Questo rilevamento viene individuato da [Microsoft cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Questo rilevamento profila l'ambiente e attiva gli avvisi quando le regole sospette che eliminano o spostano i messaggi o le cartelle vengono impostate nella posta in arrivo di un utente. Questo può indicare che l'account dell'utente è compromesso, che i messaggi sono intenzionalmente nascosti e che la cassetta postale viene utilizzata per distribuire posta indesiderata o malware nell'organizzazione. |
| Viaggio impossibile | Offline | Questo rilevamento viene individuato da [Microsoft cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Questo rilevamento identifica due attività utente (in una singola sessione o in più sessioni) provenienti da località geograficamente distanti e realizzate in un intervallo di tempo più breve rispetto a quello che sarebbe stato necessario all'utente per spostarsi dalla prima alla seconda località. Si tratta pertanto di un altro utente che sta usando le stesse credenziali. |

### <a name="other-risk-detections"></a>Altri rilevamenti di rischio

| Rilevamento del rischio | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Rilevato rischio aggiuntivo | In tempo reale o offline | Questo rilevamento indica che è stato rilevato uno dei rilevamenti Premium precedenti. Poiché i rilevamenti Premium sono visibili solo ai clienti Azure AD Premium P2, sono intitolati "rischi aggiuntivi rilevati" per i clienti senza Azure AD Premium le licenze P2. |

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)

- [Panoramica della sicurezza](concept-identity-protection-security-overview.md)
