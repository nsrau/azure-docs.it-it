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
ms.openlocfilehash: a333a61a28fabddc2e8101fdf3290c52f3db59ae
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780886"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Che cos'è il servizio Riconoscimento del parlante di Azure?

Il servizio Riconoscimento del parlante fornisce algoritmi che verificano e identificano i relatori in base alle relative caratteristiche vocali univoche. Riconoscimento del parlante viene utilizzato per rispondere alla domanda "chi parla?". Si forniscono dati di training audio per un singolo altoparlante, che consente di creare un profilo di registrazione in base alle caratteristiche univoche della voce del parlante. È quindi possibile eseguire il controllo incrociato degli esempi di voce audio in base a questo profilo per verificare che il relatore sia la stessa persona (verifica del parlante) o gli esempi di voce audio di controllo incrociato per un *gruppo* di profili speaker registrati, per vedere se corrisponde a qualsiasi profilo nel gruppo (identificazione del parlante). Al contrario, la messa in funzione del [relatore](batch-transcription.md#speaker-separation-diarization) raggruppa i segmenti di audio per altoparlante in un'operazione batch.

## <a name="speaker-verification"></a>Verifica voce

Verifica voce semplifica il processo di verifica dell'identità di un altoparlante registrato con passphrase o input vocale in formato libero. Può essere usato per verificare i singoli utenti per i clienti sicuri e senza attrito in un'ampia gamma di soluzioni, dalla verifica dell'identità dei clienti nei Call Center all'accesso alla struttura senza contatto.

### <a name="how-does-speaker-verification-work"></a>In che modo Verifica voce funziona?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Diagramma di flusso Verifica voce.":::

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

## <a name="common-questions-and-solutions"></a>Domande e soluzioni comuni

| Domanda | Soluzione |
|---------|----------|
| Quali scenari è Riconoscimento del parlante possibile usare? | Call Center verifica del cliente, archiviazione del paziente basata sulla voce, trascrizione della riunione, personalizzazione dei dispositivi multiutente|
| Qual è la differenza tra l'identificazione e la verifica? | L'identificazione è il processo che consente di rilevare quale membro di un gruppo di oratori sta parlando. La verifica è l'atto di confermare che un relatore corrisponde a una voce nota o **registrata** .|
| Qual è la differenza tra la verifica dipendente dal testo e la verifica indipendente dal testo? | Per la verifica dipendente dal testo è necessario un pass-phrase specifico per la registrazione e il riconoscimento. Per la verifica indipendente dal testo è necessario un campione vocale più lungo per la registrazione, ma è possibile pronunciare qualsiasi cosa, incluso durante il riconoscimento.|
| Quali lingue sono supportate? | Inglese, francese, spagnolo, cinese, tedesco, italiano, giapponese e portoghese |
| Quali aree di Azure sono supportate? | Riconoscimento del parlante è un servizio di anteprima e attualmente disponibile solo nell'area Stati Uniti occidentali.|
| Quali formati audio sono supportati? | Mono a 16 bit, WAV con codifica PCM 16kHz |
| Le risposte **Accept** e **Reject** non sono accurate, come si ottimizza la soglia? | Poiché la soglia ottimale varia in base agli scenari, l'API decide se "accettare" o "rifiutare" semplicemente in base a una soglia predefinita di 0,5. Si consiglia agli utenti avanzati di sostituire la decisione predefinita e di ottimizzare il risultato in base al proprio scenario. |
| È possibile registrare un altoparlante più volte? | Sì, per la verifica dipendente dal testo è possibile registrare un altoparlante per un massimo di 50 volte. Per la verifica indipendente dal testo o l'identificazione del parlante, è possibile eseguire la registrazione con un massimo di 300 secondi di audio. |
| Quali dati vengono archiviati in Azure? | L'audio di registrazione viene archiviato nel servizio fino a quando non viene [eliminato](speaker-recognition-basics.md#deleting-voice-profile-enrollments)il profilo vocale. Gli esempi di riconoscimento audio non vengono conservati o archiviati. |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> * Completare l' [articolo Nozioni di base](speaker-recognition-basics.md) su riconoscimento del parlante per un'esecuzione di modelli di progettazione comuni che è possibile usare nelle applicazioni.
> * Vedere l' [esercitazione video](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) per la verifica del relatore indipendente dal testo.
