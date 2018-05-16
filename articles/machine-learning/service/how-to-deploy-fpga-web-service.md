---
title: Come distribuire un modello come servizio Web in un FPGA con Azure Machine Learning
description: Informazioni su come distribuire un servizio Web con un modello in esecuzione in un FPGA con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Distribuire un modello come servizio Web in un FPGA con Azure Machine Learning

Questo documento illustra come configurare l'ambiente delle workstation e distribuire un modello come servizio Web in circuiti [FPGA (field programmable gate array)](concept-accelerate-with-fpgas.md). Il servizio Web usa Project Brainwave per eseguire il modello in FPGA.

L'uso di FPGA offre inferenze a latenza estremamente bassa, anche con una singola dimensione batch.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Creare un account di Gestione modelli di Azure Machine Learning

1. Passare alla pagina di creazione dell'account di Gestione modelli nel [portale di Azure](https://aka.ms/aml-create-mma).

2. Nel portale creare un account di Gestione modelli nell'area **Stati Uniti orientali 2**.

   ![Immagine della schermata Crea account di Gestione modelli](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Assegnare un nome all'account di Gestione modelli, quindi scegliere una sottoscrizione e un gruppo di risorse.

   >[!IMPORTANT]
   >Per la posizione è NECESSARIO scegliere l'area **Stati Uniti orientali 2**.  Non sono attualmente disponibili altre aree.

4. Scegliere un piano tariffario. S1 è sufficiente, ma vanno bene anche S2 e S3.  Il piano DevTest non è supportato.  

5. Fare clic su **Seleziona** per confermare il piano tariffario.

6. Fare clic su **Crea** in Gestione modelli di ML a sinistra.

## <a name="get-model-management-account-information"></a>Ottenere informazioni dell'account di Gestione modelli

Per ottenere informazioni sull'account di Gestione modelli, fare clic sull'__account di Gestione modelli__ nel portale di Azure.

Copiare i valori degli elementi seguenti:

+ Nome dell'account di Gestione modelli (nell'angolo in alto a sinistra)
+ Nome del gruppo di risorse
+ ID sottoscrizione
+ Posizione (usare "eastus2")

![Informazioni dell'account di Gestione modelli](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Configurare il computer

Per configurare la workstation per la distribuzione in FPGA, seguire questa procedura:

1. Scaricare e installare la versione più recente di [Git](https://git-scm.com/downloads).

2. Installare [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Per scaricare un ambiente Anaconda usare il comando seguente dal prompt di Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Per creare l'ambiente, aprire un **prompt di Anaconda** (non un prompt di Azure Machine Learning Workbench) ed eseguire questo comando:

    > [!IMPORTANT]
    > Il file `environment.yml` si trova nel repository Git clonato nel passaggio precedente. Modificare il percorso in modo da puntare al file nella workstation.

    ```
    conda env create -f environment.yml
    ```

5. Per attivare l'ambiente, usare il comando seguente:

    ```
    conda activate amlrealtimeai
    ```

6. Per avviare il server Jupyter Notebook, usare il comando seguente:

    ```
    jupyter notebook
    ```

    L'output di questo comando è simile al testo seguente:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Si otterrà un token diverso ogni volta che si esegue il comando.

    Se il browser non si apre automaticamente sul notebook di Jupyter, usare l'URL HTTP restituito dal comando precedente per aprire la pagina.

    ![Immagine della pagina Web di Jupyter Notebook](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Distribuire il modello

Da Jupyter Notebook, aprire il notebook `00_QuickStart.ipynb` dalla directory `notebooks/resnet50`. Seguire le istruzioni contenute nel notebook per:

* Definire il servizio
* Distribuire il modello
* Utilizzare il modello distribuito
* Eliminare i servizi distribuiti

> [!IMPORTANT]
> Per ottimizzare la latenza e la velocità effettiva, la workstation deve trovarsi nella stessa area di Azure in cui si trova l'endpoint.  Le API vengono attualmente create nell'area Stati Uniti orientali.

## <a name="ssltls-and-authentication"></a>SSL/TLS e autenticazione

Azure Machine Learning fornisce il supporto SSL e l'autenticazione basata su chiavi. In questo modo è possibile limitare l'accesso al servizio e proteggere i dati inviati dai client.

> [!NOTE]
> I passaggi di questa sezione sono applicabili solo ai modelli di accelerazione hardware di Azure Machine Learning. Per i servizi standard di Azure Machine Learning, vedere il documento [Abilitare SSL in un cluster di calcolo di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc).

> [!IMPORTANT]
> L'autenticazione è abilitata solo per i servizi che forniscono un certificato SSL e una chiave. 
>
> Se non si abilita SSL, qualsiasi utente in Internet sarà in grado di effettuare chiamate al servizio.
>
> Se si abilita SSL è necessaria una chiave di autenticazione quando si accede al servizio.

SSL crittografa i dati scambiati tra il client e il servizio. Viene inoltre usato dal client per verificare l'identità del server.

È possibile distribuire un servizio con SSL abilitato oppure aggiornare un servizio già distribuito per abilitare SSL. La procedura è la stessa:

1. Acquisire un nome di dominio.

2. Acquisire un certificato SSL.

3. Distribuire o aggiornare il servizio con SSL abilitato.

4. Aggiornare il DNS in modo che punti al servizio.

### <a name="acquire-a-domain-name"></a>Acquisire un nome di dominio

Se non è già disponibile un nome di dominio, è possibile acquistarne uno da un __registrar__. La procedura e i costi variano a seconda del registrar. Il registrar fornisce anche gli strumenti per gestire il nome di dominio. Questi strumenti vengono usati per eseguire il mapping di un nome di dominio completo (ad esempio www.contoso.com) all'indirizzo IP che ospita il servizio.

### <a name="acquire-an-ssl-certificate"></a>Acquisire un certificato SSL

Esistono diversi modi per ottenere un certificato SSL. Il più comune consiste nell'acquistarne uno da un'__autorità di certificazione__ (CA). Indipendentemente da dove si ottiene il certificato, sono necessari i file seguenti:

* Un __certificato__. Il certificato deve contenere la catena di certificati completa e avere la codifica PEM.
* Una __chiave__. La chiave deve avere la codifica PEM.

> [!TIP]
> Se l'autorità di certificazione non può fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità come [OpenSSL](https://www.openssl.org/) per modificare il formato.

> [!IMPORTANT]
> Usare i certificati autofirmati solo per lo sviluppo. Non usarli nell'ambiente di produzione.
>
> Se si usa un certificato autofirmato, vedere la sezione [Utilizzo di servizi con certificati autofirmati](#self-signed) per istruzioni specifiche.

> [!WARNING]
> Quando si richiede un certificato, è necessario fornire il nome di dominio completo (FQDN) dell'indirizzo che si intende usare per il servizio, ad esempio www.contoso.com. L'indirizzo indicato nel certificato e l'indirizzo usato dai client vengono confrontati quando viene convalidata l'identità del servizio.
>
> Se gli indirizzi non corrispondono, i client ricevono un errore. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Distribuire o aggiornare il servizio con SSL abilitato

Per distribuire il servizio con SSL abilitato, impostare il parametro `ssl_enabled` su `True`. Impostare il parametro `ssl_certificate` sul valore del file di __certificato__ e `ssl_key` sul valore del file di __chiave__. L'esempio seguente illustra la distribuzione di un servizio con SSL abilitato:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

La risposta dell'operazione `create_service` contiene l'indirizzo IP del servizio. L'indirizzo IP viene usato durante il mapping del nome DNS all'indirizzo IP del servizio.

La risposta contiene anche una __chiave primaria__ e una __chiave secondaria__ che consentono di utilizzare il servizio.

### <a name="update-your-dns-to-point-to-the-service"></a>Aggiornare il DNS in modo che punti al servizio

Usare gli strumenti forniti dal registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.

> [!NOTE]
> A seconda del registrar e della durata (TTL) configurata per il nome di dominio, possono essere necessari da alcuni minuti a diverse ore prima che i client possano risolvere il nome di dominio.

### <a name="consuming-authenticated-services"></a>Utilizzo di servizi autenticati

Gli esempi seguenti illustrano come utilizzare un servizio autenticato con Python e C#:

> [!NOTE]
> Sostituire `authkey` con la chiave primaria o secondaria restituita durante la creazione del servizio.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Gli altri client gRPC possono autenticare le richieste impostando un'intestazione dell'autorizzazione. L'approccio generale consiste nel creare un oggetto `ChannelCredentials` che combina `SslCredentials` con `CallCredentials`. L'oggetto viene aggiunto all'intestazione dell'autorizzazione della richiesta. Per altre informazioni sull'implementazione del supporto per intestazioni specifiche, vedere [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

Gli esempi seguenti illustrano come impostare l'intestazione in C# e Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Utilizzo di servizi con certificati autofirmati

Esistono due modi per consentire ai client di autenticarsi presso un server protetto con un certificato autofirmato:

* Nel sistema client impostare la variabile di ambiente `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` in modo che punti al file del certificato.

* Quando si costruisce un oggetto `SslCredentials`, passare il contenuto del file del certificato al costruttore.

Con entrambi i metodi, gRPC userà il certificato come certificato radice.

> [!IMPORTANT]
> gRPC non accetta certificati non attendibili. Se si usa un certificato non attendibile, si verificherà un errore con un codice di stato `Unavailable`. I dettagli dell'errore contengono `Connection Failed`.
