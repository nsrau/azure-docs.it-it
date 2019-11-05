---
title: Linee guida per la distribuzione responsabile della tecnologia Voice sintetica
titleSuffix: Azure Cognitive Services
description: Linee guida generali per la progettazione di Microsoft per l'utilizzo della tecnologia Voice sintetica. Queste sono state sviluppate in studi che Microsoft ha condotto con il talento vocale, i consumatori, nonché singoli utenti con problemi di sintesi vocale per guidare lo sviluppo responsabile della voce sintetica.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: fe8730b0d97ae3783282cdd401fd5889ed08ad9a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507165"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Linee guida per la distribuzione responsabile della tecnologia Voice sintetica
Ecco le linee guida di progettazione generali di Microsoft per l'uso della tecnologia Voice sintetica. Queste sono state sviluppate in studi che Microsoft ha condotto con il talento vocale, i consumatori, nonché singoli utenti con problemi di sintesi vocale per guidare lo sviluppo responsabile della voce sintetica.

## <a name="general-considerations"></a>Considerazioni generali
Per la distribuzione della tecnologia sintesi vocale sintetica, le linee guida seguenti si applicano alla maggior parte degli scenari.

### <a name="disclose-when-the-voice-is-synthetic"></a>Divulga quando la voce è sintetica
La divulgazione di una voce in un computer generato non solo riduce al minimo il rischio di risultati dannosi da un inganno, ma aumenta anche il trust nell'organizzazione che fornisce la voce. Altre informazioni su [come divulgare](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selezionare i tipi vocali appropriati per lo scenario
Valutare con attenzione il contesto di utilizzo e i potenziali danni associati all'uso della voce sintetica. Ad esempio, le voci sintetiche ad alta fedeltà potrebbero non essere appropriate negli scenari ad alto rischio, ad esempio per la messaggistica personale, le transazioni finanziarie o situazioni complesse che richiedono adattabilità umana o empatia. Gli utenti possono anche avere aspettative diverse per i tipi di voce. Ad esempio, quando si è in ascolto di notizie riservate lette da una voce sintetica, alcuni utenti preferiscono una lettura più empatica e simile a quella umana, mentre altri preferiscono una voce più monotona, non distorta. Provare a testare l'applicazione per comprendere meglio le preferenze dell'utente.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Essere trasparente sulle funzionalità e le limitazioni
È più probabile che gli utenti abbiano aspettative più elevate durante l'interazione con agenti vocali sintetici ad alta fedeltà. Di conseguenza, quando le funzionalità di sistema non soddisfano tali aspettative, l'attendibilità può soffrire e può comportare esperienze spiacevoli o persino dannose.

### <a name="provide-optional-human-support"></a>Fornire supporto umano facoltativo
In scenari ambigui e transazionali, ad esempio un Call Support Center, gli utenti non considerano sempre attendibile un agente computer per rispondere in modo appropriato alle richieste. In queste situazioni può essere necessario il supporto umano, indipendentemente dalla qualità realistica della voce o della capacità del sistema.

## <a name="considerations-for-voice-talent"></a>Considerazioni per il talento vocale
Quando si lavora con il talento vocale, ad esempio gli attori vocali, per creare voci sintetiche, si applicano le linee guida riportate di seguito.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Ottenere un consenso significativo dal talento vocale
Il talento vocale si aspetta di avere il controllo del tipo di carattere vocale (come e dove verrà usato) e verrà compensato ogni volta che viene usato. I proprietari del sistema devono quindi ottenere le autorizzazioni esplicite scritte dal talento vocale e hanno chiare specifiche contrattuali sui casi d'uso, la durata dell'utilizzo, la compensazione e così via. Un talento vocale non è a conoscenza dei potenziali usi dannosi della tecnologia e deve essere istruito dai proprietari del sistema sulle funzionalità della tecnologia. Per altre informazioni sul talento vocale e sul consenso, leggi la nostra [Nota sulla trasparenza](https://aka.ms/neural-tts-transparency-note).


## <a name="considerations-for-those-with-speech-disorders"></a>Considerazioni per gli utenti con problemi di sintesi vocale
Quando si lavora con utenti con problemi di sintesi vocale, per creare o distribuire la tecnologia vocale sintetica, si applicano le linee guida seguenti.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornire linee guida per stabilire i contratti
Fornire linee guida per la definizione di contratti con singoli utenti che utilizzano la voce sintetica per assistenza nella comunicazione. Il contratto deve considerare la possibilità di specificare le entità che possiedono la voce, la durata dell'utilizzo, i criteri di trasferimento della proprietà, le procedure per eliminare il carattere vocale e come impedire l'accesso non autorizzato. Inoltre, è possibile abilitare il trasferimento contrattuale della proprietà del tipo di carattere vocale dopo la morte ai membri della famiglia se tale persona ha concesso l'autorizzazione.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Account per le incoerenze nei modelli di riconoscimento vocale
Per i singoli utenti con problemi di sintesi vocale che registrano i propri tipi di carattere vocali, le incoerenze nel modello di riconoscimento vocale (i residui o l'impossibilità di pronunciare determinate parole) potrebbero complicare il processo di registrazione. In questi casi, le sessioni di registrazione e tecnologia della voce sintetica dovrebbero adattarsi (ovvero fornire interruzioni e un numero aggiuntivo di sessioni di registrazione).

### <a name="allow-modification-over-time"></a>Consenti modifiche nel tempo
Gli utenti con problemi di riconoscimento vocale desiderano apportare aggiornamenti alla voce sintetica per riflettere la durata (ad esempio, un bambino che raggiunge la pubertà). Le persone possono anche avere preferenze stilistiche che cambiano nel tempo e possono apportare modifiche a pitch, Accent o altre caratteristiche vocali.


## <a name="reference-docs"></a>Documentazione di riferimento

* [Nota sulla trasparenza](https://aka.ms/neural-tts-transparency-note)
* [Cenni preliminari sul controllo](concepts-gating-overview.md)
* [Come divulgare](concepts-disclosure-guidelines.md)
* [Schemi di progettazione della divulgazione](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Passaggi successivi

* [Nota sulla trasparenza](https://aka.ms/neural-tts-transparency-note)
* [Come divulgare](concepts-disclosure-guidelines.md)
* [Schemi di progettazione della divulgazione](concepts-disclosure-patterns.md)
