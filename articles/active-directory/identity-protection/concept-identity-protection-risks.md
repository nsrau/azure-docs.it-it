---
title: Che cosa sono i rischi? Azure AD Identity Protection
description: Spiegazione del rischio in Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bc7b0ce521522e677e0dc53809c8c33e0743f0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327919"
---
# <a name="what-is-risk"></a>Che cosa sono i rischi?

I rilevamenti dei rischi in Azure AD Identity Protection includono eventuali azioni sospette identificate relative agli account utente nella directory.

Identity Protection consente alle organizzazioni di accedere a risorse potenti per visualizzare e rispondere rapidamente a tali azioni sospette. 

![Panoramica della sicurezza che Mostra utenti e accessi a rischio](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipi di rischio e rilevamento

Esistono due tipi di **utente** di rischio e di **accesso** e due tipi di rilevamento o calcolo in **tempo reale** e **offline**.

I rilevamenti in tempo reale potrebbero non essere visualizzati nella creazione di report da cinque a dieci minuti. I rilevamenti offline potrebbero non essere visualizzati nella creazione di report per due o ventiquattro ore.

### <a name="user-risk"></a>Rischio utente

Un rischio utente rappresenta la probabilità che un'identità o un account specificato venga compromesso. 

Questi rischi vengono calcolati offline usando le origini di intelligence per le minacce interne ed esterne di Microsoft, inclusi i ricercatori della sicurezza, i professionisti delle forze legali, i team di sicurezza di Microsoft e altre fonti attendibili.

| Rilevamento del rischio | Descrizione |
| --- | --- |
| Credenziali perse | Questo tipo di rilevamento del rischio indica che le credenziali valide dell'utente sono state perse. Quando i criminali informatici compromettono password valide di utenti legittimi, spesso condividono tali credenziali, Questa condivisione viene in genere effettuata pubblicando sul Web scuro, incollando i siti oppure negoziando e vendendo le credenziali sul mercato nero. Quando il servizio credenziali di Microsoft ha acquisito le credenziali utente dal Web scuro, incollare i siti o altre origini, viene verificata la presenza di Azure AD credenziali valide correnti degli utenti per trovare corrispondenze valide. Per ulteriori informazioni sulle credenziali perse, vedere [domande comuni](#common-questions). |
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
| Regole di manipolazione della posta in arrivo sospette | Offline | Questo rilevamento viene individuato da [Microsoft cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Questo criterio di rilevamento profila l'ambiente e genera avvisi quando nella Posta in arrivo di un utente vengono configurate regole sospette per eliminare o spostare messaggi o cartelle. Questo rilevamento può indicare che l'account dell'utente è compromesso, che i messaggi sono intenzionalmente nascosti e che la cassetta postale viene utilizzata per distribuire posta indesiderata o malware nell'organizzazione. |
| Password spraying | Offline | Un attacco con spray per la password è la posizione in cui vengono attaccati più nomi utente usando password comuni in modo unificato per la forza bruta per ottenere accessi non autorizzati. Questo rilevamento del rischio viene attivato quando viene eseguito un attacco di spray per la password. |
| Comunicazione impossibile | Offline | Questo rilevamento viene individuato da [Microsoft cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Questo rilevamento identifica due attività utente (in una singola sessione o in più sessioni) provenienti da località geograficamente distanti e realizzate in un intervallo di tempo più breve rispetto a quello che sarebbe stato necessario all'utente per spostarsi dalla prima alla seconda località. Si tratta pertanto di un altro utente che sta usando le stesse credenziali. |

### <a name="other-risk-detections"></a>Altri rilevamenti di rischio

| Rilevamento del rischio | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Rilevato rischio aggiuntivo | In tempo reale o offline | Questo rilevamento indica che è stato rilevato uno dei rilevamenti Premium precedenti. Poiché i rilevamenti Premium sono visibili solo ai clienti Azure AD Premium P2, sono intitolati "rischi aggiuntivi rilevati" per i clienti senza Azure AD Premium le licenze P2. |

## <a name="common-questions"></a>Domande frequenti

### <a name="risk-levels"></a>Livelli di rischio

Identity Protection classifica il rischio in tre livelli: basso, medio e alto. 

Microsoft non fornisce dettagli specifici sul modo in cui viene calcolato il rischio, ma ogni livello comporta una maggiore probabilità che l'utente o l'accesso sia compromesso. Ad esempio, un'istanza di proprietà di accesso insolite per un utente potrebbe non essere pericolosa quanto le credenziali perse per un altro utente.

### <a name="leaked-credentials"></a>Credenziali perse

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Dove Microsoft trova le credenziali perse?

Microsoft individua le credenziali perse in diverse posizioni, tra cui:

- Pubblica i siti di Incolla, ad esempio pastebin.com e paste.ca, in cui gli attori cattivi in genere pubblicano tale materiale. Questo percorso è la maggior parte degli attori malintenzionati a cercare le credenziali rubate.
- Agenzie di applicazione della legge.
- Altri gruppi di Microsoft che stanno eseguendo ricerche web scure.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Perché non si vedono le credenziali perse?

Le credenziali perse vengono elaborate ogni volta che Microsoft trova un nuovo batch disponibile pubblicamente. A causa della natura sensibile, le credenziali perse vengono eliminate poco dopo l'elaborazione. Solo le nuove credenziali perse trovate dopo l'abilitazione della sincronizzazione dell'hash delle password (pH) verranno elaborate nel tenant. La verifica delle coppie di credenziali individuate in precedenza non viene eseguita. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Per un certo periodo di tempo non sono stati riscontrati eventi di rischio per le credenziali perse?

Se non si sono verificati eventi di rischio per le credenziali perse, i motivi sono i seguenti:

- Non è stata abilitata la funzionalità pH per il tenant.
- Microsoft non ha trovato coppie di credenziali perse corrispondenti agli utenti.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Con quale frequenza Microsoft elabora le nuove credenziali?

Le credenziali vengono elaborate immediatamente dopo essere state trovate, in genere in più batch al giorno.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)
- [Panoramica della sicurezza](concept-identity-protection-security-overview.md)
