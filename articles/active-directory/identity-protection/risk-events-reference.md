---
title: Informazioni di riferimento sul rilevamento del rischio Azure Active Directory Identity Protection | Microsoft Docs
description: Riferimento al rilevamento del rischio Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1005ac1853568222dea83a0f12293945825cf6d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127584"
---
# <a name="azure-active-directory-identity-protection-risk-detections-reference"></a>Riferimento al rilevamento del rischio Azure Active Directory Identity Protection

La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure Active Directory usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato rilevamento rischi.

## <a name="anonymous-ip-address"></a>Indirizzo IP anonimo

**Tipo di rilevamento:** Tempo reale  
**Nome precedente:** Accessi da indirizzi IP anonimi

Questo tipo di rilevamento dei rischi indica gli accessi da un indirizzo IP anonimo, ad esempio Tor Browser, Anonymizer VPN.
Questi indirizzi IP sono in genere usati da attori che vogliono nascondere i dati di telemetria relativi agli accessi (indirizzo IP, posizione, dispositivo e così via) con intenzioni potenzialmente dannose.

## <a name="atypical-travel"></a>Trasferimento atipico

**Tipo di rilevamento:** Offline  
**Nome precedente:** Trasferimento impossibile a posizioni atipiche

Questo tipo di rilevamento dei rischi identifica due accessi provenienti da località geograficamente distanti, in cui almeno una delle posizioni può essere atipica per l'utente, dato il comportamento precedente. Oltre ad altri fattori, l'algoritmo di apprendimento automatico prende in considerazione il tempo che intercorre tra i due accessi e il tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, che indica che un altro utente sta usando le stesse credenziali.

L'algoritmo ignora i "falsi positivi" evidenti che contribuiscono alle condizioni di impossibilità del trasferimento, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni o 10 accessi, durante i quali apprende il comportamento di accesso di un nuovo utente.

## <a name="leaked-credentials"></a>Credenziali perse

**Tipo di rilevamento:** Offline  
**Nome precedente:** Utenti con credenziali perse

Questo tipo di rilevamento del rischio indica che le credenziali valide dell'utente sono state perse.
I criminali informatici, quando compromettono password valide di utenti legittimi, spesso condividono tali credenziali, in genere pubblicandole sul dark Web o su siti pastebin oppure scambiandole o vendendole al mercato nero. Il servizio Credenziali perse di Microsoft acquisisce coppie di nome utente/password monitorando i siti Web pubblici e il dark Web e collaborando con:

- Ricercatori
- Forze dell'ordine
- Team di sicurezza di Microsoft
- Altre fonti attendibili

Quando il servizio acquisisce le credenziali utente dal dark Web, da siti pastebin o dalle origini riportate sopra, queste vengono confrontate con le credenziali valide correnti degli utenti di Azure AD per trovare corrispondenze valide.

## <a name="malware-linked-ip-address"></a>Indirizzo IP collegato a malware

**Tipo di rilevamento:** Offline  
**Nome precedente:** Accessi da dispositivi infetti

Questo tipo di rilevamento dei rischi indica gli accessi dagli indirizzi IP infetti da malware noto per la comunicazione attiva con un server bot. Viene determinato mettendo in relazione gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che sono stati in contatto con un server bot mentre quest'ultimo era attivo.

## <a name="unfamiliar-sign-in-properties"></a>Proprietà di accesso insolite

**Tipo di rilevamento:** Tempo reale  
**Nome precedente:** Accessi da posizioni non note

Questo tipo di rilevamento dei rischi considera la cronologia di accesso precedente (IP, Latitudine/Longitudine e ASN) per la ricerca di accessi anomali. Il sistema archivia le informazioni sulle posizioni usate in precedenza da un utente e le considera posizioni "note". Il rilevamento dei rischi viene attivato quando viene eseguito l'accesso da una posizione non ancora presente nell'elenco di posizioni note. Gli utenti appena creati saranno in "modalità di apprendimento" per un periodo di tempo in cui i rilevamenti dei rischi delle proprietà di accesso non note verranno spenti mentre gli algoritmi apprendono il comportamento dell'utente. La durata della modalità di apprendimento è dinamica e dipende dalla quantità di tempo impiegato dall'algoritmo per raccogliere informazioni sufficienti sui modelli di accesso dell'utente. La durata minima è di cinque giorni. Un utente può tornare alla modalità di apprendimento dopo un lungo periodo di inattività. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. 

È anche possibile eseguire questo rilevamento per l'autenticazione di base (o i protocolli legacy). Poiché questi protocolli non hanno proprietà moderne, ad esempio l'ID client, i dati di telemetria per ridurre i falsi positivi sono limitati. È consigliabile passare all'autenticazione moderna.

## <a name="azure-ad-threat-intelligence"></a>Intelligence per le minacce di Azure AD

**Tipo di rilevamento:** Offline <br>
**Nome precedente:** Questo rilevamento verrà visualizzato nei report legacy Azure AD Identity Protection (utenti contrassegnati per il rischio, rilevamento dei rischi) come ' utenti con credenziali perse '

Questo tipo di rilevamento del rischio indica che l'attività dell'utente è insolita per l'utente specificato o è coerente con i modelli di attacco noti basati sulle fonti di intelligence per le minacce interne ed esterne di Microsoft.

## <a name="admin-confirmed-user-compromised"></a>L'amministratore ha confermato che l'utente è compromesso

**Tipo di rilevamento:** Offline <br>
Questo rilevamento indica che un amministratore ha selezionato ' conferma utente compromesso ' nell'interfaccia utente Risky Users o usando l'API riskyUsers. Per verificare quale amministratore ha confermato la compromissione dell'utente, controllare la cronologia dei rischi dell'utente (tramite interfaccia utente o API).

## <a name="malicious-ip-address"></a>Indirizzo IP dannoso

**Tipo di rilevamento:** Offline <br>
Questo rilevamento indica l'accesso da un indirizzo IP dannoso. Un indirizzo IP viene considerato dannoso in base ai seguenti elementi:
-   Frequenza elevata degli errori (a causa di credenziali non valide ricevute dall'indirizzo IP)
-   Altre origini di reputazione IP

## <a name="additional-risk-detected"></a>Rischio aggiuntivo rilevato

**Tipo di rilevamento:** In tempo reale o offline <br>
Questo rilevamento indica che è stato rilevato uno dei rilevamenti Premium precedenti. Poiché i rilevamenti Premium sono visibili solo ai clienti Azure AD Premium P2, sono intitolati "rischi aggiuntivi rilevati" per i clienti non P2.
