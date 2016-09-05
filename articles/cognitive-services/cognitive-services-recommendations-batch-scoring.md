
<properties
	pageTitle="Acquisizione di consigli in batch: API Recommendations di Machine Learning | Microsoft Azure"
	description="Consigli di Azure Machine Learning: acquisizione di consigli in batch"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="luisca"/>

# Ottenere consigli in batch

>[AZURE.NOTE] Ottenere consigli in batch è più complicato che ottenere un singolo consiglio alla volta. Cercare nelle API informazioni su come ottenere consigli per una singola richiesta:

> [Item-to-Item recommendations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4) (Consigli da elemento a elemento)<br> [User-to-Item recommendations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd) (Consigli da utente a elemento)
>
> Il punteggio batch funziona solo per le build create dopo il 21 luglio 2016.


In alcune situazioni è necessario ottenere consigli per più di un elemento alla volta. È ad esempio possibile che si voglia creare una cache di consigli oppure analizzare i tipi di consigli ottenuti.

Le operazioni di assegnazione del punteggio batch, come vengono chiamate, sono operazioni asincrone. È necessario inviare la richiesta, attendere la fine dell'operazione e quindi recuperare i risultati.

Per una maggiore precisione, questi sono i passaggi da seguire:

1.	Creare un contenitore di archiviazione di Azure, se non è già disponibile.
2.	Caricare un file di input che descrive tutte le richieste di consigli all'archivio BLOB di Azure.
3.	Avviare il processo di assegnazione del punteggio batch.
4.	Attendere la fine dell'operazione asincrona.
5.	Al termine dell'operazione, raccogliere i risultati dall'archivio BLOB.

Di seguito viene illustrato ognuno di questi passaggi.

## Creare un contenitore di archiviazione se non è già disponibile

Andare al [portale di Azure](https://portal.azure.com) e creare un nuovo account di archiviazione, se necessario. A questo scopo, andare a **Nuovo** > **Dati** e **archiviazione** > **Account di archiviazione**.

Dopo aver creato un account di archiviazione, è necessario creare i contenitori BLOB in cui verranno archiviati l'input e l'output dell'esecuzione batch.

Caricare un file di input che descrive tutte le richieste di consigli all'archivio BLOB. In questo caso, il nome del file è input.json. Dopo aver creato un contenitore, è necessario caricare un file che descrive tutte le richieste da eseguire dal servizio Consigli.

Un batch può eseguire un solo tipo di richiesta da una specifica build. Nella sezione successiva verrà illustrato come definire queste informazioni. Per il momento, si presupporrà che i consigli sugli elementi vengano eseguiti esternamente a una build specifica. Il file di input contiene quindi le informazioni di input, in questo caso gli elementi seme, per ognuna delle richieste.

Di seguito è illustrato un esempio di file input.json:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Come si può notare, si tratta di un file JSON nel quale ognuna delle richieste ha le informazioni necessarie per inviare una richiesta di consigli. Creare un file JSON simile per le proprie richieste e copiarlo nel contenitore appena creato nell'archivio BLOB.

## Avviare il processo batch

Il passaggio successivo consiste nell'inviare un nuovo processo batch. Per altre informazioni, vedere le [informazioni di riferimento sulle API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

Il corpo della richiesta dell'API deve definire le posizioni in cui archiviare i file di input, output e di errore. Deve definire anche le credenziali necessarie per accedere a tali posizioni. Sarà anche necessario specificare alcuni parametri che riguardano l'intero batch, ovvero il tipo di consigli da richiedere, il modello/build da usare, il numero di risultati per ogni chiamata e così via.

Il corpo della richiesta sarà simile al seguente:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Ecco alcuni aspetti importanti da notare:

-	**authenticationType** deve essere attualmente sempre impostato su **PublicOrSas**.

-	È necessario ottenere un token di firma di accesso condiviso per consentire all'API Recommendations di leggere e scrivere da e verso l'account di archiviazione BLOB. Altre informazioni su come generare i token di firma di accesso condiviso sono disponibili nella [pagina dell'API Recommendations](../storage/storage-dotnet-shared-access-signature-part-1.md).

-	L'unico **apiName** attualmente supportato è **ItemRecommend**, usato per i consigli da elemento a elemento. L'invio in batch attualmente non supporta i consigli da utente a elemento.

## Attendere la fine dell'operazione asincrona

Quando si avvia l'operazione batch, la risposta restituisce l'intestazione Operation-Location con le informazioni necessarie per tenere traccia dell'operazione. È possibile tenere traccia dell'operazione usando l'[API per il recupero dello stato operazione](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), come nel caso di un'operazione di compilazione.

## Ottenere i risultati

Al termine dell'operazione, supponendo che non si siano verificati errori, è possibile raccogliere i risultati dall'archivio BLOB di output.

L'esempio seguente illustra come potrebbe essere l'output. Questo esempio descrive i risultati per un batch con solo due richieste, per brevità.

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## Informazioni sulle limitazioni

-	È possibile chiamare un solo processo batch per sottoscrizione alla volta.
-	Un file di input del processo batch non può essere di dimensioni superiori a 2 MB.

<!---HONumber=AcomDC_0824_2016-->