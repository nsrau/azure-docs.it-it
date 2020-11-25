---
title: Configurare i contenitori di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Il servizio riconoscimento vocale fornisce a ogni contenitore un Framework di configurazione comune, che consente di configurare e gestire facilmente le impostazioni di archiviazione, registrazione e telemetria e di sicurezza per i contenitori.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012170"
---
# <a name="configure-speech-service-containers"></a>Configurare i contenitori di servizi vocali

I contenitori di sintesi vocale consentono ai clienti di creare un'architettura di applicazione vocale ottimizzata per sfruttare le funzionalità cloud affidabili e la località perimetrale. I cinque contenitori di sintesi vocale supportati sono ora **, sintesi** vocale, riconoscimento **vocale personalizzato**, sintesi vocale, da sintesi vocale **text-to-speech**, **da** sintesi vocale e da testo a discorso **e da sintesi** vocale.

L'ambiente di runtime del contenitore **vocale** viene configurato usando gli `docker run` argomenti del comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) Impostazioni, e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutti e tre gli elementi; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](speech-container-howto.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave valida per la risorsa _vocale_ specificata per l' [`Billing`](#billing-configuration-setting) impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

- Portale di Azure: gestione delle risorse **vocale** , in **chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L' `Billing` impostazione specifica l'URI dell'endpoint della risorsa _vocale_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa _vocale_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

- Portale di Azure: Panoramica **vocale** , con etichetta `Endpoint`

| Necessario | Nome | Tipo di dati | Descrizione |
| -------- | ---- | --------- | ----------- |
| Sì | `Billing` | string | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [raccolta dei parametri obbligatori](speech-container-howto.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Impostazioni delle credenziali del proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

I contenitori di sintesi vocale standard non usano i montaggi di input o output per archiviare i dati di training o di servizio. Tuttavia, i contenitori di riconoscimento vocale personalizzati si basano sui montaggi del volume.

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](speech-container-howto.md#the-host-computer)potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host.

| Facoltativo | Nome | Tipo di dati | Descrizione |
| -------- | ---- | --------- | ----------- |
| Non consentito | `Input` | string | I contenitori di sintesi vocale standard non lo usano. I contenitori di riconoscimento vocale personalizzati utilizzano i [montaggi del volume](#volume-mount-settings).                                                                                    |
| Facoltativo | `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Impostazioni di montaggio del volume

I contenitori di riconoscimento vocale personalizzati utilizzano i [montaggi del volume](https://docs.docker.com/storage/volumes/) per salvare in modo permanente i modelli personalizzati. È possibile specificare un montaggio del volume aggiungendo l' `-v` opzione (o `--volume` ) al comando [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

I modelli personalizzati vengono scaricati la prima volta che un nuovo modello viene inserito come parte del comando Docker Run del contenitore vocale personalizzato. Le esecuzioni sequenziali dello stesso `ModelId` per un contenitore vocale personalizzato utilizzeranno il modello scaricato in precedenza. Se non viene fornito il montaggio del volume, i modelli personalizzati non possono essere resi permanente.

L'impostazione di montaggio del volume è costituita da tre `:` campi separati da colori:

1. Il primo campo è il nome del volume nel computer host, ad esempio _C:\input_.
2. Il secondo campo è la directory nel contenitore, ad esempio _/usr/local/Models_.
3. Il terzo campo (facoltativo) è un elenco di opzioni delimitato da virgole. per altre informazioni, vedere [usare i volumi](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Esempio di montaggio del volume

```bash
-v C:\input:/usr/local/models
```

Questo comando monta la directory _C:\input_ del computer host nella directory _/usr/local/Models_ dei contenitori.

> [!IMPORTANT]
> Le impostazioni di montaggio del volume sono valide solo per i contenitori da **riconoscimento vocale personalizzato a testo** e **da testo a voce personalizzato** . I contenitori di sintesi **vocale, sintesi vocale e** **sintesi vocale non** usano i montaggi **di** volumi, né i contenitori di sintesi vocale.

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`. Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](speech-container-howto.md#stop-the-container).

- **Carattere di continuazione di riga**: i comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\` , come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host.
- **Ordine** degli argomenti: non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | Valore | Formato o esempio |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Chiave dell'endpoint della `Speech` risorsa nella `Speech` pagina chiavi di Azure.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | Il valore dell'endpoint di fatturazione è disponibile nella `Speech` pagina Panoramica di Azure. | Vedere [raccolta di parametri obbligatori](speech-container-howto.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia. Per altre informazioni, vedere[Fatturazione](#billing-configuration-setting).
> Il valore di ApiKey è la **chiave** della pagina delle chiavi delle risorse vocali di Azure.

## <a name="speech-container-docker-examples"></a>Esempi di Docker del contenitore vocale

Gli esempi di Docker seguenti sono per il contenitore di riconoscimento vocale.

## <a name="speech-to-text"></a>[Riconoscimento vocale](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Esempio di base per la sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Esempio di registrazione per sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Da Riconoscimento vocale personalizzato a testo](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Esempio di base per Riconoscimento vocale personalizzato-to-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Esempio di registrazione per Riconoscimento vocale personalizzato-to-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Sintesi vocale](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Esempio di base per sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Esempio di registrazione per sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Esempio di base per sintesi vocale personalizzata

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Esempio di registrazione per sintesi vocale personalizzata

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[Sintesi vocale neurale](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>Esempio di base per sintesi vocale neurale

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>Esempio di registrazione per sintesi vocale neurale

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[Rilevamento lingua vocale](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>Esempio di base per il rilevamento della lingua vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>Esempio di registrazione per il rilevamento della lingua vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Come installare ed eseguire i contenitori](speech-container-howto.md)
