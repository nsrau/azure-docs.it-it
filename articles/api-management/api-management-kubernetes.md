---
title: Usare gestione API di Azure con microservizi distribuiti nel servizio Azure Kubernetes | Microsoft Docs
description: Questo articolo descrive le opzioni di distribuzione di gestione API con AKS
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75480996"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Usare gestione API di Azure con microservizi distribuiti nel servizio Azure Kubernetes

I microservizi sono perfetti per la creazione di API. Con [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) è possibile distribuire e gestire rapidamente un' [architettura basata su microservizi](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) nel cloud. È quindi possibile usare [gestione API di Azure](https://aka.ms/apimrocks) (gestione API) per pubblicare i microservizi come API per l'uso interno ed esterno. Questo articolo descrive le opzioni di distribuzione di gestione API con AKS. Si presuppone una conoscenza di base di Kubernetes, gestione API e rete di Azure. 

## <a name="background"></a>Informazioni

Quando si pubblicano microservizi come API per l'utilizzo, può essere difficile gestire la comunicazione tra i microservizi e i client che li utilizzano. Esistono numerose problematiche trasversali, ad esempio l'autenticazione, l'autorizzazione, la limitazione, la memorizzazione nella cache, la trasformazione e il monitoraggio. Questi problemi sono validi indipendentemente dal fatto che i microservizi siano esposti a client interni o esterni. 

Il modello di [gateway API](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) risolve questi problemi. Un gateway API funge da porta principale per i microservizi, separa i client dai microservizi, aggiunge un ulteriore livello di sicurezza e riduce la complessità dei microservizi, eliminando il carico di lavoro per la gestione delle problematiche incrociate. 

[Gestione API di Azure](https://aka.ms/apimrocks) è una soluzione chiavi in mano per risolvere le esigenze del gateway API. È possibile creare rapidamente un gateway coerente e moderno per i microservizi e pubblicarli come API. Come soluzione di gestione API con ciclo di vita completo, fornisce anche funzionalità aggiuntive, tra cui un portale per sviluppatori self-service per l'individuazione delle API, la gestione del ciclo di vita delle API e l'analisi API.

Quando vengono usati insieme, AKS e gestione API offrono una piattaforma per la distribuzione, la pubblicazione, la protezione, il monitoraggio e la gestione delle API basate su microservizi. In questo articolo verranno illustrate alcune opzioni di distribuzione di AKS insieme a gestione API. 

## <a name="kubernetes-services-and-apis"></a>Servizi e API Kubernetes

In un cluster Kubernetes, i contenitori vengono distribuiti in [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/), che sono temporanei e hanno un ciclo di vita. Quando un nodo del ruolo di lavoro viene interrotto, i pod in esecuzione nel nodo vengono persi. Pertanto, l'indirizzo IP di un pod può cambiare in qualsiasi momento. Non è possibile fare affidamento su di esso per comunicare con il pod. 

Per risolvere il problema, Kubernetes ha introdotto il concetto di [Servizi](https://kubernetes.io/docs/concepts/services-networking/service/). Un servizio Kubernetes è un livello di astrazione che definisce un gruppo logico di Pod e Abilita l'esposizione del traffico esterno, il bilanciamento del carico e l'individuazione dei servizi per tali pod. 

Quando si è pronti a pubblicare i microservizi come API tramite gestione API, è necessario pensare a come eseguire il mapping dei servizi in Kubernetes alle API in gestione API. Nessuna regola set. Dipende da come sono state progettate e partizionate le funzionalità o i domini aziendali in microservizi all'inizio. Ad esempio, se i pod dietro un servizio sono responsabili di tutte le operazioni su una determinata risorsa (ad esempio, Customer), il servizio può essere mappato a un'API. Se le operazioni su una risorsa vengono partizionate in più microservizi (ad esempio, GetOrder, PlaceOrder), è possibile che più servizi vengano aggregati logicamente in un'unica API in gestione API (vedere fig. 1). 

I mapping possono anche evolversi. Dal momento che gestione API crea una facciata davanti ai microservizi, consente di effettuare il refactoring e dimensionare correttamente i microservizi nel tempo. 

![Eseguire il mapping dei servizi alle API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Distribuire Gestione API davanti a AKS

Sono disponibili alcune opzioni di distribuzione di gestione API davanti a un cluster AKS. 

Mentre un cluster AKS viene sempre distribuito in una rete virtuale (VNet), non è necessario distribuire un'istanza di gestione API in un VNet. Quando Gestione API non si trova all'interno del cluster VNet, il cluster AKS deve pubblicare endpoint pubblici per la connessione di gestione API. In tal caso, è necessario proteggere la connessione tra gestione API e AKS. In altre parole, è necessario assicurarsi che sia possibile accedere esclusivamente al cluster solo tramite gestione API. Verranno ora esaminate le opzioni. 

### <a name="option-1-expose-services-publicly"></a>Opzione 1: esporre pubblicamente i servizi

I servizi in un cluster AKS possono essere esposti pubblicamente usando i [tipi di servizio](https://docs.microsoft.com/azure/aks/concepts-network) Deport, LoadBalancer o ExternalName. In questo caso i servizi sono accessibili direttamente da Internet pubblico. Dopo aver distribuito gestione API davanti al cluster, è necessario assicurarsi che tutto il traffico in ingresso attraversi gestione API applicando l'autenticazione nei microservizi. Ad esempio, gestione API può includere un token di accesso in ogni richiesta effettuata al cluster. Ogni microservizio è responsabile della convalida del token prima dell'elaborazione della richiesta. 


Questo potrebbe essere l'opzione più semplice per distribuire Gestione API davanti a AKS, soprattutto se si dispone già di una logica di autenticazione implementata nei microservizi. 

![Pubblica direttamente i servizi](./media/api-management-aks/direct.png)

Vantaggi:
* Facile configurazione sul lato gestione API perché non è necessario inserire il VNet nel cluster
* Nessun cambiamento sul lato AKS se i servizi sono già esposti pubblicamente e la logica di autenticazione esiste già nei microservizi

Svantaggi:
* Potenziale rischio per la sicurezza a causa della visibilità pubblica degli endpoint di servizio
* Nessun punto di ingresso singolo per il traffico in ingresso del cluster
* Complica i microservizi con la logica di autenticazione duplicata

### <a name="option-2-install-an-ingress-controller"></a>Opzione 2: installare un controller di ingresso

Sebbene l'opzione 1 possa essere più semplice, presenta svantaggi significativi, come indicato in precedenza. Se un'istanza di gestione API non risiede nel cluster VNet, l'autenticazione reciproca TLS (mTLS) è un modo efficace per garantire che il traffico sia sicuro e attendibile in entrambe le direzioni tra un'istanza di gestione API e un cluster AKS. 

L'autenticazione TLS reciproca è supportata in modo [nativo](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) da gestione API e può essere abilitata in Kubernetes [installando un controller di ingresso](https://docs.microsoft.com/azure/aks/ingress-own-tls) (fig. 3). Di conseguenza, l'autenticazione verrà eseguita nel controller di ingresso, che semplifica i microservizi. Inoltre, è possibile aggiungere gli indirizzi IP di gestione API all'elenco degli indirizzi consentiti in ingresso per assicurarsi che solo gestione API abbia accesso al cluster.  

 
![Pubblicare tramite un controller di ingresso](./media/api-management-aks/ingress-controller.png)


Vantaggi:
* Una configurazione semplice sul lato gestione API perché non deve essere inserita nel cluster VNet e mTLS è supportata in modo nativo
* Centralizza la protezione per il traffico in ingresso del cluster al livello del controller di ingresso
* Riduzione dei rischi per la sicurezza riducendo al minimo gli endpoint del cluster visibili pubblicamente

Svantaggi:
* Aumenta la complessità della configurazione del cluster a causa del lavoro aggiuntivo per l'installazione, la configurazione e la gestione del controller di ingresso e la gestione dei certificati usati per mTLS
* Rischi per la sicurezza a causa della visibilità pubblica degli endpoint del controller di ingresso


Quando si pubblicano le API tramite Gestione API, è semplice e comune proteggere l'accesso a tali API usando chiavi di sottoscrizione. Gli sviluppatori che devono utilizzare le API pubblicate devono includere una chiave di sottoscrizione valida nelle richieste HTTP quando effettuano chiamate a tali API. In caso contrario, le chiamate vengono immediatamente rifiutate dal gateway di Gestione API e non vengono inoltrate ai servizi di back-end.

Per ottenere una chiave di sottoscrizione per l'accesso alle API, è necessaria una sottoscrizione. Una sottoscrizione è essenzialmente un contenitore denominato per una coppia di chiavi di sottoscrizione. Gli sviluppatori con l'esigenza di utilizzare le API pubblicate possono ottenere le sottoscrizioni e non necessitano dell'approvazione da parte degli editori delle API. Gli editori delle API possono anche creare sottoscrizioni direttamente, per i consumer delle API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opzione 3: distribuire Gestione API all'interno del cluster VNet

In alcuni casi, i clienti con vincoli normativi o requisiti di sicurezza severi possono trovare soluzioni opzionali 1 e 2 non valide a causa di endpoint esposti pubblicamente. In altre, il cluster AKS e le applicazioni che usano i microservizi potrebbero risiedere all'interno della stessa VNet, pertanto non esiste alcun motivo per esporre il cluster pubblicamente perché tutto il traffico API rimarrà all'interno della VNet. Per questi scenari, è possibile distribuire Gestione API nel cluster VNet. Il [livello Premium di gestione API](https://aka.ms/apimpricing) supporta la distribuzione di VNet. 

Sono disponibili due modalità di [distribuzione di gestione API in una VNet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) , esterna e interna. 

Se i consumer di API non si trovano nel VNet del cluster, è consigliabile usare la modalità esterna (Figg. 4). In questa modalità il gateway di gestione API viene inserito nel VNet del cluster, ma accessibile dalla rete Internet pubblica tramite un servizio di bilanciamento del carico esterno. Consente di nascondere completamente il cluster, consentendo comunque ai client esterni di utilizzare i microservizi. Inoltre, è possibile usare le funzionalità di rete di Azure, ad esempio i gruppi di sicurezza di rete (NSG) per limitare il traffico di rete.

![Modalità VNet esterna](./media/api-management-aks/vnet-external.png)

Se tutti i consumer di API si trovano all'interno del cluster VNet, è possibile usare la modalità interna (Figg. 5). In questa modalità il gateway di gestione API viene inserito nel VNET del cluster e accessibile solo dall'interno di questo VNet tramite un servizio di bilanciamento del carico interno. Non è possibile raggiungere il gateway di gestione API o il cluster AKS da Internet pubblico. 

![Modalità VNet interna](./media/api-management-aks/vnet-internal.png)

 In entrambi i casi, il cluster AKS non è visibile pubblicamente. Rispetto all'opzione 2, il controller di ingresso potrebbe non essere necessario. A seconda dello scenario e della configurazione, potrebbe essere ancora necessaria l'autenticazione tra gestione API e i microservizi. Se, ad esempio, viene adottata una rete mesh di servizi, è sempre necessaria l'autenticazione reciproca TLS. 

Vantaggi:
* L'opzione più sicura perché il cluster AKS non ha un endpoint pubblico
* Semplifica la configurazione del cluster perché non ha un endpoint pubblico
* Possibilità di nascondere gestione API e AKS all'interno della VNet usando la modalità interna
* Possibilità di controllare il traffico di rete usando le funzionalità di rete di Azure, ad esempio i gruppi di sicurezza di rete (NSG)

Svantaggi:
* Aumenta la complessità della distribuzione e della configurazione di gestione API per l'uso all'interno di VNet

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [concetti di rete per le applicazioni in AKS](https://docs.microsoft.com/azure/aks/concepts-network)
* Altre informazioni su [come usare gestione API con le reti virtuali](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





