---
title: Come ridimensionare un cluster del servizio contenitore di Azure per Machine Learning | Microsoft Docs
description: 'Ridimensionamento di un cluster del servizio contenitore di Azure: ridimensionamento automatico e ridimensionamento statico; ridimensionamento del numero di nodi del cluster'
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 49e22c5136da67f62a43374817fb1e462fcbcaf0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Ridimensionamento del cluster per gestire la velocità effettiva del servizio Web

## <a name="why-scale-the-cluster"></a>Perché ridimensionare il cluster?

Il ridimensionamento del cluster del servizio contenitore di Azure costituisce uno dei modi più efficaci per ottimizzare la velocità effettiva del servizio mantenendo al minimo le dimensioni del cluster e limitando quindi i costi. 

Per comprendere meglio il concetto di ridimensionamento automatico, si consideri l'esempio seguente di un cluster che esegue tre servizi Web:

![Esempio: Tre servizi in un cluster](media/how-to-scale-clusters/three-services.png)

I servizi possono avere vari picchi di domanda: in questo caso, il Servizio 1 (linea blu) richiede 40 pod in corrispondenza del picco di domanda, il Servizio 2 (linea arancione) 38 pod e il Servizio 3 (linea grigia) 50 pod. Se si riserva singolarmente la capacità di picco richiesta da ogni servizio, per il cluster saranno necessari in totale almeno 40 + 38 + 50 = 128 pod.

Se invece si prende in considerazione l'utilizzo effettivo dei pod in qualsiasi momento (rappresentato nel grafico dalla linea nera tratteggiata), si osserverà come il *numero massimo di pod usati contemporaneamente*  è pari a 64, quando alle ore 20.00 si verifica il picco di domanda per il Servizio 3. In quel momento, il Servizio 3 usa 50 pod, ma il Servizio 2 ne usa solo 9 e il Servizio 1 solo 5. Questo rappresenta il momento di *utilizzo massimo* del cluster, ovvero il cluster non usa mai più di 64 pod: la metà dei 128 pod necessari ridimensionando i tre servizi in modo indipendente per soddisfare il rispettivo utilizzo massimo.

Riassegnando i pod nel cluster (ossia ridimensionando il cluster) in modo da soddisfare la domanda corrente di ogni servizio, anziché richiedere semplicemente le risorse sufficienti per il picco di domanda di tutti i servizi, è possibile quindi ridurre le dimensioni del cluster. In questo semplice esempio, il ridimensionamento automatico consente di ridurre il numero di pod da 128 a 64, dimezzando così le dimensioni del cluster.

Ridimensionare il numero di pod è un'operazione relativamente veloce, che richiede meno di un minuto e, quindi, non influisce in modo significativo sulla velocità di risposta del servizio.

> [!NOTE]
> Il ridimensionamento di un cluster non aiuta a risolvere i problemi associati alla latenza delle richieste. Per motivi di operazionalizzazione, tuttavia, il ridimensionamento dovrebbe contribuire ad aumentare il numero di esiti positivi e ridurre gli errori di tipo Servizio non disponibile. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Come ridimensionare i servizi Web in un cluster del servizio contenitore di Azure

Per impostazione predefinita, l'opzione di configurazione dell'interfaccia della riga di comando di Gestione modelli configura nell'ambiente due agenti e un pod e installa l'interfaccia della riga di comando di Kubernetes.

È possibile ridimensionare i servizi Web distribuiti nel servizio contenitore di Azure modificando gli elementi seguenti:

* Il numero di nodi agente nel cluster
* Il numero di repliche pod di Kubernetes in esecuzione sui nodi agente

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Ridimensionamento del numero di nodi nel cluster

Il comando seguente consente di impostare il numero di nodi agente nel cluster:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Questa operazione richiederà qualche minuto. Per altre informazioni sul ridimensionamento del numero di nodi nel cluster, vedere [Ridimensionare i nodi agente in un cluster del servizio contenitore](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Ridimensionamento del numero di repliche di pod Kubernetes in un cluster
 
È possibile ridimensionare il numero di repliche di pod assegnate al cluster usando l'interfaccia della riga di comando di Azure Machine Learning o il [dashboard di Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Per altre informazioni sui pod di replica Kubernetes, vedere la documentazione sui [pod Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/).

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Ridimensionamento di un cluster con l'interfaccia della riga di comando di Azure Machine Learning

Esistono due modi per ridimensionare un cluster usando l'interfaccia della riga di comando:

- Autoscale
- Ridimensionamento statico

Il ridimensionamento automatico viene attivato per impostazione predefinita durante la creazione del servizio e, in molti casi, è il metodo di ridimensionamento predefinito.

##### <a name="autoscale"></a>Autoscale

Il comando seguente consente di abilitare il ridimensionamento automatico e di impostare il numero minimo e il numero massimo di repliche per il servizio.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Impostando `autoscale-min-replicas` su 5, ad esempio, verranno create cinque repliche. Per ottenere un numero ottimale per il servizio Web, impostare il numero su un valore come 10 e monitorare il numero di messaggi di errore 503. Modificare quindi il numero di conseguenza.


| Nome parametro | type | DESCRIZIONE |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | boolean | Specifica se il ridimensionamento automatico è abilitato. Predefinito: true |
| `autoscale-min-replicas` | numero intero | Specifica il numero minimo di pod. Deve essere uguale o maggiore di 0. Predefinito: 1 |
| `autoscale-max-replicas` | numero intero | Specifica il numero massimo di pod. Deve essere uguale o maggiore di 1. Se autoscale-max-replicas è inferiore a autoscale-min-replicas, il valore di autoscale-max-replicas verrà ignorato. Valore predefinito: 10 |
| `autoscale-refresh-period-seconds` | numero intero | Specifica l'intervallo in secondi tra gli aggiornamenti di ridimensionamento automatico. Predefinito: 1 |
| `autoscale-target-utilization` | numero intero | Specifica la percentuale di utilizzo di destinazione del ridimensionamento automatico, compresa tra 1 e 100. Predefinito: 70 |

Il ridimensionamento automatico garantisce che siano soddisfatte le due condizioni seguenti:

1. Sia raggiunto l'utilizzo di destinazione
2. Il ridimensionamento non superi mai l'impostazione minima e quella massima

I servizi in esecuzione su un cluster si contendono le risorse del cluster. I servizi con ridimensionamento automatico aumentano l'utilizzo delle risorse del cluster in caso di incremento del numero di richieste al secondo e liberano progressivamente le risorse in caso di riduzione del numero di richieste al secondo. Le risorse del cluster possono essere acquisite su richiesta purché sia possibile acquisire risorse per il servizio.

Per altre informazioni sull'utilizzo dei parametri di ridimensionamento automatico, vedere il documento [Informazioni di riferimento sull'interfaccia della riga di comando di Gestione modelli](model-management-cli-reference.md).

##### <a name="static-scale"></a>Ridimensionamento statico

In generale, è preferibile evitare il ridimensionamento statico, poiché non consente la riduzione delle dimensioni del cluster permessa dal ridimensionamento automatico. In alcune situazioni, tuttavia, può essere consigliato il ridimensionamento statico. Quando un cluster è dedicato a un unico servizio, ad esempio, il ridimensionamento automatico non offre alcun vantaggio, poiché tutte le risorse del cluster devono essere assegnate a quel servizio.

Per poter ridimensionare un cluster in modo statico, è necessario disabilitare l'opzione di ridimensionamento automatico. Per disabilitare il ridimensionamento automatico, usare il comando seguente:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Dopo aver disattivato il ridimensionamento automatico, usare il comando seguente per impostare direttamente il numero di repliche per un servizio.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Per altre informazioni sul ridimensionamento del numero di nodi nel cluster, vedere Ridimensionare i nodi agente in un cluster del servizio contenitore.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Ridimensionamento del numero di repliche usando il dashboard di Kubernetes

Nella riga di comando digitare:

```
kubectl proxy
```

In Windows, il percorso di installazione di Kubernetes non viene aggiunto automaticamente al percorso. È quindi necessario passare prima alla cartella di installazione:

```
c:\users\<user name>\bin
```

Dopo aver eseguito il comando, viene visualizzato il messaggio informativo seguente:

```
Starting to serve on 127.0.0.1:8001
```

Se la porta è già in uso, viene visualizzato un messaggio simile all'esempio seguente:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

È possibile specificare un numero di porta alternativo usando il parametro *--port*.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Dopo aver avviato il server di dashboard, aprire un browser e immettere l'URL seguente:

```
127.0.0.1:<port number>/ui
```

Nella schermata principale del dashboard fare clic su **Distribuzioni** sulla barra di spostamento a sinistra. Se il riquadro di spostamento non viene visualizzato, selezionare l'icona ![Menu composto da tre linee orizzontali](media/how-to-scale-clusters/icon-hamburger.png) in alto a sinistra.

Individuare la distribuzione da modificare, fare clic su questa icona ![Icona di menu composta da tre punti verticali](media/how-to-scale-clusters/icon-kebab.png) sul lato destro e quindi fare clic su **View/edit YAML** (Visualizza/Modifica YAML).

Nella schermata di distribuzione di modifica, individuare il nodo *spec*, modificare il valore *replicas* e fare clic su **Aggiorna**.
