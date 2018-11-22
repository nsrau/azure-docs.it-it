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
ms.openlocfilehash: ec7b956f080837b297bac56e6237ac0672601ce7
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344485"
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

> [!WARNING]
> Usare i certificati autofirmati solo per lo sviluppo. Non usarli nell'ambiente di produzione. I certificati autofirmati possono causare problemi nelle applicazioni client. Per altre informazioni, vedere la documentazione relativa alle librerie di rete usate nell'applicazione client.

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

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare un modello di ML distribuito come un servizio Web](how-to-consume-web-service.md).