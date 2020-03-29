---
title: Linee guida per la distribuzione responsabile della tecnologia vocale sintetica
titleSuffix: Azure Cognitive Services
description: Linee guida generali di progettazione di Microsoft per l'utilizzo della tecnologia voce sintetica. Questi sono stati sviluppati in studi che Microsoft ha condotto con il talento vocale, consumatori, così come gli individui con disturbi del linguaggio per guidare lo sviluppo responsabile della voce sintetica.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836771"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Linee guida per la distribuzione responsabile della tecnologia vocale sintetica
Ecco le linee guida generali di progettazione di Microsoft per l'utilizzo della tecnologia vocale sintetica. Questi sono stati sviluppati in studi che Microsoft ha condotto con il talento vocale, consumatori, così come gli individui con disturbi del linguaggio per guidare lo sviluppo responsabile della voce sintetica.

## <a name="general-considerations"></a>Considerazioni generali
Per la distribuzione della tecnologia di riconoscimento vocale sintetico, le linee guida seguenti si applicano alla maggior parte degli scenari.

### <a name="disclose-when-the-voice-is-synthetic"></a>Rivelare quando la voce è sintetica
La divulgazione che una voce è generata dal computer non solo riduce al minimo il rischio di esiti dannosi da inganno, ma aumenta anche la fiducia nell'organizzazione che fornisce la voce. Ulteriori informazioni su [come divulgare](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selezionare i tipi di voce appropriati per lo scenario
Considerare attentamente il contesto di utilizzo e i potenziali danni associati all'uso di voce sintetica. Ad esempio, le voci sintetiche ad alta fedeltà potrebbero non essere appropriate in scenari ad alto rischio, ad esempio per la messaggistica personale, le transazioni finanziarie o situazioni complesse che richiedono adattabilità umana o empatia. Gli utenti possono anche avere aspettative diverse per i tipi di voce. Ad esempio, quando si ascoltano notizie sensibili lette da una voce sintetica, alcuni utenti preferiscono una lettura più empatica e umana delle notizie, mentre altri preferivano una voce più monotona e imparziale. Valutare la possibilità di testare l'applicazione per comprendere meglio le preferenze dell'utente.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Essere trasparenti su funzionalità e limitazioni
Gli utenti hanno maggiori probabilità di avere aspettative più elevate quando interagiscono con agenti vocali sintetici ad alta fedeltà. Di conseguenza, quando le funzionalità del sistema non soddisfano tali aspettative, la fiducia può soffrire e può causare esperienze spiacevoli o addirittura dannose.

### <a name="provide-optional-human-support"></a>Fornire supporto umano opzionale
In scenari transazionali ambigui (ad esempio, un centro di supporto per le chiamate), gli utenti non sempre considerano attendibile un agente del computer per rispondere in modo appropriato alle richieste. Il sostegno umano può essere necessario in queste situazioni, indipendentemente dalla qualità realistica della voce o della capacità del sistema.

## <a name="considerations-for-voice-talent"></a>Considerazioni per i talenti vocali
Quando si lavora con il talento vocale, come gli attori vocali, per creare voci sintetiche, si applica la linea guida riportata di seguito.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Ottenere il consenso significativo dal talento vocale
I talenti vocali si aspettano di avere il controllo sul loro carattere vocale (come e dove verrà utilizzato) e di essere compensati ogni volta che viene utilizzato. I proprietari di sistemi dovrebbero quindi ottenere l'esplicita autorizzazione scritta dai talenti vocali e disporre di specifiche contrattuali chiare sui casi d'uso, sulla durata dell'uso, sulla compensazione e così via. Alcuni talenti vocali non sono a conoscenza dei potenziali usi dannosi della tecnologia e dovrebbero essere istruiti dai proprietari del sistema sulle capacità della tecnologia. Per ulteriori informazioni su talenti vocali e consenso, leggi la nostra [Informativa per i talenti vocali](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Considerazioni per le persone con disturbi del linguaggio
Quando si lavora con persone con disturbi del linguaggio, per creare o distribuire la tecnologia vocale sintetica, si applicano le linee guida seguenti.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornire linee guida per stabilire contratti
Fornire linee guida per stabilire contratti con persone che usano la voce sintetica per assistenza nel parlare. Il contratto dovrebbe prendere in considerazione la possibilità di specificare le parti proprietarie della voce, la durata dell'uso, i criteri di trasferimento della proprietà, le procedure per l'eliminazione del carattere vocale e come impedire l'accesso non autorizzato. Inoltre, consentire il trasferimento contrattuale della proprietà del carattere vocale dopo la morte ai membri della famiglia se tale persona ha dato il permesso.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Tenere conto delle incoerenze nei modelli di riconoscimento vocale
Per gli individui con disturbi del linguaggio che registrano i propri caratteri vocali, le incoerenze nel loro modello vocale (slurring o incapacità di pronunciare determinate parole) possono complicare il processo di registrazione. In questi casi, la tecnologia vocale sintetica e le sessioni di registrazione dovrebbero ospitarle (vale a dire, fornire pause e un numero aggiuntivo di sessioni di registrazione).

### <a name="allow-modification-over-time"></a>Consenti modifica nel tempo
Gli individui con disturbi del linguaggio desiderano aggiornare la loro voce sintetica per riflettere l'invecchiamento (ad esempio, un bambino che raggiunge la pubertà). Gli individui possono anche avere preferenze stilistiche che cambiano nel tempo, e possono decidere di apportare modifiche al tono, accento, o altre caratteristiche vocali.


## <a name="reference-docs"></a>Documentazione di riferimento

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
* [Panoramica di Gating](concepts-gating-overview.md)
* [Come divulgare](concepts-disclosure-guidelines.md)
* [Disclosure Design Patterns](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Passaggi successivi

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
* [Come divulgare](concepts-disclosure-guidelines.md)
* [Disclosure Design Patterns](concepts-disclosure-patterns.md)
