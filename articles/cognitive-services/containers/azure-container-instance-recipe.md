---
title: File recipe istanza di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire i contenitori di servizi cognitivi sull'istanza di contenitore di Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: 5de11b62bb2a2302c247907627e27339400fcf25
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207491"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Distribuire ed eseguire contenitori in Azure istanza di contenitore

Con la procedura seguente, la scalabilità delle applicazioni di servizi cognitivi di Azure nel cloud con facilità con Azure [istanza di contenitore](https://docs.microsoft.com/azure/container-instances/) (ACI). Ciò consente di concentrarsi sulla compilazione di applicazioni anziché dover gestire l'infrastruttura.

## <a name="prerequisites"></a>Prerequisiti

Questa soluzione funziona con qualsiasi contenitore di servizi cognitivi. La risorsa di servizi cognitivi deve essere creata nel portale di Azure prima di usare questa soluzione. Ogni servizio cognitivo che supporti i contenitori dispone di un documento "Come installare" in modo specifico per l'installazione e configurazione del servizio per un contenitore. Poiché alcuni servizi richiedono un file o set di file come input per il contenitore, è importante capire e hanno utilizzati correttamente il contenitore prima di usare questa soluzione.

* Una risorsa di servizi cognitivi, creata nel portale di Azure.
* Servizi cognitivi **URL dell'endpoint** -esaminare le specifiche del servizio "come installare" per il contenitore, per individuare dove l'URL dell'endpoint consiste nell'usare il portale di Azure e ciò che un esempio corretto dell'URL è simile. Il formato esatto può cambiare da servizio a servizio.
* Servizi cognitivi **key** -le chiavi sono nel **chiavi** pagina per la risorsa di Azure. È necessaria solo una delle due chiavi. La chiave è una stringa di 32 caratteri alfanumerici.
* Un singolo Cognitive Services contenitore nell'host locale (computer). Verificare che è possibile:
  * Acquisisce l'immagine con un `docker pull` comando.
  * Eseguire il contenitore locale correttamente con tutte le impostazioni di configurazione necessarie con un `docker run` comando.
  * Chiamare l'endpoint del contenitore, ricevendo una risposta di tipo 2xx e una risposta JSON.

Tutte le variabili di parentesi angolari, `<>`, devono essere sostituiti con i propri valori. Questa sostituzione include le parentesi acute.

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>Passaggio 2: Avviare il contenitore in istanze di contenitore di Azure (ACI)

**Creazione della risorsa di istanza di contenitore di Azure (ACI).**

1. Andare alla [Create](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) pagina per istanze di contenitore.

1. Nel **nozioni di base** , immettere i dettagli seguenti:

    |Page|Impostazione|Value|
    |--|--|--|
    |Nozioni di base|Sottoscrizione|Selezionare la propria sottoscrizione.|
    |Nozioni di base|Gruppo di risorse|Selezionare il gruppo di risorse disponibili oppure crearne uno nuovo, ad esempio `cognitive-services`.|
    |Nozioni di base|Nome contenitore|Immettere un nome, ad esempio `cognitive-container-instance`. Questo nome deve essere nell'estremità inferiore.|
    |Nozioni di base|Località|Selezionare un'area per la distribuzione.|
    |Nozioni di base|Tipo di immagine|`Public`|
    |Nozioni di base|Nome dell'immagine|Immettere il percorso del contenitore di servizi cognitivi. Può essere usato nella stessa posizione di `docker pull` comando _ad esempio_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Nozioni di base|Tipo di sistema operativo|`Linux`|
    |Nozioni di base|Dimensione|Modificare le dimensioni per le raccomandazioni suggerite per il contenitore di servizi cognitivi specifico.:<br>2 core<br>4 GB
    ||||
  
1. Nel **Networking** , immettere i dettagli seguenti:

    |Page|Impostazione|Value|
    |--|--|--|
    |Rete|Porte|Modificare la porta esistente per il protocollo TCP da `80` a `5000`. Ciò significa che si sta esponendo il contenitore sulla porta 5000.|
    ||||

1. Nel **avanzate** , immettere i dettagli seguenti per passare attraverso il contenitore necessarie impostazioni nella risorsa di istanza di contenitore di fatturazione:

    |Chiave della pagina avanzate|Valore di pagina avanzate|
    |--|--|
    |`apikey`|Copiato dal **chiavi** pagina della risorsa. È necessaria solo una delle due chiavi. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiato dal **Panoramica** pagina della risorsa. |
    |`eula`|`accept`|

    Se il contenitore dispone di altre impostazioni di configurazione, ad esempio punti di montaggio di input, punti di montaggio di output o la registrazione, tali impostazioni anche devono essere aggiunti.

1. Selezionare **esaminare e creare**.
1. Dopo la convalida ha esito positivo, selezionare **Create** per completare il processo di creazione.
1. Selezionare l'icona a forma di campana nella barra di spostamento superiore. Si tratta dell'intervallo di notifica. Verrà visualizzato di colore blu **Vai alla risorsa** dopo che la risorsa viene creata. Selezionare questo pulsante per passare alla nuova risorsa.

## <a name="use-the-container-instance"></a>Usare l'istanza di contenitore

1. Selezionare il **Panoramica** e copiare l'indirizzo IP. Sarà un indirizzo IP numerico, ad esempio `55.55.55.55`.
1. Aprire una nuova scheda del browser e usare l'indirizzo IP, ad esempio, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Noterai home page del contenitore per comunicarti che il contenitore è in esecuzione.

1. Selezionare **descrizione del servizio API** per visualizzare la pagina di swagger per il contenitore.

1. Selezionare una qualsiasi delle **POST** API e selezionare **provarlo**.  Vengono visualizzati i parametri, tra cui l'input. Specificare i parametri.

1. Selezionare **Execute** per inviare la richiesta all'istanza di contenitore.

    Aver creato e usato i contenitori di servizi cognitivi nell'istanza di contenitore di Azure.
