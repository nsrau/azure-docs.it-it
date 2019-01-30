---
title: Distribuzione di servizi cognitivi di Azure ad Azure Stack | Microsoft Docs
description: Informazioni su come distribuire servizi cognitivi di Azure ad Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 331a71d4f807e1e596a91c1463064e3f6dcbd1e1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247035"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Distribuzione di servizi cognitivi di Azure ad Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!Note]  
> Servizi cognitivi di Azure in Azure Stack è disponibile in anteprima.

È possibile usare servizi cognitivi di Azure con supporto per i contenitori in Azure Stack. Supporto dei contenitori in servizi cognitivi di Azure consente di usare le stesse API avanzate disponibili in Azure. L'uso dei contenitori consente una flessibilità nel punto in cui distribuire e ospitare i servizi forniti [contenitori Docker](https://www.docker.com/what-container). Supporto dei contenitori è attualmente disponibile in anteprima per un subset di servizi cognitivi di Azure, incluse le parti del [visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [viso](https://docs.microsoft.com/azure/cognitive-services/face/overview), e [testo Analitica](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), e [Language Understanding Intelligent Service](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o il servizio, incluse le dipendenze e la configurazione, vengono impacchettati come un'immagine del contenitore. Con una minima o senza alcuna modifica, è possibile distribuire un'immagine in un host contenitore. Ogni contenitore è isolato da altri contenitori e dal sistema operativo sottostante. Il sistema, stesso, solo include i componenti necessari per eseguire l'immagine. Un host contenitore con un footprint ridotto rispetto a una macchina virtuale. Inoltre, è possibile creare contenitori dalle immagini per le attività a breve termine e rimosso quando non servono più.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Usare i contenitori con servizi cognitivi in Azure Stack

- **Il controllo di dati**  
  Consentire agli utenti di app di controllare i propri dati quando si usa servizi cognitivi. Servizi cognitivi consente di fornire agli utenti di app che non è possibile inviare i dati in Azure globale o il cloud pubblico.

- **Il controllo di aggiornamenti di modelli**  
  Fornire agli utenti di app di versione e aggiornamento dei modelli distribuiti nella soluzione offerta.

- **Architettura portabile**  
  Abilitare la creazione di un'architettura di app portabile, in modo da poter distribuire la soluzione di cloud pubblico, privato del cloud on-premises o il bordo. È possibile distribuire il contenitore Kubernetes nel servizio di Azure, istanze di contenitore di Azure o a un cluster Kubernetes in Azure stack. Per altre informazioni, vedere [Deploy Kubernetes to Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuire Kubernetes in Azure Stack).

- **Velocità effettiva elevata e bassa latenza**  
   Fornire agli utenti di app la possibilità di ridimensionare i picchi di traffico per una velocità effettiva elevata e bassa latenza. Abilitare i servizi cognitivi per l'esecuzione in Azure Kubernetes Service fisicamente vicino al loro logica dell'applicazione e dati.

Con Azure Stack, distribuire i contenitori di servizi cognitivi in un cluster Kubernetes con i contenitori di applicazioni per la disponibilità elevata e scalabilità elastica. È possibile sviluppare l'applicazione mediante la combinazione di servizi cognitivi con i componenti creati in servizi App, funzioni, Blob di archiviazione, SQL o mySQL database. 

Per altre informazioni sui contenitori di servizi cognitivi, visitare [supporto dei contenitori in servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Distribuire l'API viso Azure

Questo articolo descrive come distribuire l'API viso di Azure nel cluster Kubernetes in Azure Stack. È possibile usare lo stesso approccio per distribuire altri contenitori di servizi cognitivi nel cluster Kubernetes di Azure Stack.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

1.  Richiedere l'accesso al registro contenitori di effettuare il pull delle immagini del contenitore viso dal registro contenitori di servizi cognitivi di Azure. Per informazioni dettagliate passare alla sezione del [richiedere l'accesso al registro contenitori privato](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Preparare un cluster Kubernetes in Azure Stack. È possibile seguire l'articolo [distribuzione di Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Creare le risorse di Azure

Creare una risorsa di servizi cognitivi in Azure per visualizzare in anteprima i contenitori di viso, LUIS o testo riconoscere, rispettivamente. È necessario usare l'URL di endpoint e chiave di sottoscrizione dalla risorsa per creare un'istanza dei contenitori dei servizi cognitivi.

1.  Creare una risorsa di Azure nel portale di Azure. Se si desidera visualizzare in anteprima i contenitori di viso, è necessario creare innanzitutto una risorsa faccia corrispondente nel portale di Azure. Per altre informazioni, vedere [Avvio rapido: Creare un account di Servizi cognitivi nel portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

    >  [!Note]  
    >  La risorsa faccia o visione artificiale è necessario usare il piano tariffario F0.

2.  Ottenere l'URL dell'endpoint e la chiave di sottoscrizione per la risorsa di Azure. Dopo aver creata la risorsa di Azure, è necessario utilizzare l'URL di endpoint e chiave di sottoscrizione dalla risorsa a creare un'istanza di contenitore viso, LUIS o riconoscere testo corrispondente per l'anteprima.

## <a name="create-a-kubernetes-secret"></a>Creare un segreto Kubernetes 

Take sfrutta il Kubectl create secret comando per accedere al registro contenitori privato. Sostituire **&lt;username&gt;** con il nome utente e **&lt;password&gt;** con la password specificata in una credenziale è stato ricevuto da Azure Team di servizi cognitivi.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Preparare un YAML configurare file

Si intende usare il codice YAML configurazione file per semplificare la distribuzione del servizio cognitivo nel cluster Kubernetes.

Ecco un esempio YAML configurare file di distribuzione del servizio faccia in Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

In questo YAML configurare file, usare il segreto che sia usato per ottenere le immagini del contenitore di servizi cognitivi dal registro contenitori di Azure. Si e utilizzare il file del segreto per distribuire una replica specifica del contenitore. È anche possibile creare un servizio di bilanciamento del carico per assicurarsi che gli utenti possono accedere a questo servizio esternamente.

Dettagli sui campi di chiave:

| Campo | Note |
| --- | --- |
| replicaNumber | Definisce le repliche iniziali di istanze da creare. È certamente possibile scalare in un secondo momento dopo la distribuzione. |
| ImageLocation | Indica la posizione dell'immagine del contenitore specifiche di servizi cognitivi in Registro contenitori di AZURE. Ad esempio, il servizio di volti: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |L'URL dell'Endpoint indicato nel passaggio di [Crea risorsa di Azure](#create-azure-resources) |
| ApiKey | La chiave di sottoscrizione annotate nel passaggio di [Crea risorsa di Azure](#create-azure-resources) |
| SecretName | Il nome del segreto annotato nel passaggio di [crea segrete per accedere al registro contenitori privato](#create-secrete-to-access-the-private-container-registry) |

## <a name="deploy-the-cognitive-service"></a>Distribuire i servizi cognitivi

Utilizzare il comando seguente per distribuire i contenitori dei servizi cognitivi

```bash  
    Kubectl apply -f <yamlFineName>
```
Utilizzare il comando seguente per controllare come viene distribuito: 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Testare i servizi cognitivi

Accesso di [specifica OpenAPI](https://swagger.io/docs/specification/about/) (in precedenza la specifica Swagger), che descrive le operazioni supportate da un contenitore, un'istanza dal **swagger/** URI relativo per quel contenitore. L'URI seguente consente ad esempio di accedere alla specifica OpenAPI per il contenitore Analisi del sentiment di cui è stata creata un'istanza nell'esempio precedente:

```HTTP  
http:<External IP>:5000/swagger
```

È possibile ottenere l'indirizzo IP esterno del comando seguente: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Provare i servizi con Python

È possibile provare a convalidare i servizi cognitivi in Azure Stack eseguendo alcune semplici script di Python. Sono disponibili esempi di avvio rapido di Python ufficiali per [visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [viso](https://docs.microsoft.com/azure/cognitive-services/face/overview), e [testo Analitica](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), e [Language Understanding Intelligent Service](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) ( LUIS) per riferimento.

Esistono due aspetti è necessario tenere a mente per eseguire l'app Python nei servizi in esecuzione in contenitori: 
1. Servizi cognitivi nel contenitore delle chiavi secondarie non sono necessario per l'autenticazione, ma è comunque necessario immettere qualsiasi stringa come segnaposto per soddisfare il SDK. 
2. Sostituire il base_URL con indirizzo IP dell'EndPoint del servizio effettivo 

Ecco un esempio Python script usato viso services SDK per Python per rilevare e frame visi in un'immagine. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Passaggi successivi

[Come installare ed eseguire i contenitori di API visione artificiale.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Come installare ed eseguire i contenitori l'API viso](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[Come installare ed eseguire i contenitori di Analitica API traduzione testuale](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Come installare ed eseguire i contenitori di Language Understanding (LIUS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)