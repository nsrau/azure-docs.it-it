---
title: Servizi Web sicuri con TLS
titleSuffix: Azure Machine Learning
description: Informazioni su come abilitare HTTPS per proteggere un servizio Web distribuito tramite Azure Machine Learning.Learn how to enable HTTPS in order to secure a web service that's deployed through Azure Machine Learning. Azure Machine Learning usa TLS versione 1.2 per proteggere i modelli distribuiti come servizi Web.Azure Machine Learning uses TLS version 1.2 to secure models deployed as web services.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287340"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Usare TLS per proteggere un servizio Web tramite Azure Machine LearningUse TLS to secure a web service through Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come proteggere un servizio Web distribuito tramite Azure Machine Learning.This article shows you how to secure a web service that's deployed through Azure Machine Learning.

Utilizzare [HTTPS](https://en.wikipedia.org/wiki/HTTPS) per limitare l'accesso ai servizi Web e proteggere i dati inviati dai client. HTTPS consente di proteggere le comunicazioni tra un client e un servizio Web crittografando le comunicazioni tra i due. La crittografia utilizza [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS è a volte ancora indicato come *Secure Sockets Layer* (SSL), che era il predecessore di TLS.

> [!TIP]
> Azure Machine Learning SDK usa il termine "SSL" per le proprietà correlate alle comunicazioni protette. Ciò non significa che il servizio Web non utilizza *TLS*. SSL è solo un termine più comunemente riconosciuto.
>
> In particolare, i servizi Web distribuiti tramite Azure Machine Learning supportano solo TLS versione 1.2.Specifically, web services deployed through Azure Machine Learning only support TLS version 1.2.

TLS e SSL si basano entrambi su *certificati digitali,* che consentono la crittografia e la verifica dell'identità. Per ulteriori informazioni sul funzionamento dei certificati digitali, vedere l'argomento Wikipedia [Infrastruttura a chiave pubblica](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se non si utilizza HTTPS per il servizio Web, i dati inviati da e verso il servizio potrebbero essere visibili agli altri utenti su Internet.
>
> HTTPS consente inoltre al client di verificare l'autenticità del server a cui si connette. Questa funzionalità protegge i client dagli attacchi [man-in-the-middle.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Questo è il processo generale per proteggere un servizio Web:This is the general process to secure a web service:

1. Immettere un nome di dominio.

2. Ottenere un certificato digitale.

3. Distribuire o aggiornare il servizio Web con TLS abilitato.

4. Aggiornare il DNS in modo che punti al servizio Web.

> [!IMPORTANT]
> Se si esegue la distribuzione al servizio Azure Kubernetes (AKS), è possibile acquistare il proprio certificato o usare un certificato fornito da Microsoft.If you're deploying to Azure Kubernetes Service (AKS), you can purchase your own certificate or use a certificate that's provided by Microsoft. Se si utilizza un certificato di Microsoft, non è necessario ottenere un nome di dominio o un certificato TLS/SSL. Per altre informazioni, vedere la sezione [Abilitare TLS e distribuire](#enable) questo articolo.

Esistono lievi differenze quando si protegge s tra [destinazioni](how-to-deploy-and-where.md)di distribuzione .

## <a name="get-a-domain-name"></a>Immettere un nome di dominio

Se non si possiede già un nome di dominio, acquistarne uno da un registrar di nomi di *dominio*. Il processo e il prezzo differiscono tra i registrar. Il registrar fornisce strumenti per gestire il nome di dominio. Questi strumenti consentono di eseguire il mapping di un nome\.di dominio completo (FQDN) (ad esempio www contoso.com) all'indirizzo IP che ospita il servizio Web.

## <a name="get-a-tlsssl-certificate"></a>Ottenere un certificato TLS/SSLGet a TLS/SSL certificate

Esistono molti modi per ottenere un certificato TLS/SSL (certificato digitale). Il più comune è quello di acquistare uno da *un'autorità di certificazione* (CA). Indipendentemente da dove si ottiene il certificato, sono necessari i seguenti file:

* Un **certificato**. Il certificato deve contenere l'intera catena di certificati e deve essere "codificato PEM".
* Una **chiave**. Anche la chiave deve essere con codifica PEM.

Quando si richiede un certificato, è necessario fornire il nome di dominio completo dell'indirizzo che si intende utilizzare per il servizio Web, ad esempio www\.contoso.com. L'indirizzo inserito nel certificato e l'indirizzo utilizzati dai client vengono confrontati per verificare l'identità del servizio Web. Se tali indirizzi non corrispondono, il client riceve un messaggio di errore.

> [!TIP]
> Se l'autorità di certificazione non è in grado di fornire il certificato e la chiave come file con codifica PEM, è possibile utilizzare un'utilità come [OpenSSL](https://www.openssl.org/) per modificare il formato.

> [!WARNING]
> Utilizzare certificati *autofirmati* solo per lo sviluppo. Non utilizzarli negli ambienti di produzione. I certificati autofirmati possono causare problemi nelle applicazioni client. Per altre informazioni, vedere la documentazione relativa alle librerie di rete utilizzate dall'applicazione client.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Abilitare TLS e distribuire

Per distribuire (o ridistribuire) il servizio con TLS abilitato, impostare il parametro *ssl_enabled* su "True" ovunque sia applicabile. Impostare il parametro *ssl_certificate* sul valore del file del *certificato.* Impostare il *ssl_key* sul valore del file di *chiave.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Distribuzione su AKS e FPGA (Field-programmable gate array)

  > [!NOTE]
  > Le informazioni contenute in questa sezione si applicano anche quando si distribuisce un servizio Web protetto per la finestra di progettazione. Se non si ha familiarità con l'uso di Python SDK, vedere [Che cos'è Azure Machine Learning SDK per Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Quando si esegue la distribuzione in AKS, è possibile creare un nuovo cluster AKS o collegarne uno esistente. Per altre informazioni sulla creazione o il collegamento di un cluster, vedere Distribuire un modello in un cluster di servizi Azure Kubernetes.For more information on creating or attaching a cluster, see [Deploy a model to an Azure Kubernetes Service cluster.](how-to-deploy-azure-kubernetes-service.md)
  
-  Se si crea un nuovo cluster, si utilizza **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Se si collega un cluster esistente, si utilizza **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Entrambi restituiscono un oggetto di configurazione che dispone di **un metodo enable_ssl.**

Il **metodo enable_ssl** può usare un certificato fornito da Microsoft o un certificato acquistato dall'utente.

  * Quando si utilizza un certificato di Microsoft, è necessario utilizzare il parametro *leaf_domain_label.* Questo parametro genera il nome DNS per il servizio. Ad esempio, un valore "contoso" crea un\<nome di dominio "contoso sei caratteri casuali>. \<azureregion>.cloudapp.azure.com", \<dove azureregion> è l'area che contiene il servizio. Facoltativamente, è possibile utilizzare il parametro *overwrite_existing_domain* per sovrascrivere i *leaf_domain_label*esistenti.

    Per distribuire (o ridistribuire) il servizio con TLS abilitato, impostare il parametro *ssl_enabled* su "True" ovunque sia applicabile. Impostare il parametro *ssl_certificate* sul valore del file del *certificato.* Impostare il *ssl_key* sul valore del file di *chiave.*

    > [!IMPORTANT]
    > Quando si utilizza un certificato di Microsoft, non è necessario acquistare il proprio certificato o nome di dominio.

    Nell'esempio seguente viene illustrato come creare una configurazione che abilita un certificato TLS/SSL da Microsoft:The following example demonstrates how to create a configuration that enables an TLS/SSL certificate from Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Quando si utilizza *un certificato acquistato*, si utilizzano i parametri *ssl_cert_pem_file*, *ssl_key_pem_file*e *ssl_cname.* Nell'esempio seguente viene illustrato come utilizzare i file *con estensione pem* per creare una configurazione che utilizza un certificato TLS/SSL acquistato:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Per ulteriori informazioni su *enable_ssl*, consultate [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuire in istanze del contenitore di AzureDeploy on Azure Container Instances

Quando si distribuisce in istanze del contenitore di Azure, si forniscono valori per i parametri correlati a TLS, come illustrato nel frammento di codice seguente:When you deploy to Azure Container Instances, you provide values for TLS-related parameters, as the following code snippet shows:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Per ulteriori informazioni, consultate [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aggiorna DNS

Successivamente, è necessario aggiornare il DNS in modo che punti al servizio Web.

+ **Per le istanze del contenitore:For Container Instances:**

  Utilizzare gli strumenti del registrar del nome di dominio per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.

  Può verificarsi un ritardo di minuti o ore prima che i client possano risolvere il nome di dominio, a seconda del registrar e del "Time to Live" (TTL) configurato per il nome di dominio.

+ **Per AKS:**

  > [!WARNING]
  > Se è stato utilizzato *leaf_domain_label* per creare il servizio utilizzando un certificato di Microsoft, non aggiornare manualmente il valore DNS per il cluster. Il valore deve essere impostato automaticamente.

  Aggiornare il DNS dell'indirizzo IP pubblico del cluster AKS nella scheda **Configurazione** in **Impostazioni** nel riquadro sinistro. (Vedere l'immagine seguente.) L'indirizzo IP pubblico è un tipo di risorsa creato nel gruppo di risorse che contiene i nodi dell'agente AKS e altre risorse di rete.

  [![Azure Machine Learning: Securing web services with TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Aggiornare il certificato TLS/SSL

I certificati TLS/SSL scadono e devono essere rinnovati. In genere questo accade ogni anno. Usare le informazioni nelle sezioni seguenti per aggiornare e rinnovare il certificato per i modelli distribuiti nel servizio Azure Kubernetes:Use the information in the following sections to update and renew your certificate for models deployed to Azure Kubernetes Service:

### <a name="update-a-microsoft-generated-certificate"></a>Aggiornare un certificato generato da MicrosoftUpdate a Microsoft generated certificate

Se il certificato è stato originariamente generato da Microsoft (quando si utilizza il *leaf_domain_label* per creare il servizio), utilizzare uno degli esempi seguenti per aggiornare il certificato:

**Utilizzare l'SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Usare l'interfaccia della riga di comando**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Per altre informazioni, vedere i documenti di riferimento seguenti:For more information, see the following reference docs:

* [Configurazione di Ssl](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration (Configurazione indisponibilità)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aggiorna certificato personalizzato

Se il certificato è stato originariamente generato da un'autorità di certificazione, attenersi alla seguente procedura:

1. Utilizzare la documentazione fornita dall'autorità di certificazione per rinnovare il certificato. Questo processo crea nuovi file di certificato.

1. Utilizzare l'SDK o l'interfaccia della riga di comando per aggiornare il servizio con il nuovo certificato:

    **Utilizzare l'SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Usare l'interfaccia della riga di comando**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Per altre informazioni, vedere i documenti di riferimento seguenti:For more information, see the following reference docs:

* [Configurazione di Ssl](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration (Configurazione indisponibilità)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Disabilita TLS

Per disabilitare TLS per un modello distribuito nel `SslConfiguration` servizio `status="Disabled"`Azure Kubernetes, creare un oggetto with , quindi eseguire un aggiornamento:To disable TLS for a model deployed to Azure Kubernetes Service, create an with , then perform an update:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Passaggi successivi
Viene illustrato come:
+ [Consume a machine learning model deployed as a web service](how-to-consume-web-service.md) (Come usare un modello di Machine Learning distribuito come servizio Web)
+ [Esegui in modo sicuro esperimenti e inferenza all'interno di una rete virtuale di AzureSecurely run experiments and inference inside an Azure virtual network](how-to-enable-virtual-network.md)
