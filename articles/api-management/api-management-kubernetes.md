---
title: Usare Gestione API di Azure con i microservizi distribuiti nel servizio Azure Kubernetes. Documenti Microsoft
description: In questo articolo vengono descritte le opzioni di distribuzione di Gestione API con AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480996"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Usare Gestione API di Azure con microservizi distribuiti nel servizio Azure KubernetesUse Azure API Management with microservices deployed in Azure Kubernetes Service

I microservizi sono perfetti per la creazione di API. Con [il servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) (AKS), è possibile distribuire e gestire rapidamente [un'architettura basata su microservizi](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) nel cloud. È quindi possibile sfruttare [Gestione API](https://aka.ms/apimrocks) di Azure (Gestione API) per pubblicare i microservizi come API per l'utilizzo interno ed esterno. In questo articolo vengono descritte le opzioni di distribuzione di Gestione API con AKS. Presuppone una conoscenza di base di Kubernetes, Gestione API e rete di Azure.It assumes basic knowledge of Kubernetes, API Management, and Azure networking. 

## <a name="background"></a>Background

Quando si pubblicano microservizi come API per l'utilizzo, può essere difficile gestire la comunicazione tra i microservizi e i client che li utilizzano. Esistono una moltitudine di problemi trasversali, ad esempio l'autenticazione, l'autorizzazione, la limitazione delle richieste, la memorizzazione nella cache, la trasformazione e il monitoraggio. Questi problemi sono validi indipendentemente dal fatto che i microservizi siano esposti a client interni o esterni. 

Il modello [gateway API](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) risolve questi problemi. Un gateway API funge da porta principale per i microservizi, separa i client dai microservizi, aggiunge un ulteriore livello di sicurezza e riduce la complessità dei microservizi eliminando l'onere della gestione dei problemi di taglio incrociato. 

[Gestione API di Azure](https://aka.ms/apimrocks) è una soluzione chiavi in volta per risolvere le esigenze del gateway API. È possibile creare rapidamente un gateway coerente e moderno per i microservizi e pubblicarli come API. Come soluzione di gestione API completa del ciclo di vita, offre anche funzionalità aggiuntive, tra cui un portale per sviluppatori self-service per l'individuazione delle API, la gestione del ciclo di vita delle API e l'analisi delle API.

Se usati insieme, AKS e Gestione API forniscono una piattaforma per la distribuzione, la pubblicazione, la protezione, il monitoraggio e la gestione delle API basate su microservizi. In questo articolo verranno eseguite alcune opzioni di distribuzione di AKS insieme a Gestione API. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Servizi e API

In un cluster Kubernetes, i contenitori vengono distribuiti in Pod , che sono [effimeri](https://kubernetes.io/docs/concepts/workloads/pods/pod/)e hanno un ciclo di vita. Quando un nodo di lavoro muore, i Pod in esecuzione sul nodo vengono persi. Pertanto, l'indirizzo IP di un Pod può cambiare in qualsiasi momento. Non possiamo contare su di esso per comunicare con il baccello. 

Per risolvere questo problema, Kubernetes ha introdotto il concetto di [servizi](https://kubernetes.io/docs/concepts/services-networking/service/). Un servizio Kubernetes è un livello di astrazione che definisce un gruppo logico di Pod e consente l'esposizione del traffico esterno, il bilanciamento del carico e l'individuazione dei servizi per tali Pod. 

Quando siamo pronti a pubblicare i nostri microservizi come API tramite Gestione API, dobbiamo pensare a come eseguire il mapping dei nostri servizi in Kubernetes alle API in Gestione API. Non ci sono regole stabilite. Dipende da come sono stati progettati e partizionati le funzionalità aziendali o i domini in microservizi all'inizio. Ad esempio, se i pod dietro un servizio sono responsabili di tutte le operazioni su una determinata risorsa (ad esempio, Cliente), il servizio può essere mappato a un'API. Se le operazioni su una risorsa sono partizionate in più microservizi (ad esempio, GetOrder, PlaceOrder), più servizi possono essere aggregati logicamente in un'unica API in Gestione API (vedere Fig. 1). 

Anche i mapping possono evolversi. Poiché Gestione API crea una facciata davanti ai microservizi, consente di eseguire il refactoring e ridimensionare correttamente i nostri microservizi nel tempo. 

![Eseguire il mapping dei servizi alle API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Distribuire Gestione API davanti ad AKSDeploy API Management front of AKS

Esistono alcune opzioni per distribuire Gestione API davanti a un cluster AKS. 

Mentre un cluster AKS viene sempre distribuito in una rete virtuale (VNet), non è necessario distribuire un'istanza di Gestione API in una rete virtuale. Quando Gestione API non risiede nella rete virtuale del cluster, il cluster AKS deve pubblicare endpoint pubblici a cui Gestione API può connettersi. In tal caso, è necessario proteggere la connessione tra Gestione API e AKS. In altre parole, è necessario garantire che il cluster sia accessibile solo esclusivamente tramite Gestione API. Esaminiamo le opzioni. 

### <a name="option-1-expose-services-publicly"></a>Opzione 1: Esporre i servizi pubblicamente

I servizi in un cluster AKS possono essere esposti pubblicamente usando tipi di servizio di NodePort, LoadBalancer o ExternalName.Services in an AKS cluster can be exposed publicly using [Service types](https://docs.microsoft.com/azure/aks/concepts-network) of NodePort, LoadBalancer, or ExternalName. In questo caso, i Servizi sono accessibili direttamente da Internet pubblico. Dopo aver distribuito Gestione API davanti al cluster, è necessario assicurarsi che tutto il traffico in ingresso passi attraverso Gestione API applicando l'autenticazione nei microservizi. Ad esempio, Gestione API può includere un token di accesso in ogni richiesta effettuata al cluster. Ogni microservizio è responsabile della convalida del token prima dell'elaborazione della richiesta. 


Questa potrebbe essere l'opzione più semplice per distribuire Gestione API davanti ad AKS, soprattutto se si dispone già di logica di autenticazione implementata nei microservizi. 

![Pubblicare servizi direttamente](./media/api-management-aks/direct.png)

Vantaggi:
* Facile configurazione sul lato Gestione API perché non è necessario iniettarlo nella rete virtuale del cluster
* Nessuna modifica sul lato AKS se i servizi sono già esposti pubblicamente e la logica di autenticazione esiste già nei microservizi

Svantaggi:
* Potenziale rischio per la sicurezza dovuto alla visibilità pubblica degli endpoint del servizio
* Nessun punto di ingresso singolo per il traffico cluster in ingressoNo single-entry point for inbound cluster traffic
* Complicazioni microservizi con logica di autenticazione duplicata

### <a name="option-2-install-an-ingress-controller"></a>Opzione 2: Installare un controller in ingressoOption 2: Install an Ingress Controller

Anche se l'opzione 1 potrebbe essere più facile, ha notevoli svantaggi come accennato in precedenza. Se un'istanza di Gestione API non risiede nella rete virtuale del cluster, l'autenticazione TLS reciproca (mTLS) è un modo efficace per garantire che il traffico sia sicuro e attendibile in entrambe le direzioni tra un'istanza di Gestione API e un cluster AKS. 

L'autenticazione TLS reciproca è [supportata in modo nativo](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) da Gestione API e può essere abilitata in Kubernetes [installando un Controller di ingresso](https://docs.microsoft.com/azure/aks/ingress-own-tls) (Fig. 3). Di conseguenza, l'autenticazione verrà eseguita nel controller di ingresso, che semplifica i microservizi. Inoltre, è possibile aggiungere gli indirizzi IP di Gestione API all'elenco consentito da Ingress per assicurarsi che solo Gestione API abbia accesso al cluster.  

 
![Pubblicare tramite un controller di ingresso](./media/api-management-aks/ingress-controller.png)


Vantaggi:
* Facile configurazione sul lato Gestione API perché non è necessario iniettarlo nella rete virtuale del cluster e mTLS è supportato in modo nativo
* Centralizza la protezione per il traffico del cluster in ingresso a livello di Controller in ingresso
* Riduce i rischi per la sicurezza riducendo al minimo gli endpoint cluster visibili pubblicamente

Svantaggi:
* Aumenta la complessità della configurazione del cluster a causa del lavoro aggiuntivo per installare, configurare e gestire il controller di ingresso e gestire i certificati utilizzati per mTLS
* Rischio di sicurezza dovuto alla visibilità pubblica degli endpoint del controller di ingresso


Quando si pubblicano le API tramite Gestione API, è semplice e comune proteggere l'accesso a tali API usando chiavi di sottoscrizione. Gli sviluppatori che devono utilizzare le API pubblicate devono includere una chiave di sottoscrizione valida nelle richieste HTTP quando effettuano chiamate a tali API. In caso contrario, le chiamate vengono immediatamente rifiutate dal gateway di Gestione API e non vengono inoltrate ai servizi di back-end.

Per ottenere una chiave di sottoscrizione per l'accesso alle API, è necessaria una sottoscrizione. Una sottoscrizione è essenzialmente un contenitore denominato per una coppia di chiavi di sottoscrizione. Gli sviluppatori con l'esigenza di utilizzare le API pubblicate possono ottenere le sottoscrizioni e non necessitano dell'approvazione da parte degli editori delle API. Gli editori delle API possono anche creare sottoscrizioni direttamente, per i consumer delle API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opzione 3: Distribuire IL servizio APIM all'interno della rete virtuale del clusterOption 3: Deploy COMM inside the cluster VNet

In alcuni casi, i clienti con vincoli normativi o requisiti di sicurezza rigorosi possono trovare soluzioni option 1 e 2 non praticabili a causa di endpoint esposti pubblicamente. In altri, il cluster AKS e le applicazioni che utilizzano i microservizi potrebbero risiedere all'interno della stessa rete virtuale, pertanto non vi è alcun motivo per esporre il cluster pubblicamente come tutto il traffico API rimarrà all'interno della rete virtuale. Per questi scenari, è possibile distribuire Gestione API nella rete virtuale del cluster. [Il livello API Management Premium](https://aka.ms/apimpricing) supporta la distribuzione della rete virtuale. 

Esistono due modalità di distribuzione di [Gestione API in una rete virtuale](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) - esterna e interna. 

Se i consumer API non risiedono nella rete virtuale del cluster, è necessario utilizzare la modalità esterna (Fig. 4). In questa modalità, il gateway di gestione API viene inserito nella rete virtuale del cluster ma accessibile da Internet pubblico tramite un servizio di bilanciamento del carico esterno. Aiuta a nascondere completamente il cluster mentre consente comunque ai client esterni di utilizzare i microservizi. Inoltre, è possibile usare le funzionalità di rete di Azure, ad esempio Gruppi di sicurezza di rete (NSG) per limitare il traffico di rete.

![Modalità VNet esterna](./media/api-management-aks/vnet-external.png)

Se tutti i consumer di API risiedono all'interno della rete virtuale del cluster, è possibile utilizzare la modalità interna (Fig. 5). In questa modalità, il gateway di gestione API viene inserito nella rete virtuale del cluster e accessibile solo dall'interno di questa rete virtuale tramite un servizio di bilanciamento del carico interno. Non è possibile raggiungere il gateway di gestione API o il cluster AKS da Internet pubblico. 

![Modalità VNet interna](./media/api-management-aks/vnet-internal.png)

 In entrambi i casi, il cluster AKS non è visibile pubblicamente. Rispetto all'opzione 2, il controller in ingresso potrebbe non essere necessario. A seconda dello scenario e della configurazione, l'autenticazione potrebbe essere ancora necessaria tra Gestione API e i microservizi. Ad esempio, se viene adottata una mesh di servizio, richiede sempre l'autenticazione TLS reciproca. 

Vantaggi:
* L'opzione più sicura perché il cluster AKS non dispone di un endpoint pubblico
* Semplifica la configurazione del cluster poiché non dispone di alcun endpoint pubblico
* Possibilità di nascondere sia Gestione API che AKS all'interno della rete virtuale utilizzando la modalità interna
* Possibilità di controllare il traffico di rete usando le funzionalità di rete di Azure, ad esempio Gruppi di sicurezza di rete (NSG)Ability to control network traffic using Azure networking capabilities such as Network Security Groups (NSG)

Svantaggi:
* Aumenta la complessità della distribuzione e configurazione di Gestione API per l'utilizzo all'interno della rete virtuale

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sui concetti di [rete per le applicazioni in AKSLearn](https://docs.microsoft.com/azure/aks/concepts-network) more about Network concepts for applications in AKS
* Altre informazioni su [Come usare Gestione API con reti virtualiLearn](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) more about How to use API Management with virtual networks





