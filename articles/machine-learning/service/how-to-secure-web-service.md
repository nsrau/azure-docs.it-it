---
title: Proteggere i servizi web con SSL
titleSuffix: Azure Machine Learning service
description: Informazioni su come proteggere un servizio web che viene distribuito tramite il servizio di Azure Machine Learning per l'abilitazione di HTTPS. HTTPS protegge i dati dai client tramite transport layer security (TLS), un sostituto di secure Sockets Layer (SSL). Inoltre, i client usano HTTPS per verificare l'identità del servizio web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: c176458cfc404a9d55d7fb71a36ea63110b3a6d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657959"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Usa SSL per proteggere un servizio web tramite Azure Machine Learning

Questo articolo illustra come proteggere un servizio web che viene distribuito tramite il servizio di Azure Machine Learning.

Si utilizza [HTTPS](https://en.wikipedia.org/wiki/HTTPS) per limitare l'accesso ai servizi web e proteggere i dati che consentono di inviare ai client. HTTPS aiuta a proteggere le comunicazioni tra un client e un servizio web grazie alla crittografia delle comunicazioni tra i due. Usa crittografia [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS è ancora dette *SSL (Secure Sockets Layer)* (SSL), che era il predecessore di TLS.

> [!TIP]
> il SDK di Azure Machine Learning Usa il termine "SSL" per le proprietà associate a comunicazioni protette. Ciò non significa che non usa il servizio web *TLS*. SSL è solo un termine più comunemente riconosciuto.

Entrambi si basano su SSL e TLS *certificati digitali*, che contribuiscono con la verifica di crittografia e identità. Per altre informazioni sulle attività di certificati digitali, vedere l'argomento di Wikipedia [infrastruttura a chiave pubblica](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se non si usa HTTPS per il servizio web, i dati inviati da e verso il servizio potrebbero essere visibili ad altri utenti su internet.
>
> HTTPS consente anche al client verificare l'autenticità del server che si sta connettendo a. Questa funzionalità consente di proteggere i client contro [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacchi.

Questo è il processo generale per proteggere un servizio web:

1. Immettere un nome di dominio.

2. Ottenere un certificato digitale.

3. Distribuire o aggiornare il servizio web con SSL abilitato.

4. Aggiornare il DNS in modo che punti al servizio Web.

> [!IMPORTANT]
> Se si esegue la distribuzione di Azure Kubernetes Service (AKS), è possibile acquistare un certificato personalizzato o usare un certificato che viene fornito da Microsoft. Se si usa un certificato rilasciato da Microsoft, non devi ottenere un nome di dominio o un certificato SSL. Per altre informazioni, vedere la [abilita SSL e si distribuisce](#enable) sezione di questo articolo.

Esistono piccole differenze, la protezione di servizi web attraverso [destinazioni di distribuzione](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Immettere un nome di dominio

Se non si è già proprietari di un nome di dominio, acquistarne uno da un *registrar di nomi di dominio*. Il processo e il prezzo differiscono tra Registrar. Il registrar offre strumenti per gestire il nome di dominio. Utilizzare questi strumenti per eseguire il mapping di un nome di dominio completo (FQDN) (ad esempio www\.contoso.com) all'indirizzo IP che ospita il servizio web.

## <a name="get-an-ssl-certificate"></a>Ottenere un certificato SSL

Esistono molti modi per ottenere un certificato SSL (digitale certificato). La più comune consiste nell'acquisto di uno da un *autorità di certificazione* (CA). Indipendentemente dalla provenienza del certificato, sono necessari i file seguenti:

* Un **certificato**. Il certificato deve contenere la catena di certificati e deve essere "Con codifica PEM."
* Una **chiave**. La chiave deve anche essere con codifica PEM.

Quando si richiede un certificato, è necessario specificare il FQDN dell'indirizzo che si intende usare per il servizio web (ad esempio, www\.contoso.com). L'indirizzo a cui è indicato nel certificato e l'indirizzo utilizzato dai client vengono confrontate per verificare l'identità del servizio web. Se tali indirizzi non corrispondono, il client ottiene un messaggio di errore.

> [!TIP]
> Se l'autorità di certificazione non è possibile fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità, ad esempio [OpenSSL](https://www.openssl.org/) per modificare il formato.

> [!WARNING]
> Uso *autofirmati* certificati solo per lo sviluppo. Non usarle negli ambienti di produzione. I certificati autofirmati possono causare problemi nelle applicazioni client. Per altre informazioni, vedere la documentazione per le librerie di rete utilizzati dall'applicazione client.

## <a id="enable"></a> Abilitare SSL e distribuire

Per distribuire o ridistribuire il servizio con SSL abilitato, impostare il *ssl_enabled* parametro su "True" ogni volta che è applicabile. Impostare il *ssl_certificate* parametro per il valore della *certificato* file. Impostare il *ssl_key* al valore della *chiave* file.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Distribuire nel servizio contenitore di AZURE e campo-programmable gate array FPGA)

  > [!NOTE]
  > Le informazioni contenute in questa sezione si applicano anche quando si distribuisce un servizio web protetto per l'interfaccia visiva. Se non si ha familiarità con Python SDK, vedere [che cos'è Azure Machine Learning SDK per Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Quando si distribuisce in AKS, è possibile creare un nuovo cluster AKS o collegare uno esistente.
  
-  Se si crea un nuovo cluster, usare  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Se si collega un cluster esistente, usare  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Entrambe restituiscono un oggetto di configurazione con un **enable_ssl** (metodo).

Il **enable_ssl** metodo può usare un certificato che viene fornito da Microsoft o un certificato acquistato.

  * Quando si usa un certificato rilasciato da Microsoft, è necessario usare il *leaf_domain_label* parametro. Questo parametro genera il nome DNS per il servizio. Ad esempio, un valore di "myservice" Crea un nome di dominio di "myservice\<sei caratteri casuali >.\< areaazure >. cloudapp.azure.com ", dove \<areaazure > è l'area che contiene il servizio. Facoltativamente, è possibile usare la *overwrite_existing_domain* parametro per sovrascrivere esistenti *leaf_domain_label*.

    Per distribuire o ridistribuire il servizio con SSL abilitato, impostare il *ssl_enabled* parametro su "True" ogni volta che è applicabile. Impostare il *ssl_certificate* parametro per il valore della *certificato* file. Impostare il *ssl_key* al valore della *chiave* file.

    > [!IMPORTANT]
    > Quando si usa un certificato rilasciato da Microsoft, non devi acquistare il proprio certificato o nome di dominio.

    Nell'esempio seguente viene illustrato come creare una configurazione che consente a un certificato SSL da parte di Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Quando si usa *un certificato acquistato*, si utilizza il *ssl_cert_pem_file*, *ssl_key_pem_file*, e *ssl_cname* parametri. Nell'esempio seguente viene illustrato come utilizzare *PEM* file per creare una configurazione che utilizza un certificato SSL acquistato:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Per altre informazioni sulle *enable_ssl*, vedere [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuire in istanze di contenitore di Azure

Quando si distribuisce in istanze di contenitore di Azure, fornisce i valori per parametri associate a SSL, come illustrato nel frammento di codice seguente:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Per altre informazioni, vedere [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Aggiorna DNS

Successivamente, è necessario aggiornare il DNS in modo che punti al servizio Web.

+ **Per le istanze di contenitore:**

  Usare gli strumenti dal registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.

  Si può verificare un ritardo di pochi minuti oppure ore prima che i client possono risolvere il nome di dominio, a seconda le registrar e "time to live" (TTL) che è configurato per il nome di dominio.

+ **Per servizio contenitore di AZURE:**

  > [!WARNING]
  > Se è stata usata *leaf_domain_label* per creare il servizio usando un certificato da Microsoft, non aggiorna manualmente il valore DNS per il cluster. Il valore deve essere impostato automaticamente.

  Aggiornare il DNS sul **configurazione** scheda dell'indirizzo IP pubblico del cluster servizio contenitore di AZURE. (Vedere la figura seguente). L'indirizzo IP pubblico è un tipo di risorsa che viene creato nel gruppo di risorse che contiene i nodi agente AKS e altre risorse di rete.

  ![Servizio Azure Machine Learning: Protezione del servizio Web con SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Passaggi successivi
È possibile passare agli argomenti seguenti:
+ [Consume a machine learning model deployed as a web service](how-to-consume-web-service.md) (Come usare un modello di Machine Learning distribuito come servizio Web)
+ [In modo sicuro eseguire esperimenti e inferenza all'interno di una rete virtuale di Azure](how-to-enable-virtual-network.md)
