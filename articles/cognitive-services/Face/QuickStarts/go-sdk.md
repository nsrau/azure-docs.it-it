---
title: 'Avvio rapido: Libreria client di Viso per Go | Microsoft Docs'
description: Introduzione alla libreria client di Viso per Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961228"
---
# <a name="quickstart-face-client-library-for-go"></a>Avvio rapido: Libreria client di Viso per Go

Introduzione alla libreria client di Viso per Go. Seguire questa procedura per installare la libreria e provare gli esempi di attività di base. Il servizio Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Installare la libreria client del servizio Viso per Go per:

* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group)
* [Identificare un viso](#identify-a-face)
* [Creare uno snapshot per la migrazione dei dati](#take-a-snapshot-for-data-migration)

[Documentazione di riferimento](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [Download dell'SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Prerequisites

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/)
* Versione più recente di [Go](https://golang.org/dl/)

## <a name="set-up"></a>Configurare

### <a name="create-a-face-azure-resource"></a>Creare una risorsa di Azure Viso 

Per iniziare a usare il servizio Viso, creare una risorsa di Azure. Scegliere il tipo di risorsa più adatto alle proprie esigenze:

* Una [risorsa di valutazione](https://azure.microsoft.com/try/cognitive-services/#decision) (non richiede una sottoscrizione di Azure): 
    * è gratuita e valida sette giorni. Dopo aver eseguito l'iscrizione, una chiave di valutazione e un endpoint saranno disponibili sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Si tratta di un'ottima soluzione se si vuole provare il servizio Viso ma non si ha una sottoscrizione di Azure.
* Una [risorsa del servizio Viso](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):
    * disponibile tramite il portale di Azure fino a quando la risorsa non viene eliminata.
    * Usare il piano tariffario gratuito per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Una [risorsa multi-servizio](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * disponibile tramite il portale di Azure fino a quando la risorsa non viene eliminata.  
    * Usare la stessa chiave e lo stesso endpoint per le applicazioni in più servizi cognitivi.

### <a name="create-an-environment-variable"></a>Creare una variabile di ambiente

>[!NOTE]
> Gli endpoint per le risorse non di valutazione create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando la chiave e l'endpoint della risorsa creata, creare due variabili di ambiente per l'autenticazione:
* `FACE_SUBSCRIPTION_KEY`: la chiave della risorsa per l'autenticazione delle richieste.
* `FACE_ENDPOINT`: l'endpoint della risorsa per l'invio delle richieste API. L'aspetto sarà simile al seguente: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Usare le istruzioni per il sistema operativo in uso.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Dopo aver aggiunto la variabile di ambiente, riavviare la finestra della console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.
***

### <a name="create-a-go-project-directory"></a>Creare una directory di progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go, denominato `my-app`.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

L'area di lavoro conterrà tre cartelle:

* **src**: questa directory conterrà il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno disponibili in questa cartella.
* **pkg**: questa directory conterrà gli oggetti pacchetto Go compilati. Tutti questi i file hanno estensione `.a`.
* **bin**: questa directory conterrà i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Per altre informazioni sulla struttura di un'area di lavoro Go, vedere la [documentazione del linguaggio Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installare la libreria client per Go

Successivamente, installare la libreria client per Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

oppure, se si usa DEP, all'interno del repository eseguire:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Creare un'applicazione Go

Successivamente, creare un file nella directory **src** denominato `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Aprire `sample-app.go` nell'IDE o nell'editor di testo preferito. Aggiungere quindi il nome del pacchetto e importare le librerie seguenti:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Successivamente, si inizierà ad aggiungere il codice per eseguire diverse operazioni del servizio Viso.

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dell'SDK del servizio Viso per Go.

|Nome|Descrizione|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Questa classe rappresenta l'autorizzazione per l'utilizzo del servizio Viso ed è necessaria per tutte le funzionalità del servizio. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Questa classe gestisce le attività di rilevamento e riconoscimento di base che è possibile eseguire con i visi umani. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Questa classe rappresenta tutti i dati rilevati da un singolo viso in un'immagine. È possibile usarla per recuperare informazioni dettagliate sul viso.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Questa classe gestisce i costrutti **FaceList** archiviati nel cloud, che archiviano un set assortito di visi. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Questa classe gestisce i costrutti **Person** archiviati nel cloud, che archiviano un set di visi che appartengono a una singola persona.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Questa classe gestisce i costrutti **PersonGroup** archiviati nel cloud, che archiviano un set di oggetti **Person** assortiti. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Questa classe gestisce la funzionalità Snapshot, che può essere usata per salvare temporaneamente tutti i dati sui visi basati sul cloud ed eseguirne la migrazione in una nuova sottoscrizione di Azure. |

## <a name="code-examples"></a>Esempi di codice

Questi esempi di codice illustrano come completare le attività di base usando la libreria client del servizio Viso per Go:

* [Autenticare il client](#authenticate-the-client)
* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group)
* [Identificare un viso](#identify-a-face)
* [Creare uno snapshot per la migrazione dei dati](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE] 
> Questo argomento di avvio rapido presuppone che siano state [create variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Viso, denominate rispettivamente `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.

Creare una funzione **main** e aggiungervi il codice seguente per creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** con la chiave e usarlo con l'endpoint per creare un oggetto **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Questo codice crea anche un'istanza di un oggetto di contesto, che è necessario per la creazione di oggetti client. Definisce inoltre una posizione remota in cui si trovano alcune immagini di esempio di questo argomento di avvio rapido.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Aggiungere il codice seguente al metodo **main**. Questo codice definisce un'immagine di esempio remota e specifica le caratteristiche del viso da estrarre dall'immagine. Specifica anche quale modello di intelligenza artificiale usare per estrarre i dati dal viso o dai visi rilevati. Per informazioni su queste opzioni, vedere [Specificare un modello di riconoscimento volto](../Face-API-How-to-Topics/specify-recognition-model.md). Infine, il metodo **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** esegue l'operazione di rilevamento dei visi nell'immagine e salva i risultati nella memoria del programma.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Visualizzare i dati dei visi rilevati

Il blocco di codice successivo acquisisce il primo elemento della matrice di oggetti **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** e stampa gli attributi nella console. Se è stata usata un'immagine con più visi, è invece necessario scorrere la matrice.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Individuazione di visi simili

Il codice seguente cerca le corrispondenze di un singolo viso rilevato (origine) in un set di altri visi (destinazione). Quando trova una corrispondenza, visualizza l'ID del viso corrispondente nella console.

### <a name="detect-faces-for-comparison"></a>Rilevare visi per il confronto

Per prima cosa, salvare un riferimento al viso rilevato nella sezione [Rilevare visi in un'immagine](#detect-faces-in-an-image). Questo viso costituirà l'origine.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Immettere quindi il codice seguente per rilevare un set di visi in un'immagine diversa. Questi visi rappresenteranno la destinazione.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Trovare le corrispondenze

Il codice seguente usa il metodo **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** per trovare tutti i visi di destinazione che corrispondono al viso di origine.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Visualizzare le corrispondenze

Il codice seguente visualizza i dettagli delle corrispondenze nella console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Creare ed eseguire il training di un gruppo di persone

Per eseguire i passaggi di questo scenario, è necessario salvare le immagini seguenti nella directory radice del progetto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Questo gruppo di immagini contiene tre set di immagini con un singolo viso che corrispondono a tre persone diverse. Il codice definirà tre oggetti **PersonGroup Person** e li assocerà ai file di immagini che iniziano con `woman`, `man` e `child`.

### <a name="create-persongroup"></a>Creare l'oggetto PersonGroup

Dopo aver scaricato le immagini, aggiungere il codice seguente alla fine del metodo **main**. Questo codice autentica un oggetto **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** e quindi lo usa per definire un nuovo oggetto **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Creare oggetti PersonGroup Person

Il blocco di codice successivo autentica un oggetto **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** e lo usa per definire tre nuovi oggetti **PersonGroup Person**. Ognuno di questi oggetti rappresenta una singola persona nel set di immagini.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Assegnare i visi alle persone

Il codice seguente ordina le immagini in base al prefisso, rileva i visi e li assegna a ogni rispettivo oggetto **PersonGroup Person**, in base al nome del file di immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Eseguire il training di PersonGroup

Una volta assegnati i visi, eseguire il training di **PersonGroup** in modo che possa identificare le caratteristiche visive associate a ogni oggetto **Person**. Il codice seguente chiama il metodo **train** asincrono ed esegue il polling del risultato, visualizzando lo stato nella console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identificare un viso

Il codice seguente cerca di identificare l'identità di ogni persona in un'immagine con più visi. Confronta ogni viso rilevato con un **PersonGroup**, un database di oggetti **Person** diversi le cui caratteristiche del viso sono note.

> [!IMPORTANT]
> Prima di eseguire questo esempio occorre eseguire il codice in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Ottenere un'immagine di test

Il codice seguente cerca nella radice del progetto l'immagine _test-image-person-group.jpg_ e la carica nella memoria del programma. Questa immagine si trova nello stesso repository delle immagini usate in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Rilevare i visi di origine nell'immagine di test

Il blocco di codice successivo esegue un normale rilevamento dei visi nell'immagine di test per recuperare tutti visi e salvarli in una matrice.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identificare visi

Il metodo **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** confronta la matrice di visi rilevati con l'oggetto **PersonGroup** specificato, definito e sottoposto a training nella sezione precedente. Se riesce a stabilire una corrispondenza tra un viso rilevato e un oggetto **Person** nel gruppo, salva il risultato.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Il codice stampa quindi i risultati dettagliati delle corrispondenze nella console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verificare i visi

L'operazione di verifica si basa su un Face ID e su un altro Face ID o un oggetto **Person** per determinare se appartengono alla stessa persona.

Il codice seguente rileva i visi in due immagini di origine e quindi li verifica rispetto a un viso rilevato da un'immagine di destinazione.

### <a name="get-test-images"></a>Ottenere immagini di test

I blocchi di codice seguenti dichiarano variabili che punteranno alle immagini di destinazione e di origine per l'operazione di verifica.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Rilevare i visi per la verifica

Il codice seguente rileva i visi nelle immagini di origine e di destinazione e li salva nelle variabili.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Ottenere i risultati della verifica

Il codice seguente confronta ognuna delle immagini di origine con l'immagine di destinazione e stampa un messaggio che indica se appartengono alla stessa persona.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Creare uno snapshot per la migrazione dei dati

La funzionalità Snapshot consente di spostare i dati sui visi salvati, ad esempio un **PersonGroup** sottoposto a training, in una sottoscrizione diversa dell'API Viso di Servizi cognitivi di Azure. Si potrebbe usare questa funzionalità se, ad esempio, è stato creato un oggetto **PersonGroup** usando una sottoscrizione di valutazione gratuita e ora se ne vuole eseguire la migrazione a una sottoscrizione a pagamento. Per un'ampia panoramica della funzionalità Snapshot, vedere [Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa](../Face-API-How-to-Topics/how-to-migrate-face-data.md).

In questo esempio si eseguirà la migrazione dell'oggetto **PersonGroup** creato in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group). È possibile completare prima quella sezione o usare i propri costrutti di dati sui visi.

### <a name="set-up-target-subscription"></a>Configurare la sottoscrizione di destinazione

Prima di tutto, è necessario avere una seconda sottoscrizione di Azure con una risorsa Viso. A tale scopo, ripetere la procedura descritta nella sezione [Configurazione](#set-up). 

Quindi, creare le variabili seguenti quasi all'inizio del metodo **main**. Sarà anche necessario creare nuove variabili di ambiente per l'ID sottoscrizione dell'account Azure, oltre alla chiave, all'endpoint e all'ID sottoscrizione del nuovo account (di destinazione).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Quindi, inserire il valore di ID sottoscrizione in una matrice per i passaggi successivi.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Autenticare il client di destinazione

Più avanti nello script salvare l'oggetto client originale come client di origine, quindi autenticare un nuovo oggetto client per la sottoscrizione di destinazione. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Creare uno snapshot

Il passaggio successivo consiste nell'acquisire lo snapshot con **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** , quindi salvare i dati sui visi della sottoscrizione originale in una posizione temporanea sul cloud. Questo metodo restituisce un ID da usare per eseguire query sullo stato dell'operazione.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Successivamente, eseguire query sull'ID fino al completamento dell'operazione.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Applicare lo snapshot

Usare l'operazione **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** per scrivere i dati sui visi appena scaricati nella sottoscrizione di destinazione. Questo metodo restituisce anche un ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Anche in questo caso, eseguire query sull'ID fino al completamento dell'operazione.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Una volta completati questi passaggi, sarà possibile accedere ai costrutti di dati sui visi dalla nuova sottoscrizione di destinazione.

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione Go immettendo il comando `go run [arguments]` dalla directory dell'applicazione.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se è stato creato un oggetto **PersonGroup** in questo argomento di avvio rapido e si vuole eliminarlo, chiamare il metodo **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** . Se è stata eseguita la migrazione dei dati usando la funzionalità Snapshot in questa guida di avvio rapido, sarà anche necessario eliminare il **PersonGroup** salvato nella sottoscrizione di destinazione.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria di Viso per Go per eseguire attività di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Viso (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Che cos'è il servizio Viso?](../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
