---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 0fd13437ecd494eebf79fa80ed210a0663864104
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875453"
---
Questo argomento di avvio rapido illustra i modelli di progettazione di base per Riconoscimento del parlante tramite Speech SDK, tra cui:

* Verifica dipendente dal testo e indipendente dal testo
* Identificazione voce per identificare un campione di voce tra un gruppo di voci
* Eliminazione dei profili vocali

Per informazioni generali sui concetti di Riconoscimento del parlante, vedere l'articolo [Panoramica](../../../speaker-recognition-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> La funzionalità Riconoscimento del parlante è attualmente supportata *solo* nelle risorse Voce di Azure create nell'area `westus`.

## <a name="text-dependent-verification"></a>Verifica dipendente dal testo

Verifica voce è l'atto di confermare che un parlante corrisponde a una voce nota o **registrata**. Il primo passaggio consiste nel **registrare** un profilo vocale, in modo che il servizio disponga di un profilo da confrontare con i campioni di voce futuri. In questo esempio, il profilo viene registrato usando una strategia **dipendente dal testo**, che richiede una passphrase specifica da usare per la registrazione e la verifica. Per un elenco delle passphrase supportate, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/speakerrecognition/).

Per iniziare, [creare un profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/createprofile). Sarà necessario inserire la chiave e l'area della sottoscrizione del servizio Voce in ognuno dei comandi curl in questo articolo.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Si noti che esistono tre tipi di profili vocali:

- Verifica dipendente dal testo
- Verifica indipendente dal testo
- Identificazione indipendente dal testo

In questo caso, verrà creato un profilo vocale di verifica dipendente dal testo. Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Successivamente, si procederà alla [registrazione del profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/createenrollment). Per il valore del parametro `--data-binary`, specificare un file audio nel computer contenente una delle passphrase supportate, ad esempio "my voice is my passport, verify me" (La mia voce è il mio passaporto, verificami). È possibile registrare un file audio di questo tipo con un'app, ad esempio [Registratore vocale Windows](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), oppure è possibile generarlo usando la [sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Questa risposta indica che è necessario registrare altri due campioni audio.

Dopo aver registrato un totale di tre campioni audio, si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

A questo punto si è pronti per [verificare un campione audio in base al profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Il campione audio deve contenere la stessa passphrase dei campioni usati per registrare il profilo vocale.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` indica che la passphrase corrisponde e che la verifica è stata superata. La risposta contiene anche un punteggio di somiglianza compreso tra 0,0 e 1,0.

Per terminare, [eliminare il profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Non viene restituita alcuna risposta.

## <a name="text-independent-verification"></a>Verifica indipendente dal testo

A differenza della verifica **dipendente dal testo**, la verifica **indipendente dal testo**:

* Non richiede che venga pronunciata una passphrase specifica, ma è possibile pronunciare le parole desiderate
* Non richiede tre campioni audio, ma *richiede* 20 secondi di audio totale

Per iniziare, [creare un profilo di verifica indipendente dal testo](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Successivamente, si procederà alla [registrazione del profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/createenrollment). Anche in questo caso, invece di inviare tre campioni audio, è necessario inviare campioni audio contenenti un totale di 20 secondi di audio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Dopo aver inviato un numero sufficiente di campioni audio, si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

A questo punto si è pronti per [verificare un campione audio in base al profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Anche in questo caso, il campione audio non deve contenere una passphrase. Può includere le parole desiderate, purché contenga un totale di almeno quattro secondi di audio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` indica che la verifica ha avuto esito positivo. La risposta contiene anche un punteggio di somiglianza compreso tra 0,0 e 1,0.

Per terminare, [eliminare il profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Non viene restituita alcuna risposta.

## <a name="speaker-identification"></a>Identificazione voce

La funzionalità Identificazione voce viene usata per determinare **chi** parla da un gruppo specifico di voci registrate. Il processo è simile a quello di **verifica indipendente dal testo**, con la differenza principale che può eseguire la verifica rispetto a più profili vocali contemporaneamente, anziché rispetto a un singolo profilo.

Per iniziare, [creare un profilo di identificazione indipendente dal testo](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Successivamente, si procederà alla [registrazione del profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/createenrollment). Anche in questo caso, è necessario inviare campioni audio contenenti un totale di 20 secondi di audio. Questi campioni non devono contenere una passphrase.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Dopo aver inviato un numero sufficiente di campioni audio, si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

A questo punto si è pronti per [identificare un campione audio in base al profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Il comando di identificazione accetta un elenco delimitato da virgole di possibili ID di profilo vocale. In questo caso, si passerà semplicemente l'ID del profilo vocale creato in precedenza. Tuttavia, se si desidera, è possibile passare più ID di profilo vocale in cui ogni profilo vocale è registrato con campioni audio da un'altra voce.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Si riceverà la risposta seguente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

La risposta contiene l'ID del profilo vocale che corrisponde maggiormente al campione audio inviato. Contiene anche un elenco di profili vocali candidati, classificati in ordine di somiglianza.

Per terminare, [eliminare il profilo vocale](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Non viene restituita alcuna risposta.
