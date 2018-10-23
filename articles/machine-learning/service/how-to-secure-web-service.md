---
title: Proteggere i servizi Web di Azure Machine Learning con SSL
description: Informazioni su come proteggere un servizio Web distribuito con il servizio di Azure Machine Learning. È possibile limitare l'accesso ai servizi Web e proteggere i dati inviati dai client usando Secure Sockets Layer (SSL) e l'autenticazione basata su chiave.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801013"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Proteggere i servizi Web di Azure Machine Learning con SSL

L'articolo contiene informazioni su come proteggere un servizio Web distribuito con il servizio di Azure Machine Learning. È possibile limitare l'accesso ai servizi Web e proteggere i dati inviati dai client usando Secure Sockets Layer (SSL) e l'autenticazione basata su chiave.

> [!Warning]
> Se non si abilita SSL, qualsiasi utente Internet sarà in grado di effettuare chiamate al servizio Web.

SSL esegue la crittografia dei dati scambiati tra il client e il servizio Web. Viene inoltre usato dal client per verificare l'identità del server. L'autenticazione è abilitata solo per i servizi che forniscono un certificato SSL e una chiave.  Se si abilita SSL è necessaria una chiave di autenticazione quando si accede al servizio Web.

Se si distribuisce un servizio Web con SSL abilitato o si abilita SSL per il servizio Web distribuito esistente, i passaggi sono gli stessi:

1. Immettere un nome di dominio.

2. Ottenere un certificato SSL.

3. Distribuire o aggiornare il servizio Web con l'impostazione SSL abilitata.

4. Aggiornare il DNS in modo che punti al servizio Web.

Si riscontrano lievi differenze per la protezione dei servizi Web tra [destinazioni di distribuzione](how-to-deploy-and-where.md). 

## <a name="get-a-domain-name"></a>Immettere un nome di dominio

Se non è già disponibile un nome di dominio, è possibile acquistarne uno da un __registrar__. La procedura e i costi variano a seconda del registrar. Il registrar fornisce anche gli strumenti per gestire il nome di dominio. Questi strumenti vengono usati per eseguire il mapping di un nome di dominio completo (ad esempio www.contoso.com) all'indirizzo IP che ospita il servizio Web.

## <a name="get-an-ssl-certificate"></a>Ottenere un certificato SSL

Esistono diversi modi per ottenere un certificato SSL. Il più comune consiste nell'acquistarne uno da un'__autorità di certificazione__ (CA). Indipendentemente da dove si ottiene il certificato, sono necessari i file seguenti:

* Un __certificato__. Il certificato deve contenere la catena di certificati completa e avere la codifica PEM.
* Una __chiave__. La chiave deve avere la codifica PEM.

Quando si richiede un certificato, è necessario fornire il nome di dominio completo (FQDN) dell'indirizzo che si intende usare per il servizio Web. ad esempio www.contoso.com. L'indirizzo indicato nel certificato e l'indirizzo usato dai client vengono confrontati quando viene convalidata l'identità del servizio Web. Se gli indirizzi non corrispondono, i client ricevono un errore. 

> [!TIP]
> Se l'autorità di certificazione non può fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità come [OpenSSL](https://www.openssl.org/) per modificare il formato.

> [!IMPORTANT]
> Usare i certificati autofirmati solo per lo sviluppo. Non usarli nell'ambiente di produzione. Se si usa un certificato autofirmato, vedere la sezione [Utilizzo di servizi Web con certificati autofirmati](#self-signed) per istruzioni specifiche.


## <a name="enable-ssl-and-deploy"></a>Abilitare SSL e distribuire

Per distribuire (o ridistribuire) il servizio con SSL abilitato, impostare il parametro `ssl_enabled` su `True`, dove applicabile. Impostare il parametro `ssl_certificate` sul valore del file di __certificato__ e `ssl_key` sul valore del file di __chiave__. 

+ **Distribuire nel servizio Kubernetes di Azure (AKS)**
  
  Durante il provisioning del cluster AKS, fornire i valori per i parametri associate a SSL come illustrato nel frammento di codice:

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Distribuire in Istanze di contenitore di Azure (ACI)**
 
  Durante la distribuzione a ACI, specificare i valori per i parametri associate a SSL come illustrato nel frammento di codice:

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Distribuire in Field-programmable Gate Arrays (FPGA)**

  La risposta dell'operazione `create_service` contiene l'indirizzo IP del servizio. L'indirizzo IP viene usato durante il mapping del nome DNS all'indirizzo IP del servizio. La risposta contiene anche una __chiave primaria__ e una __chiave secondaria__ che consentono di utilizzare il servizio. Fornire i valori per i parametri associate a SSL come illustrato nel frammento di codice:

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

## <a name="update-your-dns"></a>Aggiorna DNS

Successivamente, è necessario aggiornare il DNS in modo che punti al servizio Web.

+ **Per ACI e FPGA**:  

  Usare gli strumenti forniti dal registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.  

  A seconda del registrar e della durata (TTL) configurata per il nome di dominio, possono essere necessari da alcuni minuti a diverse ore prima che i client possano risolvere il nome di dominio.

+ **Per AKS**: 

  Aggiornare il DNS nella scheda "Configurazione" di "Indirizzo IP pubblico" del cluster AKS come illustrato nell'immagine. È possibile trovare l'indirizzo IP pubblico come uno dei tipi di risorse create nel gruppo di risorse che contiene i nodi agente AKS e altre risorse di rete.

  ![Servizio Machine Learning: protezione dei servizi Web con SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>Utilizzo di servizi autenticati

### <a name="how-to-consume"></a>Modalità di utilizzo 
+ **Per ACI e AKS**: 

  Per i servizi Web ACI e AKS, questi articoli contengono informazioni su come utilizzare i servizi Web:
  + [Come distribuire in ACI](how-to-deploy-to-aci.md)

  + [Come distribuire in AKS](how-to-deploy-to-aks.md)

+ **Per FPGA**:  

  Gli esempi seguenti illustrano come utilizzare un servizio FPGA autenticato in Python e C#.
  Sostituire `authkey` con la chiave primaria o secondaria restituita durante la distribuzione del servizio.

  Esempio relativo a Python:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  Esempio in C#:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Impostare l'intestazione dell'autorizzazione
Gli altri client gRPC possono autenticare le richieste impostando un'intestazione dell'autorizzazione. L'approccio generale consiste nel creare un oggetto `ChannelCredentials` che combina `SslCredentials` con `CallCredentials`. L'oggetto viene aggiunto all'intestazione dell'autorizzazione della richiesta. Per altre informazioni sull'implementazione del supporto per intestazioni specifiche, vedere [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

Gli esempi seguenti illustrano come impostare l'intestazione in C# e Go:

+ Usare C# per impostare l'intestazione:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Usare Go per impostare l'intestazione:
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Utilizzo di servizi con certificati autofirmati

Esistono due modi per consentire ai client di autenticarsi presso un server protetto con un certificato autofirmato:

* Nel sistema client impostare la variabile di ambiente `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` in modo che punti al file del certificato.

* Quando si costruisce un oggetto `SslCredentials`, passare il contenuto del file del certificato al costruttore.

Con entrambi i metodi, gRPC userà il certificato come certificato radice.

> [!IMPORTANT]
> gRPC non accetta certificati non attendibili. Se si usa un certificato non attendibile, si verificherà un errore con un codice di stato `Unavailable`. I dettagli dell'errore contengono `Connection Failed`.
