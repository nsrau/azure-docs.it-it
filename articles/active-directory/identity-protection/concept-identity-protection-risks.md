---
title: Cos'è il rischio? Azure AD Identity Protection
description: Spiegazione dei rischi in Azure AD Identity ProtectionExplaining risk in Azure AD Identity Protection
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253689"
---
# <a name="what-is-risk"></a>Cos'è il rischio?

I rilevamenti dei rischi in Azure AD Identity Protection includono eventuali azioni sospette identificate relative agli account utente nella directory.

Identity Protection consente alle organizzazioni di accedere a potenti risorse per visualizzare e rispondere rapidamente a queste azioni sospette. 

![Panoramica della sicurezza che mostra utenti rischiosi e degli invii](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipi di rischio e rilevamento

Esistono due tipi di **rischio Utente** e **Accesso** e due tipi di rilevamento o calcolo in **tempo reale** e **Offline**.

### <a name="user-risk"></a>Rischio utente

Un rischio utente rappresenta la probabilità che una determinata identità o account venga compromessa. 

Questi rischi vengono calcolati offline utilizzando le fonti di intelligence sulle minacce interne ed esterne di Microsoft, inclusi i ricercatori di sicurezza, i professionisti delle forze dell'ordine, i team di sicurezza di Microsoft e altre fonti attendibili.

| Rilevamento dei rischi | Descrizione |
| --- | --- |
| Credenziali perse | Questo tipo di rilevamento dei rischi indica che le credenziali valide dell'utente sono trapelate. Quando i criminali informatici compromettono password valide di utenti legittimi, spesso condividono tali credenziali, Questa condivisione viene in genere fatto pubblicamente sul web scuro, incollare siti, o commerciando e vendendo le credenziali sul mercato nero. Quando il servizio credenziali trapelate microsoft acquisisce le credenziali utente dal Web scuro, dai siti incolla o da altre origini, vengono confrontate con le credenziali valide correnti degli utenti di Azure AD per trovare corrispondenze valide. |
| Intelligence per le minacce di Azure AD | Questo tipo di rilevamento dei rischi indica l'attività dell'utente insolita per l'utente specificato o coerente con i modelli di attacco noti basati sulle origini di Intelligence sulle minacce interne ed esterne di Microsoft.This risk detection type indicates user activity that is unusual for the given user or is consistent with known attack patterns based on Microsoft's internal and external threat intelligence sources. |

### <a name="sign-in-risk"></a>Rischio di accesso

Un rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. 

Questi rischi possono essere calcolati in tempo reale o calcolati offline utilizzando le fonti di intelligence sulle minacce interne ed esterne di Microsoft, inclusi ricercatori di sicurezza, professionisti delle forze dell'ordine, team di sicurezza di Microsoft e altre fonti attendibili.

| Rilevamento dei rischi | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Indirizzo IP anonimo | Tempo reale | Questo tipo di rilevamento dei rischi indica gli accessi da un indirizzo IP anonimo (ad esempio, il browser Tor o vpn anonima). Questi indirizzi IP sono in genere usati da attori che vogliono nascondere i dati di telemetria relativi agli accessi (indirizzo IP, posizione, dispositivo e così via) con intenzioni potenzialmente dannose. |
| Trasferimento atipico | Offline | Questo tipo di rilevamento dei rischi identifica due insegne provenienti da posizioni geograficamente distanti, in cui almeno una delle posizioni può anche essere atipica per l'utente, dato il comportamento passato. Oltre ad altri fattori, l'algoritmo di apprendimento automatico prende in considerazione il tempo che intercorre tra i due accessi e il tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, che indica che un altro utente sta usando le stesse credenziali. <br><br> L'algoritmo ignora i "falsi positivi" evidenti che contribuiscono alle condizioni di impossibilità del trasferimento, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni o 10 accessi, durante il quale apprende il comportamento di accesso di un nuovo utente. |
| Indirizzo IP collegato a malware | Offline | Questo tipo di rilevamento dei rischi indica gli indirizzi di accesso da indirizzi IP infettati da malware che è noto per comunicare attivamente con un server bot. Questo rilevamento viene determinato correlando gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP in contatto con un server bot mentre il server bot era attivo. |
| Proprietà di accesso insolite | Tempo reale | Questo tipo di rilevamento dei rischi considera la cronologia di accesso passata (IP, Latitude / Longitude e ASN) per cercare gli insuccessi anomali. Il sistema memorizza le informazioni sulle posizioni precedenti utilizzate da un utente e considera queste posizioni "familiari". Il rilevamento dei rischi viene attivato quando l'accesso avviene da una posizione che non è già presente nell'elenco delle posizioni familiari. Gli utenti appena creati saranno in "modalità di apprendimento" per un periodo di tempo in cui i rilevamenti dei rischi delle proprietà di accesso sconosciute verranno disattivati mentre i nostri algoritmi apprendono il comportamento dell'utente. La durata della modalità di apprendimento è dinamica e dipende dal tempo necessario all'algoritmo per raccogliere informazioni sufficienti sui modelli di accesso dell'utente. La durata minima è di cinque giorni. Un utente può tornare in modalità di apprendimento dopo un lungo periodo di inattività. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. <br><br> È anche possibile eseguire questo rilevamento per l'autenticazione di base (o i protocolli legacy). Poiché questi protocolli non hanno proprietà moderne, ad esempio l'ID client, i dati di telemetria per ridurre i falsi positivi sono limitati. È consigliabile passare all'autenticazione moderna. |
| Amministratore confermato utente compromessa | Offline | Questo rilevamento indica che un amministratore ha selezionato "Conferma utente compromesso" nell'interfaccia utente degli utenti rischiosi o che utilizza l'API RiskyUsers. Per vedere quale amministratore ha confermato che l'utente ha compromesso, controlla la cronologia dei rischi dell'utente (tramite l'interfaccia utente o l'API). |
| Indirizzo IP dannoso | Offline | Questo rilevamento indica l'accesso da un indirizzo IP dannoso. Un indirizzo IP è considerato dannoso in base a tassi di errore elevati a causa di credenziali non valide ricevute dall'indirizzo IP o da altre origini di reputazione IP. |
| Regole di manipolazione della posta in arrivo sospette | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Questo criterio di rilevamento profila l'ambiente e genera avvisi quando nella Posta in arrivo di un utente vengono configurate regole sospette per eliminare o spostare messaggi o cartelle. Ciò potrebbe indicare che l'account dell'utente è compromesso, che i messaggi vengono intenzionalmente nascosti e che la cassetta postale viene utilizzata per distribuire posta indesiderata o malware nell'organizzazione. |
| Comunicazione impossibile | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Questo rilevamento identifica due attività utente (in una singola sessione o in più sessioni) provenienti da località geograficamente distanti e realizzate in un intervallo di tempo più breve rispetto a quello che sarebbe stato necessario all'utente per spostarsi dalla prima alla seconda località. Si tratta pertanto di un altro utente che sta usando le stesse credenziali. |

### <a name="other-risk-detections"></a>Altre rilevazioni dei rischi

| Rilevamento dei rischi | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Rischio aggiuntivo rilevato | In tempo reale o offline | Questo rilevamento indica che è stato rilevato uno dei rilevamenti premium di cui sopra. Poiché i rilevamenti premium sono visibili solo ai clienti di Azure AD Premium P2, sono denominati "rischio aggiuntivo rilevato" per i clienti senza licenze di Azure AD Premium P2. |

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)

- [Panoramica della sicurezza](concept-identity-protection-security-overview.md)
