---
title: Tag delle immagini di contenitori in Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Elenco completo di tutti i tag dell'immagine del contenitore di servizi cognitivi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: 90ac4891dbca34ff2d9bdb9653e27ad2a495b317
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021253"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Tag di immagine del contenitore di servizi cognitivi di Azure e note sulla versione

Servizi cognitivi di Azure offre molte immagini del contenitore. I registri dei contenitori e i repository corrispondenti variano tra le immagini del contenitore. Ogni nome dell'immagine del contenitore offre più tag. Un tag dell'immagine del contenitore è un meccanismo di controllo delle versioni dell'immagine del contenitore. Questo articolo è destinato a essere usato come riferimento completo per l'elenco di tutte le immagini del contenitore di servizi cognitivi e dei relativi tag disponibili.

> [!TIP]
> Quando si usa [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , prestare particolare attenzione alle maiuscole e minuscole del registro contenitori, al repository, al nome dell'immagine del contenitore e al tag corrispondente, poiché fanno **distinzione tra maiuscole** e minuscole.

## <a name="anomaly-detector"></a>Rilevamento anomalie

L'immagine del contenitore del [rilevatore di anomalie][ad-containers] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/decision` repository ed è denominato `anomaly-detector` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Lettura OCR (riconoscimento ottico di caratteri)

Il [visione artificiale][cv-containers] l'immagine del contenitore OCR è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services` repository ed è denominato `read` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

Note sulla versione per `v2.0.013250001-amd64-preview` :

* Ridurre ulteriormente l'utilizzo della memoria per il contenitore.
* La cache esterna è necessaria per la configurazione di più POD. Ad esempio, configurare Redis per la memorizzazione nella cache.
* Corretti i risultati mancanti quando la cache Redis è configurata e `ResultExpirationPeriod` è impostata su 0.
* Rimuovere la limitazione delle dimensioni del corpo della richiesta di 26MB. Il contenitore è ora in grado di accettare >file 26MB.
* Aggiungere il timestamp e la versione di build alla registrazione della console.

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

Note sulla versione per `1.1.013050001-amd64-preview`

* Aggiunta `ReadEngineConfig:ResultExpirationPeriod` della configurazione di inizializzazione del contenitore per specificare quando il sistema deve pulire i risultati del riconoscimento. 
    * L'impostazione è in ore e il valore predefinito è 48 ore.
    * L'impostazione può ridurre l'utilizzo della memoria per l'archiviazione dei risultati, soprattutto quando si usa l'archiviazione in memoria del contenitore.
    * Esempio 1. ReadEngineConfig: ResultExpirationPeriod = 1, il sistema cancellerà il risultato del riconoscimento 1 ora dopo il processo.
    * Se questa configurazione è impostata su 0, il sistema cancellerà il risultato del riconoscimento dopo che il risultato è stato recuperato.

* Correzione di un errore interno del server 500 quando al sistema viene passato un formato di immagine non valido. Verrà ora restituito un errore 400:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Riconoscimento modulo

L'immagine del contenitore del [riconoscitore del modulo][fr-containers] è disponibile nel `mcr.microsoft.com` Syndication del registro contenitori. Si trova all'interno del `azure-cognitive-services/custom-form` repository ed è denominato `labeltool` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L'immagine del contenitore [Luis][lu-containers] si trova nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/language` repository ed è denominato `luis` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Da Riconoscimento vocale personalizzato a testo

L'immagine del contenitore [da riconoscimento vocale personalizzato a testo][sp-cstt] è disponibile nel Syndicate del `mcr.microsoft.com` Registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `custom-speech-to-text` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `2.7.0-amd64` :

**Funzionalità**
* La punteggiatura è impostata come abilitata per impostazione predefinita.

Si noti che a causa degli elenchi di frasi inclusi, le dimensioni di questa immagine del contenitore sono aumentate.

| Tag immagine                    | Note | Digest                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)
Nota sulla versione per `2.6.0-amd64` :

**Funzionalità**
* Supporto per l'phrase V2 
* Gli elenchi di frasi sono supportati nelle impostazioni locali seguenti:
    * it-au
    * en-CA
    * en-GB
    * it-in
    * it-it
    * zh-cn
* Supporto per il download personalizzato del modello di base. 
    * Usare `BaseModelLocale=<locale>` con il `docker run` comando
* Migrazione completa a .NET 3,1

**Correzioni**
* È stato risolto un problema per cui il Punteggio di confidenza era sempre 1 in modalità di programmazione
* Migrazione all'API TextAnalytics 3,0

Si noti che a causa degli elenchi di frasi inclusi, le dimensioni di questa immagine del contenitore sono aumentate.

Nota sulla versione per `2.5.0-amd64` :

**Funzionalità**
* Supporto della pronuncia personalizzata nei modelli personalizzati
* Supportare Azure e il Cloud Azure per enti pubblici degli Stati Uniti

**Correzioni**
* Correzione del problema di esecuzione come utente non radice in modalità di Diarizzazione

| Tag immagine                    | Note               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   1 ° versione GA    |

---

## <a name="custom-text-to-speech"></a>Sintesi vocale personalizzata

L'immagine del contenitore [di sintesi vocale personalizzato][sp-ctts] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `custom-text-to-speech` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `1.9.0-amd64` :

Versione mensile regolare

| Tag immagine                    | Note | Digest                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)
Nota sulla versione per `1.8.0-amd64` :

**Funzionalità**
* Migrazione completa a .NET 3,1

Nota sulla versione per `1.7.0-amd64` :

**Funzionalità**
* Migrazione parziale a .NET 3,1

| Tag immagine                    | Note               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   1 ° versione GA    |

---

## <a name="speech-to-text"></a>Riconoscimento vocale

L'immagine del contenitore [di sintesi vocale][sp-stt] è reperibile nel Syndicate del `mcr.microsoft.com` Registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `speech-to-text` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . È possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)versione.

Poiché il 2.5.0 di riconoscimento vocale v, le immagini sono supportate nell'area *Virginia del governo degli Stati Uniti* . Usare l'endpoint di fatturazione di Virginia per il *governo degli Stati Uniti* e le chiavi API quando si usa questa area.

# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `2.7.0-amd64-<locale>` :

**Funzionalità**
* Supporto per le nuove impostazioni locali seguenti:
    * AR-BH, AR-IQ, AR-Jo, AR-lb, AR-OM, ar-SY
    * bg-bg
    * el-gr
    * en-HK, en-IE, en-pH, en-SG, en-za
    * es-AR, es-Bo, es-CL, es-CO, es-CR, es-cu, es-do, es-EC, es-gt, es-PA, es-PE, es-PR, es-py, es-SV, es-US, es-uy, es-ve
    * et-ee
    * ga-ie
    * hr-hr
    * hu-hu
    * lt-lt
    * lv-lv
    * mt-mt
    * ro-ro
    * sk-sk
    * SL-SL
* La punteggiatura è abilitata per impostazione predefinita.

Si noti che a causa degli elenchi di frasi inclusi, le dimensioni di questa immagine del contenitore sono aumentate. 

| Tag immagine                    | Note                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Immagine del contenitore con le `en-US` impostazioni locali.                                                             |
| `2.7.0-amd64-<locale>`        | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `2.7.0-amd64-en-us`. |

Per questo contenitore sono disponibili le impostazioni locali seguenti.

| Impostazioni locali per v 2.7.0           | Note                                    | Digest                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | Immagine del contenitore con le `ar-AE` impostazioni locali. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | Immagine del contenitore con le `ar-BH` impostazioni locali. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | Immagine del contenitore con le `ar-EG` impostazioni locali. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | Immagine del contenitore con le `ar-IQ` impostazioni locali. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | Immagine del contenitore con le `ar-JO` impostazioni locali. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | Immagine del contenitore con le `ar-KW` impostazioni locali. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | Immagine del contenitore con le `ar-LB` impostazioni locali. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | Immagine del contenitore con le `ar-OM` impostazioni locali. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | Immagine del contenitore con le `ar-QA` impostazioni locali. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | Immagine del contenitore con le `ar-SA` impostazioni locali. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | Immagine del contenitore con le `ar-SY` impostazioni locali. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | Immagine del contenitore con le `bg-BG` impostazioni locali. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | Immagine del contenitore con le `ca-ES` impostazioni locali. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | Immagine del contenitore con le `cs-CZ` impostazioni locali. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | Immagine del contenitore con le `da-DK` impostazioni locali. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | Immagine del contenitore con le `de-DE` impostazioni locali. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | Immagine del contenitore con le `el-GR` impostazioni locali. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | Immagine del contenitore con le `en-AU` impostazioni locali. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | Immagine del contenitore con le `en-CA` impostazioni locali. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | Immagine del contenitore con le `en-GB` impostazioni locali. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | Immagine del contenitore con le `en-HK` impostazioni locali. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | Immagine del contenitore con le `en-IE` impostazioni locali. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | Immagine del contenitore con le `en-IN` impostazioni locali. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | Immagine del contenitore con le `en-NZ` impostazioni locali. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | Immagine del contenitore con le `en-PH` impostazioni locali. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | Immagine del contenitore con le `en-SG` impostazioni locali. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | Immagine del contenitore con le `en-US` impostazioni locali. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | Immagine del contenitore con le `en-ZA` impostazioni locali. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | Immagine del contenitore con le `es-AR` impostazioni locali. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | Immagine del contenitore con le `es-BO` impostazioni locali. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | Immagine del contenitore con le `es-CL` impostazioni locali. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | Immagine del contenitore con le `es-CO` impostazioni locali. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | Immagine del contenitore con le `es-CR` impostazioni locali. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | Immagine del contenitore con le `es-CU` impostazioni locali. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | Immagine del contenitore con le `es-DO` impostazioni locali. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | Immagine del contenitore con le `es-EC` impostazioni locali. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | Immagine del contenitore con le `es-ES` impostazioni locali. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | Immagine del contenitore con le `es-GT` impostazioni locali. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | Immagine del contenitore con le `es-HN` impostazioni locali. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | Immagine del contenitore con le `es-MX` impostazioni locali. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | Immagine del contenitore con le `es-NI` impostazioni locali. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | Immagine del contenitore con le `es-PA` impostazioni locali. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | Immagine del contenitore con le `es-PE` impostazioni locali. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | Immagine del contenitore con le `es-PR` impostazioni locali. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | Immagine del contenitore con le `es-PY` impostazioni locali. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | Immagine del contenitore con le `es-SV` impostazioni locali. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | Immagine del contenitore con le `es-US` impostazioni locali. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | Immagine del contenitore con le `es-UY` impostazioni locali. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | Immagine del contenitore con le `es-VE` impostazioni locali. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | Immagine del contenitore con le `et-EE` impostazioni locali. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | Immagine del contenitore con le `fi-FI` impostazioni locali. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | Immagine del contenitore con le `fr-CA` impostazioni locali. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | Immagine del contenitore con le `fr-FR` impostazioni locali. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | Immagine del contenitore con le `ga-IE` impostazioni locali. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | Immagine del contenitore con le `gu-IN` impostazioni locali. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | Immagine del contenitore con le `hi-IN` impostazioni locali. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | Immagine del contenitore con le `hr-HR` impostazioni locali. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | Immagine del contenitore con le `hu-HU` impostazioni locali. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | Immagine del contenitore con le `it-IT` impostazioni locali. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | Immagine del contenitore con le `ja-JP` impostazioni locali. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | Immagine del contenitore con le `ko-KR` impostazioni locali. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | Immagine del contenitore con le `lt-LT` impostazioni locali. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | Immagine del contenitore con le `lv-LV` impostazioni locali. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | Immagine del contenitore con le `mr-IN` impostazioni locali. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | Immagine del contenitore con le `mt-MT` impostazioni locali. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | Immagine del contenitore con le `nb-NO` impostazioni locali. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | Immagine del contenitore con le `nl-NL` impostazioni locali. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | Immagine del contenitore con le `pl-PL` impostazioni locali. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | Immagine del contenitore con le `pt-BR` impostazioni locali. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | Immagine del contenitore con le `pt-PT` impostazioni locali. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | Immagine del contenitore con le `ro-RO` impostazioni locali. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | Immagine del contenitore con le `ru-RU` impostazioni locali. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | Immagine del contenitore con le `sk-SK` impostazioni locali. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | Immagine del contenitore con le `sl-SI` impostazioni locali. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | Immagine del contenitore con le `sv-SE` impostazioni locali. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | Immagine del contenitore con le `ta-IN` impostazioni locali. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | Immagine del contenitore con le `te-IN` impostazioni locali. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | Immagine del contenitore con le `th-TH` impostazioni locali. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | Immagine del contenitore con le `tr-TR` impostazioni locali. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | Immagine del contenitore con le `zh-CN` impostazioni locali. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | Immagine del contenitore con le `zh-HK` impostazioni locali. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | Immagine del contenitore con le `zh-TW` impostazioni locali. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

Nota sulla versione per `2.6.0-amd64-<locale>` :

**Funzionalità**
* Aggiornamento ai modelli più recenti e migrazione completa a .NET 3,1
* Supporto per l'phrase V2
* Gli elenchi di frasi sono supportati nelle impostazioni locali seguenti:
    * it-au
    * en-CA
    * en-GB
    * it-in
    * it-it
    * zh-cn
* Supporto per le nuove impostazioni locali `cs-CZ` 
    * Le lettere maiuscole e punteggiatura non sono attualmente supportate.

**Correzioni**
* Corregge un problema per cui i punteggi di confidenza sono sempre 1 in modalità di pubblicazione
* Migrated use the TextAnalytics 3,0 API

Si noti che a causa degli elenchi di frasi inclusi, le dimensioni di questa immagine del contenitore sono aumentate. 

Nota sulla versione per `2.5.0-amd64-<locale>` :

**Funzionalità**
* Supporto per il cloud di Azure per il governo degli Stati Uniti

**Correzioni**
* Corregge un problema relativo all'esecuzione come utente non root in modalità di pubblicazione

| Tag immagine                  | Note                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `2.5.0-amd64-en-us`. |


Per questo contenitore sono disponibili le impostazioni locali seguenti.

| Impostazioni locali per v 2.6.0           | Note                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `ar-eg`                     | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `ar-kw`                     | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `ar-qa`                     | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `ar-sa`                     | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `ca-es`                     | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `cs-cz`                     | Immagine del contenitore con le `cs-CZ` impostazioni locali. |
| `da-dk`                     | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `de-de`                     | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `en-au`                     | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `en-ca`                     | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `en-gb`                     | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `en-in`                     | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `en-nz`                     | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `en-us`                     | Immagine del contenitore con le `en-US` impostazioni locali. |
| `es-es`                     | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `es-mx`                     | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `fi-fi`                     | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `fr-ca`                     | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `fr-fr`                     | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `gu-in`                     | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `hi-in`                     | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `it-it`                     | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `ja-jp`                     | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `ko-kr`                     | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `mr-in`                     | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `nb-no`                     | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `nl-nl`                     | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `pl-pl`                     | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `pt-br`                     | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `pt-pt`                     | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `ru-ru`                     | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `sv-se`                     | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `ta-in`                     | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `te-in`                     | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `th-th`                     | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `tr-tr`                     | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `zh-cn`                     | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `zh-hk`                     | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `zh-tw`                     | Immagine del contenitore con le `zh-TW` impostazioni locali. |

| Impostazioni locali per v 2.5.0           | Note                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Immagine del contenitore con le `ar-AE` impostazioni locali. |
| `ar-eg`                     | Immagine del contenitore con le `ar-EG` impostazioni locali. |
| `ar-kw`                     | Immagine del contenitore con le `ar-KW` impostazioni locali. |
| `ar-qa`                     | Immagine del contenitore con le `ar-QA` impostazioni locali. |
| `ar-sa`                     | Immagine del contenitore con le `ar-SA` impostazioni locali. |
| `ca-es`                     | Immagine del contenitore con le `ca-ES` impostazioni locali. |
| `da-dk`                     | Immagine del contenitore con le `da-DK` impostazioni locali. |
| `de-de`                     | Immagine del contenitore con le `de-DE` impostazioni locali. |
| `en-au`                     | Immagine del contenitore con le `en-AU` impostazioni locali. |
| `en-ca`                     | Immagine del contenitore con le `en-CA` impostazioni locali. |
| `en-gb`                     | Immagine del contenitore con le `en-GB` impostazioni locali. |
| `en-in`                     | Immagine del contenitore con le `en-IN` impostazioni locali. |
| `en-nz`                     | Immagine del contenitore con le `en-NZ` impostazioni locali. |
| `en-us`                     | Immagine del contenitore con le `en-US` impostazioni locali. |
| `es-es`                     | Immagine del contenitore con le `es-ES` impostazioni locali. |
| `es-mx`                     | Immagine del contenitore con le `es-MX` impostazioni locali. |
| `fi-fi`                     | Immagine del contenitore con le `fi-FI` impostazioni locali. |
| `fr-ca`                     | Immagine del contenitore con le `fr-CA` impostazioni locali. |
| `fr-fr`                     | Immagine del contenitore con le `fr-FR` impostazioni locali. |
| `gu-in`                     | Immagine del contenitore con le `gu-IN` impostazioni locali. |
| `hi-in`                     | Immagine del contenitore con le `hi-IN` impostazioni locali. |
| `it-it`                     | Immagine del contenitore con le `it-IT` impostazioni locali. |
| `ja-jp`                     | Immagine del contenitore con le `ja-JP` impostazioni locali. |
| `ko-kr`                     | Immagine del contenitore con le `ko-KR` impostazioni locali. |
| `mr-in`                     | Immagine del contenitore con le `mr-IN` impostazioni locali. |
| `nb-no`                     | Immagine del contenitore con le `nb-NO` impostazioni locali. |
| `nl-nl`                     | Immagine del contenitore con le `nl-NL` impostazioni locali. |
| `pl-pl`                     | Immagine del contenitore con le `pl-PL` impostazioni locali. |
| `pt-br`                     | Immagine del contenitore con le `pt-BR` impostazioni locali. |
| `pt-pt`                     | Immagine del contenitore con le `pt-PT` impostazioni locali. |
| `ru-ru`                     | Immagine del contenitore con le `ru-RU` impostazioni locali. |
| `sv-se`                     | Immagine del contenitore con le `sv-SE` impostazioni locali. |
| `ta-in`                     | Immagine del contenitore con le `ta-IN` impostazioni locali. |
| `te-in`                     | Immagine del contenitore con le `te-IN` impostazioni locali. |
| `th-th`                     | Immagine del contenitore con le `th-TH` impostazioni locali. |
| `tr-tr`                     | Immagine del contenitore con le `tr-TR` impostazioni locali. |
| `zh-cn`                     | Immagine del contenitore con le `zh-CN` impostazioni locali. |
| `zh-hk`                     | Immagine del contenitore con le `zh-HK` impostazioni locali. |
| `zh-tw`                     | Immagine del contenitore con le `zh-TW` impostazioni locali. |

---

## <a name="text-to-speech"></a>Sintesi vocale

L'immagine del contenitore [da sintesi vocale][sp-tts] è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `text-to-speech` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Nota sulla versione per `1.9.0-amd64-<locale-and-voice>` :

* Versione mensile regolare

| Tag immagine                                  | Note                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.9.0-amd64-en-us-ariarus`.  |


| Impostazioni locali per v 1.9.0                          | Note                                                                      | Digest                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | Immagine del contenitore con le `ar-EG` impostazioni locali e la `ar-EG-Hoda` voce.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | Immagine del contenitore con le `ar-SA` impostazioni locali e la `ar-SA-Naayf` voce.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | Immagine del contenitore con le `bg-BG` impostazioni locali e la `bg-BG-Ivan` voce.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | Immagine del contenitore con le `ca-ES` impostazioni locali e la `ca-ES-HerenaRUS` voce.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | Immagine del contenitore con le `cs-CZ` impostazioni locali e la `cs-CZ-Jakub` voce.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | Immagine del contenitore con le `da-DK` impostazioni locali e la `da-DK-HelleRUS` voce.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | Immagine del contenitore con le `de-AT` impostazioni locali e la `de-AT-Michael` voce.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | Immagine del contenitore con le `de-CH` impostazioni locali e la `de-CH-Karsten` voce.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | Immagine del contenitore con le `el-GR` impostazioni locali e la `el-GR-Stefanos` voce.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-HeatherRUS` voce.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-Linda` voce.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | Immagine del contenitore con le `en-IE` impostazioni locali e la `en-IE-Sean` voce.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Aria24kRUS` voce.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | Immagine del contenitore con le `fi-FI` impostazioni locali e la `fi-FI-HeidiRUS` voce.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | Immagine del contenitore con le `fr-CH` impostazioni locali e la `fr-CH-Guillaume` voce.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | Immagine del contenitore con le `he-IL` impostazioni locali e la `he-IL-Asaf` voce.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Hemant` voce.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana-Apollo` voce.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | Immagine del contenitore con le `hr-HR` impostazioni locali e la `hr-HR-Matej` voce.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | Immagine del contenitore con le `hu-HU` impostazioni locali e la `hu-HU-Szabolcs` voce.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | Immagine del contenitore con le `id-ID` impostazioni locali e la `id-ID-Andika` voce.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | Immagine del contenitore con le `ms-MY` impostazioni locali e la `ms-MY-Rizwan` voce.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | Immagine del contenitore con le `nb-NO` impostazioni locali e la `nb-NO-HuldaRUS` voce.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | Immagine del contenitore con le `nl-NL` impostazioni locali e la `nl-NL-HannaRUS` voce.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | Immagine del contenitore con le `pl-PL` impostazioni locali e la `pl-PL-PaulinaRUS` voce.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | Immagine del contenitore con le `pt-PT` impostazioni locali e la `pt-PT-HeliaRUS` voce.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | Immagine del contenitore con le `ro-RO` impostazioni locali e la `ro-RO-Andrei` voce.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-EkaterinaRUS` voce.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Irina-Apollo` voce.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Pavel-Apollo` voce.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | Immagine del contenitore con le `sk-SK` impostazioni locali e la `sk-SK-Filip` voce.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | Immagine del contenitore con le `sl-SI` impostazioni locali e la `sl-SI-Lado` voce.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | Immagine del contenitore con le `sv-SE` impostazioni locali e la `sv-SE-HedvigRUS` voce.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | Immagine del contenitore con le `ta-IN` impostazioni locali e la `ta-IN-Valluvar` voce.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | Immagine del contenitore con le `te-IN` impostazioni locali e la `te-IN-Chitra` voce.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | Immagine del contenitore con le `th-TH` impostazioni locali e la `th-TH-Pattara` voce.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | Immagine del contenitore con le `tr-TR` impostazioni locali e la `tr-TR-SedaRUS` voce.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | Immagine del contenitore con le `vi-VN` impostazioni locali e la `vi-VN-An` voce.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Danny-Apollo` voce.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Tracy-Apollo` voce.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-TracyRUS` voce.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-HanHanRUS` voce.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Yating-Apollo` voce.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Zhiwei-Apollo` voce.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[Versione precedente](#tab/previous)

Nota sulla versione per `1.8.0-amd64-<locale-and-voice>` :

**Funzionalità**

* Migrazione completa a .NET 3,1

Nota sulla versione per `1.7.0-amd64-<locale-and-voice>` :

**Funzionalità**

* Componenti aggiornati a .NET 3,1

| Tag immagine                                  | Note                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | 1 ° versione GA. Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.7.0-amd64-en-us-ariarus`.  |


| Impostazioni locali per v 1.8.0                          | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Immagine del contenitore con le `ar-EG` impostazioni locali e la `ar-EG-Hoda` voce.            |
| `ar-sa-naayf`                               | Immagine del contenitore con le `ar-SA` impostazioni locali e la `ar-SA-Naayf` voce.           |
| `bg-bg-ivan`                                | Immagine del contenitore con le `bg-BG` impostazioni locali e la `bg-BG-Ivan` voce.            |
| `ca-es-herenarus`                           | Immagine del contenitore con le `ca-ES` impostazioni locali e la `ca-ES-HerenaRUS` voce.       |
| `cs-cz-jakub`                               | Immagine del contenitore con le `cs-CZ` impostazioni locali e la `cs-CZ-Jakub` voce.           |
| `da-dk-hellerus`                            | Immagine del contenitore con le `da-DK` impostazioni locali e la `da-DK-HelleRUS` voce.        |
| `de-at-michael`                             | Immagine del contenitore con le `de-AT` impostazioni locali e la `de-AT-Michael` voce.         |
| `de-ch-karsten`                             | Immagine del contenitore con le `de-CH` impostazioni locali e la `de-CH-Karsten` voce.         |
| `de-de-hedda`                               | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-heddarus`                            | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-stefan-apollo`                       | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `el-gr-stefanos`                            | Immagine del contenitore con le `el-GR` impostazioni locali e la `el-GR-Stefanos` voce.        |
| `en-au-catherine`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `en-au-hayleyrus`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `en-ca-heatherrus`                          | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-HeatherRUS` voce.      |
| `en-ca-linda`                               | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-Linda` voce.           |
| `en-gb-george-apollo`                       | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `en-gb-hazelrus`                            | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `en-gb-susan-apollo`                        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `en-ie-sean`                                | Immagine del contenitore con le `en-IE` impostazioni locali e la `en-IE-Sean` voce.            |
| `en-in-heera-apollo`                        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `en-in-priyarus`                            | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `en-in-ravi-apollo`                         | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `en-us-benjaminrus`                         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `en-us-guy24krus`                           | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `en-us-aria24krus`                          | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Aria24kRUS` voce.      |
| `en-us-ariarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.         |
| `en-us-zirarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `es-es-helenarus`                           | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `es-es-laura-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `es-es-pablo-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `es-mx-hildarus`                            | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `es-mx-raul-apollo`                         | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `fi-fi-heidirus`                            | Immagine del contenitore con le `fi-FI` impostazioni locali e la `fi-FI-HeidiRUS` voce.        |
| `fr-ca-caroline`                            | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `fr-ca-harmonierus`                         | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `fr-ch-guillaume`                           | Immagine del contenitore con le `fr-CH` impostazioni locali e la `fr-CH-Guillaume` voce.       |
| `fr-fr-hortenserus`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `fr-fr-julie-apollo`                        | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `fr-fr-paul-apollo`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `he-il-asaf`                                | Immagine del contenitore con le `he-IL` impostazioni locali e la `he-IL-Asaf` voce.            |
| `hi-in-hemant`                              | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Hemant` voce.          |
| `hi-in-kalpana-apollo`                      | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana-Apollo` voce.  |
| `hi-in-kalpana`                             | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         |
| `hr-hr-matej`                               | Immagine del contenitore con le `hr-HR` impostazioni locali e la `hr-HR-Matej` voce.           |
| `hu-hu-szabolcs`                            | Immagine del contenitore con le `hu-HU` impostazioni locali e la `hu-HU-Szabolcs` voce.        |
| `id-id-andika`                              | Immagine del contenitore con le `id-ID` impostazioni locali e la `id-ID-Andika` voce.          |
| `it-it-cosimo-apollo`                       | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `it-it-luciarus`                            | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `ja-jp-ayumi-apollo`                        | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `ja-jp-harukarus`                           | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `ja-jp-ichiro-apollo`                       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `ko-kr-heamirus`                            | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `ms-my-rizwan`                              | Immagine del contenitore con le `ms-MY` impostazioni locali e la `ms-MY-Rizwan` voce.          |
| `nb-no-huldarus`                            | Immagine del contenitore con le `nb-NO` impostazioni locali e la `nb-NO-HuldaRUS` voce.        |
| `nl-nl-hannarus`                            | Immagine del contenitore con le `nl-NL` impostazioni locali e la `nl-NL-HannaRUS` voce.        |
| `pl-pl-paulinarus`                          | Immagine del contenitore con le `pl-PL` impostazioni locali e la `pl-PL-PaulinaRUS` voce.      |
| `pt-br-daniel-apollo`                       | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `pt-br-heloisarus`                          | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `pt-pt-heliarus`                            | Immagine del contenitore con le `pt-PT` impostazioni locali e la `pt-PT-HeliaRUS` voce.        |
| `ro-ro-andrei`                              | Immagine del contenitore con le `ro-RO` impostazioni locali e la `ro-RO-Andrei` voce.          |
| `ru-ru-ekaterinarus`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-EkaterinaRUS` voce.    |
| `ru-ru-irina-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Irina-Apollo` voce.    |
| `ru-ru-pavel-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Pavel-Apollo` voce.    |
| `sk-sk-filip`                               | Immagine del contenitore con le `sk-SK` impostazioni locali e la `sk-SK-Filip` voce.           |
| `sl-si-lado`                                | Immagine del contenitore con le `sl-SI` impostazioni locali e la `sl-SI-Lado` voce.            |
| `sv-se-hedvigrus`                           | Immagine del contenitore con le `sv-SE` impostazioni locali e la `sv-SE-HedvigRUS` voce.       |
| `ta-in-valluvar`                            | Immagine del contenitore con le `ta-IN` impostazioni locali e la `ta-IN-Valluvar` voce.        |
| `te-in-chitra`                              | Immagine del contenitore con le `te-IN` impostazioni locali e la `te-IN-Chitra` voce.          |
| `th-th-pattara`                             | Immagine del contenitore con le `th-TH` impostazioni locali e la `th-TH-Pattara` voce.         |
| `tr-tr-sedarus`                             | Immagine del contenitore con le `tr-TR` impostazioni locali e la `tr-TR-SedaRUS` voce.         |
| `vi-vn-an`                                  | Immagine del contenitore con le `vi-VN` impostazioni locali e la `vi-VN-An` voce.              |
| `zh-cn-huihuirus`                           | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `zh-cn-kangkang-apollo`                     | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `zh-cn-yaoyao-apollo`                       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `zh-hk-danny-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Danny-Apollo` voce.    |
| `zh-hk-tracy-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Tracy-Apollo` voce.    |
| `zh-hk-tracyrus`                            | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-TracyRUS` voce.        |
| `zh-tw-hanhanrus`                           | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-HanHanRUS` voce.       |
| `zh-tw-yating-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Yating-Apollo` voce.   |
| `zh-tw-zhiwei-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Zhiwei-Apollo` voce.   |

| Impostazioni locali per v 1.7.0                          | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Immagine del contenitore con le `ar-EG` impostazioni locali e la `ar-EG-Hoda` voce.            |
| `ar-sa-naayf`                               | Immagine del contenitore con le `ar-SA` impostazioni locali e la `ar-SA-Naayf` voce.           |
| `bg-bg-ivan`                                | Immagine del contenitore con le `bg-BG` impostazioni locali e la `bg-BG-Ivan` voce.            |
| `ca-es-herenarus`                           | Immagine del contenitore con le `ca-ES` impostazioni locali e la `ca-ES-HerenaRUS` voce.       |
| `cs-cz-jakub`                               | Immagine del contenitore con le `cs-CZ` impostazioni locali e la `cs-CZ-Jakub` voce.           |
| `da-dk-hellerus`                            | Immagine del contenitore con le `da-DK` impostazioni locali e la `da-DK-HelleRUS` voce.        |
| `de-at-michael`                             | Immagine del contenitore con le `de-AT` impostazioni locali e la `de-AT-Michael` voce.         |
| `de-ch-karsten`                             | Immagine del contenitore con le `de-CH` impostazioni locali e la `de-CH-Karsten` voce.         |
| `de-de-hedda`                               | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-heddarus`                            | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Hedda` voce.           |
| `de-de-stefan-apollo`                       | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-Stefan-Apollo` voce.   |
| `el-gr-stefanos`                            | Immagine del contenitore con le `el-GR` impostazioni locali e la `el-GR-Stefanos` voce.        |
| `en-au-catherine`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-Catherine` voce.       |
| `en-au-hayleyrus`                           | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-HayleyRUS` voce.       |
| `en-ca-heatherrus`                          | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-HeatherRUS` voce.      |
| `en-ca-linda`                               | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-Linda` voce.           |
| `en-gb-george-apollo`                       | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-George-Apollo` voce.   |
| `en-gb-hazelrus`                            | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-HazelRUS` voce.        |
| `en-gb-susan-apollo`                        | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-Susan-Apollo` voce.    |
| `en-ie-sean`                                | Immagine del contenitore con le `en-IE` impostazioni locali e la `en-IE-Sean` voce.            |
| `en-in-heera-apollo`                        | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Heera-Apollo` voce.    |
| `en-in-priyarus`                            | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-PriyaRUS` voce.        |
| `en-in-ravi-apollo`                         | Immagine del contenitore con le `en-IN` impostazioni locali e la `en-IN-Ravi-Apollo` voce.     |
| `en-us-benjaminrus`                         | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-BenjaminRUS` voce.     |
| `en-us-guy24krus`                           | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Guy24kRUS` voce.       |
| `en-us-aria24krus`                          | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-Aria24kRUS` voce.      |
| `en-us-ariarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaRUS` voce.         |
| `en-us-zirarus`                             | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-ZiraRUS` voce.         |
| `es-es-helenarus`                           | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-HelenaRUS` voce.       |
| `es-es-laura-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Laura-Apollo` voce.    |
| `es-es-pablo-apollo`                        | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-Pablo-Apollo` voce.    |
| `es-mx-hildarus`                            | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-HildaRUS` voce.        |
| `es-mx-raul-apollo`                         | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-Raul-Apollo` voce.     |
| `fi-fi-heidirus`                            | Immagine del contenitore con le `fi-FI` impostazioni locali e la `fi-FI-HeidiRUS` voce.        |
| `fr-ca-caroline`                            | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-Caroline` voce.        |
| `fr-ca-harmonierus`                         | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-HarmonieRUS` voce.     |
| `fr-ch-guillaume`                           | Immagine del contenitore con le `fr-CH` impostazioni locali e la `fr-CH-Guillaume` voce.       |
| `fr-fr-hortenserus`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-HortenseRUS` voce.     |
| `fr-fr-julie-apollo`                        | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Julie-Apollo` voce.    |
| `fr-fr-paul-apollo`                         | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-Paul-Apollo` voce.     |
| `he-il-asaf`                                | Immagine del contenitore con le `he-IL` impostazioni locali e la `he-IL-Asaf` voce.            |
| `hi-in-hemant`                              | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Hemant` voce.          |
| `hi-in-kalpana-apollo`                      | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana-Apollo` voce.  |
| `hi-in-kalpana`                             | Immagine del contenitore con le `hi-IN` impostazioni locali e la `hi-IN-Kalpana` voce.         |
| `hr-hr-matej`                               | Immagine del contenitore con le `hr-HR` impostazioni locali e la `hr-HR-Matej` voce.           |
| `hu-hu-szabolcs`                            | Immagine del contenitore con le `hu-HU` impostazioni locali e la `hu-HU-Szabolcs` voce.        |
| `id-id-andika`                              | Immagine del contenitore con le `id-ID` impostazioni locali e la `id-ID-Andika` voce.          |
| `it-it-cosimo-apollo`                       | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-Cosimo-Apollo` voce.   |
| `it-it-luciarus`                            | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-LuciaRUS` voce.        |
| `ja-jp-ayumi-apollo`                        | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ayumi-Apollo` voce.    |
| `ja-jp-harukarus`                           | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-HarukaRUS` voce.       |
| `ja-jp-ichiro-apollo`                       | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-Ichiro-Apollo` voce.   |
| `ko-kr-heamirus`                            | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-HeamiRUS` voce.        |
| `ms-my-rizwan`                              | Immagine del contenitore con le `ms-MY` impostazioni locali e la `ms-MY-Rizwan` voce.          |
| `nb-no-huldarus`                            | Immagine del contenitore con le `nb-NO` impostazioni locali e la `nb-NO-HuldaRUS` voce.        |
| `nl-nl-hannarus`                            | Immagine del contenitore con le `nl-NL` impostazioni locali e la `nl-NL-HannaRUS` voce.        |
| `pl-pl-paulinarus`                          | Immagine del contenitore con le `pl-PL` impostazioni locali e la `pl-PL-PaulinaRUS` voce.      |
| `pt-br-daniel-apollo`                       | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-Daniel-Apollo` voce.   |
| `pt-br-heloisarus`                          | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-HeloisaRUS` voce.      |
| `pt-pt-heliarus`                            | Immagine del contenitore con le `pt-PT` impostazioni locali e la `pt-PT-HeliaRUS` voce.        |
| `ro-ro-andrei`                              | Immagine del contenitore con le `ro-RO` impostazioni locali e la `ro-RO-Andrei` voce.          |
| `ru-ru-ekaterinarus`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-EkaterinaRUS` voce.    |
| `ru-ru-irina-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Irina-Apollo` voce.    |
| `ru-ru-pavel-apollo`                        | Immagine del contenitore con le `ru-RU` impostazioni locali e la `ru-RU-Pavel-Apollo` voce.    |
| `sk-sk-filip`                               | Immagine del contenitore con le `sk-SK` impostazioni locali e la `sk-SK-Filip` voce.           |
| `sl-si-lado`                                | Immagine del contenitore con le `sl-SI` impostazioni locali e la `sl-SI-Lado` voce.            |
| `sv-se-hedvigrus`                           | Immagine del contenitore con le `sv-SE` impostazioni locali e la `sv-SE-HedvigRUS` voce.       |
| `ta-in-valluvar`                            | Immagine del contenitore con le `ta-IN` impostazioni locali e la `ta-IN-Valluvar` voce.        |
| `te-in-chitra`                              | Immagine del contenitore con le `te-IN` impostazioni locali e la `te-IN-Chitra` voce.          |
| `th-th-pattara`                             | Immagine del contenitore con le `th-TH` impostazioni locali e la `th-TH-Pattara` voce.         |
| `tr-tr-sedarus`                             | Immagine del contenitore con le `tr-TR` impostazioni locali e la `tr-TR-SedaRUS` voce.         |
| `vi-vn-an`                                  | Immagine del contenitore con le `vi-VN` impostazioni locali e la `vi-VN-An` voce.              |
| `zh-cn-huihuirus`                           | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-HuihuiRUS` voce.       |
| `zh-cn-kangkang-apollo`                     | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Kangkang-Apollo` voce. |
| `zh-cn-yaoyao-apollo`                       | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-Yaoyao-Apollo` voce.   |
| `zh-hk-danny-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Danny-Apollo` voce.    |
| `zh-hk-tracy-apollo`                        | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-Tracy-Apollo` voce.    |
| `zh-hk-tracyrus`                            | Immagine del contenitore con le `zh-HK` impostazioni locali e la `zh-HK-TracyRUS` voce.        |
| `zh-tw-hanhanrus`                           | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-HanHanRUS` voce.       |
| `zh-tw-yating-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Yating-Apollo` voce.   |
| `zh-tw-zhiwei-apollo`                       | Immagine del contenitore con le `zh-TW` impostazioni locali e la `zh-TW-Zhiwei-Apollo` voce.   |

---

## <a name="neural-text-to-speech"></a>Sintesi vocale neurale

L'immagine del contenitore [di sintesi vocale neurale][sp-ntts] è disponibile nel Syndicate del `mcr.microsoft.com` Registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `neural-text-to-speech` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list)versione.


# <a name="latest-version"></a>[Versione più recente](#tab/current)

Note sulla versione per `v1.3.0` :
* Il contenitore da testo a voce neurale è ora disponibile a livello generale. 

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `1.3.0-amd64-<locale-and-voice>`    | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0 impostazioni locali e voci           | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-KatjaNeural` voce.     |
| `en-au-natashaneural`               | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-NatashaNeural` voce.   |
| `en-ca-claraneural`                 | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-ClaraNeural` voce.     |
| `en-gb-libbyneural`                 | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-LibbyNeural` voce.     |
| `en-gb-mianeural`                   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-MiaNeural` voce.       |
| `en-us-arianeural`                  | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `en-us-guyneural`                   | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-GuyNeural` voce.       |
| `es-es-elviraneural`                | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-ElviraNeural` voce.    |
| `es-mx-dalianeural`                 | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-DaliaNeural` voce.     |
| `fr-ca-sylvieneural`                | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-SylvieNeural` voce.    |
| `fr-fr-deniseneural`                | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-DeniseNeural` voce.    |
| `it-it-elsaneural`                  | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-ElsaNeural` voce.      |
| `ja-jp-nanamineural`                | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-NanamiNeural` voce.    |
| `ko-kr-sunhineural`                 | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-SunHiNeural` voce.     |
| `pt-br-franciscaneural`             | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-FranciscaNeural` voce. |
| `zh-cn-xiaoxiaoneural`              | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-XiaoxiaoNeural` voce.  |

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Sostituire `<locale>` con una delle impostazioni locali disponibili, elencate di seguito. Ad esempio `1.2.0-amd64-en-us-arianeural-preview`. |


| v 1.2.0 Anteprima impostazioni locali e voci           | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `de-de-katjaneural-preview`                 | Immagine del contenitore con le `de-DE` impostazioni locali e la `de-DE-KatjaNeural` voce.     |
| `en-au-natashaneural-preview`               | Immagine del contenitore con le `en-AU` impostazioni locali e la `en-AU-NatashaNeural` voce.   |
| `en-ca-claraneural-preview`                 | Immagine del contenitore con le `en-CA` impostazioni locali e la `en-CA-ClaraNeural` voce.     |
| `en-gb-libbyneural-preview`                 | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-LibbyNeural` voce.     |
| `en-gb-mianeural-preview`                   | Immagine del contenitore con le `en-GB` impostazioni locali e la `en-GB-MiaNeural` voce.       |
| `en-us-arianeural-preview`                  | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-AriaNeural` voce.      |
| `en-us-guyneural-preview`                   | Immagine del contenitore con le `en-US` impostazioni locali e la `en-US-GuyNeural` voce.       |
| `es-es-elviraneural-preview`                | Immagine del contenitore con le `es-ES` impostazioni locali e la `es-ES-ElviraNeural` voce.    |
| `es-mx-dalianeural-preview`                 | Immagine del contenitore con le `es-MX` impostazioni locali e la `es-MX-DaliaNeural` voce.     |
| `fr-ca-sylvieneural-preview`                | Immagine del contenitore con le `fr-CA` impostazioni locali e la `fr-CA-SylvieNeural` voce.    |
| `fr-fr-deniseneural-preview`                | Immagine del contenitore con le `fr-FR` impostazioni locali e la `fr-FR-DeniseNeural` voce.    |
| `it-it-elsaneural-preview`                  | Immagine del contenitore con le `it-IT` impostazioni locali e la `it-IT-ElsaNeural` voce.      |
| `ja-jp-nanamineural-preview`                | Immagine del contenitore con le `ja-JP` impostazioni locali e la `ja-JP-NanamiNeural` voce.    |
| `ko-kr-sunhineural-preview`                 | Immagine del contenitore con le `ko-KR` impostazioni locali e la `ko-KR-SunHiNeural` voce.     |
| `pt-br-franciscaneural-preview`             | Immagine del contenitore con le `pt-BR` impostazioni locali e la `pt-BR-FranciscaNeural` voce. |
| `zh-cn-xiaoxiaoneural-preview`              | Immagine del contenitore con le `zh-CN` impostazioni locali e la `zh-CN-XiaoxiaoNeural` voce.  |

---

## <a name="speech-language-detection"></a>Rilevamento della lingua parlata

L'immagine del contenitore del [rilevamento della lingua vocale][sp-lid] è reperibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/speechservices/` repository ed è denominato `language-detection` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)versione.

| Tag immagine                                  | Note                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Estrazione frasi chiave

è possibile trovare l'immagine del contenitore nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/textanalytics/` repository ed è denominato `keyphrase` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)versione.

# <a name="latest-version"></a>[Versione più recente](#tab/current)


| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Rilevamento della lingua del testo

Il [rilevamento lingua][ta-la] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/textanalytics/` repository ed è denominato `language` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)versione.

# <a name="latest-versions"></a>[Versioni più recenti](#tab/current)

| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Versioni precedenti](#tab/previous)


| Tag immagine                    | Note |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Analisi del sentiment

Il [analisi del sentiment][ta-se] immagine del contenitore è disponibile nel `mcr.microsoft.com` Syndicate del registro contenitori. Si trova all'interno del `azure-cognitive-services/textanalytics/` repository ed è denominato `sentiment` . Il nome completo dell'immagine del contenitore è, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Questa immagine del contenitore include i seguenti tag disponibili. È anche possibile trovare un elenco completo di [tag per la](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)versione.

| Tag immagine | Note                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Analisi del sentiment V3 (Inglese)               |
| `3.0-es`   | Analisi del sentiment V3 (Spagnolo)               |
| `3.0-fr`   | Analisi del sentiment V3 (Francese)                |
| `3.0-it`   | Analisi del sentiment V3 (Italiano)               |
| `3.0-de`   | Analisi del sentiment V3 (tedesco)                |
| `3.0-zh`   | Analisi del sentiment V3 (cinese semplificato)  |
| `3.0-zht`  | Analisi del sentiment V3 (cinese tradizionale) |
| `3.0-ja`   | Analisi del sentiment V3 (giapponese)              |
| `3.0-pt`   | Analisi del sentiment V3 (Portoghese)            |
| `3.0-nl`   | Analisi del sentiment V3 (Olandese)                 |
| `2.1`    | Analisi del sentiment v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
