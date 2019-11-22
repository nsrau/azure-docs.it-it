---
title: 'Esercitazione: Usare Riconoscimento modulo con App per la logica di Azure per analizzare le fatture - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si userà Riconoscimento moduli con App per la logica di Azure per creare un flusso di lavoro che automatizzi il processo di training e di test di un modello usando dati di esempio.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: bf6efbdbe19be28c005e2081c99827734ef10174
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177004"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Esercitazione: Usare Riconoscimento modulo con App per la logica di Azure per analizzare le fatture

In questa esercitazione viene creato un flusso di lavoro in App per la logica di Azure che usa Riconoscimento modulo, un servizio che fa parte della suite di Servizi cognitivi di Azure, per estrarre i dati dalle fatture. È possibile usare Riconoscimento modulo prima per eseguire il training di un modello usando un set di dati di esempio e quindi per eseguire il test del modello usando un altro set di dati. I dati di esempio usati in questa esercitazione vengono archiviati in contenitori BLOB di Archiviazione di Azure.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Richiedere l'accesso per Riconoscimento modulo
> * Creare un contenitore BLOB di Archiviazione di Azure
> * Caricare i dati di esempio nel contenitore BLOB di Azure
> * Creare un'app per la logica di Azure
> * Configurare l'app per la logica per l'uso di una risorsa di Riconoscimento modulo
> * Testare il flusso di lavoro eseguendo l'app per la logica

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/).

## <a name="request-access-for-form-recognizer"></a>Richiedere l'accesso per Riconoscimento modulo

Il riconoscimento modulo è disponibile in anteprima ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess). Dopo l'approvazione della richiesta da parte del team di Servizi cognitivi di Azure, si riceverà un messaggio di posta elettronica con le istruzioni per l'accesso al servizio.

## <a name="understand-the-invoice-to-be-analyzed"></a>Comprendere la fattura da analizzare

Il set di dati di esempio usato per il training e il test del modello è disponibile come file ZIP da [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Scaricare ed estrarre il file ZIP e aprire un file PDF della fattura nella cartella **/Train**. Notare la tabella con il numero di fattura, la data della fattura e così via. 

> [!div class="mx-imgBorder"]
> ![Esempio di fattura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Questa esercitazione illustra come estrarre le informazioni da tali tabelle in un formato JSON usando un flusso di lavoro creato con App per la logica di Azure e Riconoscimento modulo.

## <a name="create-an-azure-storage-blob-container"></a>Creare un contenitore BLOB di Archiviazione di Azure

Questo contenitore viene usato per caricare i dati di esempio necessari per eseguire il training del modello.

1. Seguire le istruzioni riportate in [Creare un account di Archiviazione di Azure](../../storage/common/storage-quickstart-create-account.md) per creare un account di archiviazione. Usare **formrecostorage** come nome dell'account di archiviazione.
1. Seguire le istruzioni riportate in [Creare un contenitore BLOB di Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) per creare un contenitore all'interno dell'account di Archiviazione di Azure. Usare **formrecocontainer** come nome del contenitore. Assicurarsi di impostare il livello di accesso pubblico su **Contenitore (accesso in lettura anonimo per contenitori e BLOB)** .

    > [!div class="mx-imgBorder"]
    > ![Creare un contenitore BLOB](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Caricare i dati di esempio nel contenitore BLOB di Azure

Scaricare i dati di esempio disponibili in [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Estrarre i dati in una cartella locale e caricare il contenuto della cartella **/Train** nel contenitore **formrecocontainer** creato in precedenza. Seguire le istruzioni riportate in [Caricare un BLOB in blocchi](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) per caricare i dati in un contenitore.

Copiare l'URL del contenitore. Sarà necessario usarlo più avanti in questa esercitazione. Se è stato creato l'account di archiviazione e il contenitore con gli stessi nomi elencati in questa esercitazione, l'URL sarà *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/* .

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Creare l'app per la logica

È possibile usare App per la logica di Azure per automatizzare e orchestrare le attività e i flussi di lavoro. In questa esercitazione si creerà un'app per la logica che viene attivata ricevendo una fattura che si vuole analizzare come allegato di posta elettronica. In questo flusso di lavoro si eseguono le attività seguenti:
* Configurare l'app per la logica per l'attivazione automatica quando si riceve un messaggio di posta elettronica con una fattura allegata.
* Configurare l'app per la logica per l'uso di un'operazione di **training del modello** di Riconoscimento modulo per eseguire il training di un modello usando i dati di esempio caricati in Archiviazione BLOB di Azure.
* Configurare l'app per la logica per l'uso di un'operazione di **analisi del modulo** di Riconoscimento modulo per usare il modello di cui è già stato eseguito il training. Questo componente analizzerà la fattura fornita a questa app per la logica in base al modello di cui è stato eseguito il training in precedenza.

Iniziamo! Attenersi alla seguente procedura per configurare il flusso di lavoro.

1. Dal menu principale di Azure selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.

1. In **Crea app per la logica** specificare i dettagli sull'app per la logica come mostrato di seguito. Al termine, selezionare **Crea**.

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **Nome** | <*logic-app-name*> | Il nome dell'app per la logica, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). Questo esempio usa "My-First-Logic-app". |
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | Il nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | <*Azure-resource-group-name*> | Nome del [gruppo di risorse di Azure](./../../azure-resource-manager/resource-group-overview.md) usato per organizzare le risorse correlate. Questo esempio usa "My-First-LA-RG". |
   | **Posizione** | <*Area di Azure*> | L'area in cui archiviare le informazioni sull'app per la logica. Questo esempio usa "Stati Uniti occidentali". |
   | **Log Analytics** | Off | Lasciare l'impostazione **No** per la registrazione diagnostica. |
   ||||

1. Dopo che Azure ha distribuito l'app, sulla barra degli strumenti di Azure selezionare **Notifiche** > **Vai alla risorsa** per l'app per la logica distribuita. In alternativa, è possibile trovare e selezionare l'app per la logica digitando il nome nella casella di ricerca.

   Viene visualizzata la finestra Progettazione app per la logica, che mostra una pagina con un video introduttivo e i trigger più usati. In **Modelli** selezionare **App per la logica vuota**.

   > [!div class="mx-imgBorder"]
   > ![Selezionare il modello vuoto per l'app per la logica](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Configurare l'app per la logica per attivare il flusso di lavoro quando arriva un messaggio di posta elettronica

In questa esercitazione viene attivato il flusso di lavoro quando viene ricevuto un messaggio di posta elettronica con una fattura allegata. Per questa esercitazione viene usato Office 365 come servizio di posta elettronica, ma è possibile usare qualsiasi altro provider di posta elettronica che si vuole usare.

1. Nelle schede selezionare Tutti, selezionare **Office 365 Outlook** e quindi in **Trigger**selezionare **All'arrivo di un nuovo messaggio di posta elettronica**.

    ![Trigger dell'app per la logica tramite un messaggio di posta elettronica in arrivo](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Nella finestra **Office 365 Outlook** fare clic su **Accedi** e immettere i dettagli per accedere a un account di Office 365.

1. Nella finestra di dialogo successiva seguire questa procedura.
    1. Selezionare la cartella da monitorare per ogni nuovo messaggio di posta elettronica.
    1. Per **Con allegati** selezionare **Sì**. Ciò garantisce che solo i messaggi di posta elettronica con allegati attivino il flusso di lavoro.
    1. Per **Includi allegati** selezionare **Sì**. In questo modo si garantisce che il contenuto dell'allegato venga usato nell'elaborazione downstream.

        > [!div class="mx-imgBorder"]
        > ![Configurare il trigger di posta elettronica dell'app per la logica](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Fare clic su **Salva** nella barra degli strumenti in alto.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Configurare l'app per la logica per l'uso di un'operazione di training del modello di Riconoscimento modulo

Prima di poter usare il servizio Riconoscimento modulo per analizzare le fatture, è necessario eseguire il training di un modello fornendo alcuni dati di esempio che possono essere analizzati e acquisiti dal modello.

1. Selezionare **Nuovo passaggio** e in **Scegliere un'azione** cercare **Riconoscimento modulo**. Nei risultati visualizzati selezionare **Riconoscimento modulo** e quindi nelle azioni disponibili per Riconoscimento modulo selezionare **Train Model** (Training del modello).

    > [!div class="mx-imgBorder"]
    > ![Eseguire il training di un modello di Riconoscimento modulo](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Nella finestra di dialogo Riconoscimento modulo specificare un nome per la connessione e immettere l'URL dell'endpoint e la chiave recuperata per la risorsa di Riconoscimento modulo.

    > [!div class="mx-imgBorder"]
    > ![Nome della connessione per Riconoscimento modulo](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Fare clic su **Create**(Crea).

1. Nella finestra di dialogo **Train Model** (Training del modello) in **Origine** immettere l'URL del contenitore in cui sono stati caricati i dati di esempio.

    > [!div class="mx-imgBorder"]
    > ![Contenitore di archiviazione per le fatture di esempio](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Fare clic su **Salva** nella barra degli strumenti in alto.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Configurare l'app per la logica per l'uso di un'operazione di analisi del modulo di Riconoscimento modulo

In questa sezione si aggiunge l'operazione di **analisi del modulo** al flusso di lavoro. Questa operazione usa il modello di cui è già stato eseguito il training per analizzare una nuova fattura fornita all'app per la logica.

1. Selezionare **Nuovo passaggio** e in **Scegliere un'azione** cercare **Riconoscimento modulo**. Nei risultati visualizzati selezionare **Riconoscimento modulo** e quindi nelle azioni disponibili per Riconoscimento modulo selezionare **Analyze Form** (Analisi del modulo).

    > [!div class="mx-imgBorder"]
    > ![Analizzare un modello di Riconoscimento modulo](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Nella finestra di dialogo **Analyze Form** (Analisi del modulo) eseguire le operazioni seguenti:

    1. Fare clic sulla casella di testo **ID modello** e nella scheda **Contenuto dinamico** della finestra di dialogo visualizzata selezionare **modelId**. Questa operazione consente di fornire all'applicazione di flusso l'ID del modello di cui è stato eseguito il training nell'ultima sezione.

        > [!div class="mx-imgBorder"]
        > ![Usare ModelID per Riconoscimento modulo](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Fare clic sulla casella di testo **Documento** e nella scheda **Contenuto dinamico** della finestra di dialogo visualizzata selezionare **Attachments Content** (Contenuto allegati). Questa operazione consente di configurare il flusso per l'uso del file di fattura di esempio allegato al messaggio di posta elettronica inviato per attivare il flusso di lavoro.

        > [!div class="mx-imgBorder"]
        > ![Usare l'allegato di posta elettronica per analizzare le fatture](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Fare clic su **Salva** nella barra degli strumenti in alto.

### <a name="extract-the-table-information-from-the-invoice"></a>Estrarre le informazioni della tabella dalla fattura

In questa sezione viene configurata l'app per la logica per estrarre le informazioni dalla tabella all'interno delle fatture.

1. Selezionare **Aggiungi un'azione** e in **Scegliere un'azione** cercare **Compose** e quindi nelle azioni disponibili selezionare di nuovo **Componi**.
    ![Estrarre le informazioni della tabella dalla fattura](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Nella finestra di dialogo **Componi** fare clic sulla casella di testo **Input** e nella finestra di dialogo visualizzata selezionare **Tabelle**.

    > [!div class="mx-imgBorder"]
    > ![Estrarre le informazioni della tabella dalla fattura](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Fare clic su **Save**.

## <a name="test-your-logic-app"></a>Testare l'app per la logica

Per testare l'app per la logica, usare le fatture di esempio nella cartella **/Test** del set di dati di esempio scaricato da [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Eseguire la procedura seguente:

1. Nella finestra di progettazione di App per la logica di Azure per l'app selezionare **Esegui** nella barra degli strumenti in alto. Il flusso di lavoro è ora attivo e attende la ricezione di un messaggio di posta elettronica con la fattura allegata.
1. Inviare un messaggio di posta elettronica con una fattura di esempio allegata all'indirizzo di posta elettronica fornito durante la creazione dell'app per la logica. Verificare che il messaggio di posta elettronica venga recapitato alla cartella specificata durante la configurazione dell'app per la logica.
1. Non appena il messaggio viene recapitato alla cartella, la finestra di progettazione di App per la logica mostra una schermata con lo stato di avanzamento di ogni fase. Nella schermata seguente si noterà che è stato ricevuto un messaggio di posta elettronica con allegato e che il flusso di lavoro è in corso.

    > [!div class="mx-imgBorder"]
    > ![Avviare il flusso di lavoro inviando un messaggio di posta elettronica](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Al termine dell'esecuzione di tutte le fasi del flusso di lavoro, la finestra di progettazione di App per la logica mostra una casella di controllo verde per ogni fase. Nella finestra di progettazione selezionare **For each 2** (Per ogni 2) e quindi selezionare **Componi**.

    > [!div class="mx-imgBorder"]
    > ![Flusso di lavoro completato](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Copiare l'output della casella **OUTPUT** e incollarlo in qualsiasi editor di testo.

1. Confrontare l'output JSON con la fattura di esempio inviata come allegato nel messaggio di posta elettronica. Verificare che i dati JSON corrispondano ai dati nella tabella all'interno della fattura.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    L'esercitazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è configurato un flusso di lavoro di App per la logica di Azure per l'uso di Riconoscimento modulo per eseguire il training di un modello ed estrarre il contenuto di una fattura. Successivamente, verrà illustrato come creare un set di dati di training in modo da poter creare uno scenario simile con moduli personalizzati.

> [!div class="nextstepaction"]
> [Creare un set di dati di training](build-training-data-set.md)