---
title: Servizio Riconoscimento del parlante di Azure
titleSuffix: Azure Cognitive Services
description: Servizi cognitivi di Azure Riconoscimento del parlante fornisce algoritmi che verificano e identificano gli speaker con le relative caratteristiche vocali univoche. Riconoscimento del parlante viene utilizzato per rispondere alla domanda "chi parla?".
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261786"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Che cos'è il servizio Riconoscimento del parlante di Azure?

Il servizio Riconoscimento del parlante fornisce algoritmi che verificano e identificano i relatori in base alle relative caratteristiche vocali univoche. Riconoscimento del parlante viene utilizzato per rispondere alla domanda "chi parla?". Si forniscono dati di training audio per un singolo altoparlante, che consente di creare un profilo di registrazione in base alle caratteristiche univoche della voce del parlante. È quindi possibile eseguire il controllo incrociato degli esempi di voce audio in base a questo profilo per verificare che il relatore sia la stessa persona (verifica del parlante) o gli esempi di voce audio di controllo incrociato per un *gruppo* di profili speaker registrati, per vedere se corrisponde a qualsiasi profilo nel gruppo (identificazione del parlante). Al contrario, la messa in funzione del [relatore](batch-transcription.md#speaker-separation-diarization) raggruppa i segmenti di audio per altoparlante in un'operazione batch.

## <a name="speaker-verification"></a>Verifica voce

Verifica voce semplifica il processo di verifica dell'identità di un altoparlante registrato con passphrase o input vocale in formato libero. Può essere usato per verificare i singoli utenti per i clienti sicuri e senza attrito in un'ampia gamma di soluzioni, dalla verifica dell'identità dei clienti nei Call Center all'accesso alla struttura senza contatto.

### <a name="how-does-speaker-verification-work"></a>In che modo Verifica voce funziona?

![Funzionamento della verifica dell'altoparlante](media/speaker-recognition/speaker-rec.png)

La verifica dell'altoparlante può essere dipendente dal testo o indipendente dal testo. La verifica **dipendente dal testo** indica che i relatori devono scegliere la stessa passphrase da usare durante le fasi di registrazione e di verifica. La verifica **indipendente dal testo** indica che i relatori possono parlare in linguaggio quotidiano nelle frasi di registrazione e di verifica.

Per la verifica **dipendente dal testo** , la voce del relatore viene registrata pronunciando una passphrase da un set di frasi predefinite. Le funzionalità vocali vengono estratte dalla registrazione audio per formare una firma vocale univoca, mentre viene riconosciuta anche la passphrase scelta. Insieme, la firma vocale e la passphrase vengono usate per verificare il relatore. 

La verifica **indipendente dal testo** non prevede alcuna restrizione su ciò che il relatore indica durante la registrazione o nell'esempio audio da verificare, perché estrae solo le funzionalità vocali per assegnare un punteggio alla somiglianza. 

Le API non sono destinate a determinare se l'audio provenga da una persona Live o da un'imitazione/registrazione di un altoparlante registrato. 

## <a name="speaker-identification"></a>Identificazione voce

Identificazione voce viene usato per determinare l'identità di un altoparlante sconosciuto all'interno di un gruppo di altoparlanti registrati. Identificazione voce consente di attribuire il riconoscimento vocale a singoli relatori e di sbloccare il valore da scenari con più altoparlanti, ad esempio:

* Soluzioni di supporto per la produttività delle riunioni remote 
* Creazione della personalizzazione dei dispositivi multiutente

### <a name="how-does-speaker-identification-work"></a>In che modo Identificazione voce funziona?

La registrazione per l'identificazione del relatore è **indipendente dal testo**, il che significa che non vi sono restrizioni sui contenuti che l'altoparlante dichiara nell'audio. Analogamente a Verifica voce, nella fase di registrazione viene registrata la voce del parlante e le funzionalità vocali vengono estratte per formare una firma vocale univoca. Nella fase di identificazione, l'esempio di voce di input viene confrontato con un elenco specificato di voci registrate (fino a 50 in ogni richiesta).

## <a name="data-security-and-privacy"></a>Sicurezza e privacy dei dati

I dati di registrazione del relatore vengono archiviati in un sistema protetto, incluso l'audio vocale per la registrazione e le funzionalità di firma vocale. L'audio vocale per la registrazione viene usato solo quando l'algoritmo viene aggiornato e le funzionalità devono essere nuovamente estratte. Il servizio non mantiene la registrazione vocale o le funzionalità di voce estratte inviate al servizio durante la fase di riconoscimento. 

È possibile controllare per quanto tempo devono essere conservati i dati. È possibile creare, aggiornare ed eliminare i dati di registrazione per i singoli speaker tramite chiamate API. Quando la sottoscrizione viene eliminata, verranno eliminati anche tutti i dati di registrazione dei parlanti associati. 

Come per tutte le risorse di servizi cognitivi, gli sviluppatori che usano il servizio Riconoscimento del parlante devono essere a conoscenza dei criteri Microsoft sui dati dei clienti. È necessario assicurarsi di avere ricevuto le autorizzazioni appropriate dagli utenti per Riconoscimento del parlante. Per ulteriori informazioni, vedere la [pagina Servizi cognitivi](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   nel centro protezione Microsoft. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> * Vedere l' [esercitazione video](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) per la verifica del relatore indipendente dal testo.
