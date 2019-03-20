---
title: Proteggere il servizio Web con SSL
titleSuffix: Azure Machine Learning service
description: Informazioni su come proteggere un servizio web distribuito con il servizio di Azure Machine Learning per l'abilitazione di HTTPS. HTTPS protegge i dati inviati dai client tramite transport layer security (TLS), un sostituto di secure Sockets Layer (SSL). Anche utilizzato dai client per verificare l'identità del servizio web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103894"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Utilizzare SSL per proteggere il servizio Web con il servizio Azure Machine Learning

L'articolo contiene informazioni su come proteggere un servizio Web distribuito con il servizio di Azure Machine Learning. È possibile limitare l'accesso ai servizi web e proteggere i dati inviati dai client che usano [Hypertext Transfer Protocol sicura (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

Viene utilizzato HTTPS per proteggere le comunicazioni tra un client e il servizio web mediante la crittografia delle comunicazioni tra i due. La crittografia viene gestita usando [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). In alcuni casi ciò viene comunque definito come SSL Secure Sockets Layer (), che era il predecessore di TLS.

> [!TIP]
> il SDK di Azure Machine Learning Usa il termine "SSL" per le proprietà relative all'abilitazione di proteggere le comunicazioni. Ciò non significa che TLS non viene utilizzato dal servizio web, solo che SSL è il termine più facilmente riconoscibile per molti lettori.

Entrambi si basano su SSL e TLS __certificati digitali__, che sono usati per eseguire la verifica di crittografia e identità. Per altre informazioni sulle attività di certificati digitali, vedere la pagina di Wikipedia sul [infrastruttura a chiave pubblica (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Se non si abilita e uso di HTTPS per il servizio web, i dati inviati da e verso il servizio potrebbero essere visibili ad altri utenti su internet.
>
> HTTPS consente anche al client verificare l'autenticità del server che si sta connettendo a. Ciò consente di proteggere i client contro [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacchi.

Il processo di protezione di un servizio web nuovo o esistente è come segue:

1. Immettere un nome di dominio.

2. Ottenere un certificato digitale.

3. Distribuire o aggiornare il servizio Web con l'impostazione SSL abilitata.

4. Aggiornare il DNS in modo che punti al servizio Web.

Si riscontrano lievi differenze per la protezione dei servizi Web tra [destinazioni di distribuzione](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Immettere un nome di dominio

Se non è già disponibile un nome di dominio, è possibile acquistarne uno da un __registrar__. La procedura e i costi variano a seconda del registrar. Il registrar fornisce anche gli strumenti per gestire il nome di dominio. Questi strumenti vengono usati per eseguire il mapping di un nome di dominio completo (ad esempio www\.contoso.com) all'indirizzo IP che ospita il servizio web.

## <a name="get-an-ssl-certificate"></a>Ottenere un certificato SSL

Esistono molti modi per ottenere un certificato SSL (digitale certificato). Il più comune consiste nell'acquistarne uno da un'__autorità di certificazione__ (CA). Indipendentemente da dove si ottiene il certificato, sono necessari i file seguenti:

* Un __certificato__. Il certificato deve contenere la catena di certificati completa e avere la codifica PEM.
* Una __chiave__. La chiave deve avere la codifica PEM.

Quando si richiede un certificato, è necessario fornire il nome di dominio completo (FQDN) dell'indirizzo che si intende usare per il servizio Web. Ad esempio, www\.contoso.com. L'indirizzo indicato nel certificato e l'indirizzo usato dai client vengono confrontati quando viene convalidata l'identità del servizio Web. Se gli indirizzi non corrispondono, i client ricevono un errore.

> [!TIP]
> Se l'autorità di certificazione non può fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità come [OpenSSL](https://www.openssl.org/) per modificare il formato.

> [!WARNING]
> Usare i certificati autofirmati solo per lo sviluppo. Non usarli nell'ambiente di produzione. I certificati autofirmati possono causare problemi nelle applicazioni client. Per altre informazioni, vedere la documentazione relativa alle librerie di rete usate nell'applicazione client.

## <a name="enable-ssl-and-deploy"></a>Abilitare SSL e distribuire

Per distribuire (o ridistribuire) il servizio con SSL abilitato, impostare il parametro `ssl_enabled` su `True`, dove applicabile. Impostare il parametro `ssl_certificate` sul valore del file di __certificato__ e `ssl_key` sul valore del file di __chiave__.

+ **Distribuire nel servizio Azure Kubernetes**

  Durante il provisioning del cluster servizio Azure Kubernetes, fornire i valori per i parametri associate a SSL come illustrato nel frammento di codice:

    ```python
    from azureml.core.compute import AksCompute

    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Distribuire in Istanze di Azure Container**

  Durante la distribuzione a ACI, specificare i valori per i parametri associate a SSL come illustrato nel frammento di codice:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Eseguire la distribuzione in dispositivi FPGA**

  Durante la distribuzione in dispositivi FPGA, specificare i valori per i parametri associati a SSL come illustrato nel frammento di codice:

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>Aggiorna DNS

Successivamente, è necessario aggiornare il DNS in modo che punti al servizio Web.

+ **Per ACI**:

  Usare gli strumenti forniti dal registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.

  A seconda del registrar e della durata (TTL) configurata per il nome di dominio, possono essere necessari da alcuni minuti a diverse ore prima che i client possano risolvere il nome di dominio.

+ **Per servizio Azure Kubernetes**:

  Aggiornare il DNS nella scheda "Configurazione" di "Indirizzo IP pubblico" del cluster servizio Azure Kubernetes come illustrato nell'immagine. È possibile trovare l'indirizzo IP pubblico come uno dei tipi di risorse create nel gruppo di risorse che contiene i nodi agente servizio Azure Kubernetes e altre risorse di rete.

  ![Servizio Azure Machine Learning: Protezione del servizio Web con SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Passaggi successivi
È possibile passare agli argomenti seguenti:
+ [Consume a machine learning model deployed as a web service](how-to-consume-web-service.md) (Come usare un modello di Machine Learning distribuito come servizio Web)
+ [Securely run experiments and inferencing inside an Azure Virtual Network](how-to-enable-virtual-network.md) (Eseguire esperimenti e inferenze in modo sicuro in una rete virtuale di Azure)
