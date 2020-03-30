---
title: Funzionamento della connessione del computer di sviluppo al cluster AKS
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi nell'uso di Azure Dev Spaces per connettere il computer di sviluppo al cluster del servizio Azure Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241712"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Funzionamento della connessione del computer di sviluppo al cluster AKS

Con Azure Dev Spaces, è possibile connettere il computer di sviluppo al cluster AKS, consentendo di eseguire ed eseguire il debug di codice nel computer di sviluppo come se fosse in esecuzione nel cluster. Azure Dev Spaces reindirizza il traffico tra il cluster AKS connesso eseguendo un pod nel cluster che funge da agente remoto per reindirizzare il traffico tra il computer di sviluppo e il cluster. Questo reindirizzamento del traffico consente al codice nel computer di sviluppo e ai servizi in esecuzione nel cluster AKS di comunicare come se si trovano nello stesso cluster AKS. Questa connessione consente inoltre di eseguire ed eseguire il debug di codice con o senza un contenitore nel computer di sviluppo. La connessione del computer di sviluppo al cluster consente di sviluppare rapidamente l'applicazione ed eseguire test end-to-end.

## <a name="connecting-to-your-cluster"></a>Connessione al cluster

Si connette al cluster AKS esistente utilizzando [Visual Studio Code][vs-code] con l'estensione Azure Dev [Spaces][azds-vs-code] installato in esecuzione su MacOS o Windows 10. Quando si stabilisce una connessione, è possibile reindirizzare tutto il traffico da e verso un pod nuovo o esistente nel cluster al computer di sviluppo.

> [!NOTE]
> Quando si usa Visual Studio Code per connettersi al cluster, l'estensione Azure Dev Spaces offre la possibilità di reindirizzare un servizio al computer di sviluppo. Questa opzione è un modo pratico per identificare un contenitore per il reindirizzamento. Tutto il reindirizzamento tra il cluster AKS e il computer di sviluppo è per un pod.

La connessione al cluster non richiede l'abilitazione degli spazi di sviluppo di Azure nel cluster. Al contrario, quando l'estensione Azure Dev Spaces stabilisce una connessione al cluster, è invece:Instead, when the Azure Dev Spaces extension establishes a connection to your cluster, it:

* Sostituisce il contenitore nel pod nel cluster AKS con un contenitore di agenti remoti che reindirizza il traffico al computer di sviluppo. Quando si reindirizza un nuovo pod, Azure Dev Spaces crea un nuovo pod nel cluster AKS con l'agente remoto.
* Esegue [il portforward kubectl][kubectl-port-forward] nel computer di sviluppo per inoltrare il traffico dal computer di sviluppo all'agente remoto in esecuzione nel cluster.
* Raccoglie informazioni sull'ambiente dal cluster utilizzando l'agente remoto. Queste informazioni sull'ambiente includono variabili di ambiente, servizi visibili, montaggi di volumi e supporti segreti.
* Configura l'ambiente nel terminale di visual Studio Code in modo che il servizio nel computer di sviluppo possa accedere alle stesse variabili come se fosse in esecuzione nel cluster.  
* Aggiorna il file hosts per eseguire il mapping dei servizi nel cluster AKS agli indirizzi IP locali nel computer di sviluppo. Queste voci del file host consentono al codice in esecuzione nel computer di sviluppo di effettuare richieste ad altri servizi in esecuzione nel cluster. Per aggiornare il file hosts, Azure Dev Spaces richiederà l'accesso come amministratore nel computer di sviluppo quando ci si connette al cluster.

Se nel cluster sono abilitati spazi di sviluppo di Azure, è anche possibile usare il reindirizzamento del [traffico offerto da Azure Dev Spaces.][how-it-works-routing] Il reindirizzamento del traffico offerto da Azure Dev Spaces consente di connettersi a una copia del servizio in esecuzione in uno spazio di sviluppo figlio. L'uso di uno spazio di sviluppo figlio consente di evitare di interrompere il lavoro di altri utenti nello spazio di sviluppo padre poiché reindirizza solo il traffico destinato all'istanza del servizio dello spazio figlio, lasciando invariata l'istanza dello spazio padre del servizio.

Dopo la connessione al cluster, il traffico viene instradato al computer di sviluppo, indipendentemente dal fatto che il servizio sia in esecuzione nel computer di sviluppo.

## <a name="running-code-on-your-development-computer"></a>Esecuzione di codice nel computer di sviluppoRunning code on your development computer

Dopo aver stabilito una connessione al cluster AKS, è possibile eseguire qualsiasi codice in modo nativo nel computer, senza contenitori. Qualsiasi traffico di rete ricevuto dall'agente remoto viene reindirizzato alla porta locale specificata durante la connessione in modo che il codice in esecuzione in modo nativo possa accettare ed elaborare tale traffico. Le variabili di ambiente, i volumi e i segreti del cluster vengono resi disponibili per il codice in esecuzione nel computer di sviluppo. Inoltre, a causa delle voci del file hosts e dell'inoltro delle porte aggiunto al computer di sviluppo da Azure Dev Spaces, il codice può inviare traffico di rete ai servizi in esecuzione nel cluster usando i nomi dei servizi del cluster e che il traffico viene inoltrato al servizi in esecuzione nel cluster.

Poiché il codice è in esecuzione nel computer di sviluppo, è possibile usare qualsiasi strumento normalmente utilizzato per lo sviluppo per eseguire il codice ed eseguirne il debug. Il traffico viene instradato tra il computer di sviluppo e il cluster per tutto il tempo di connessione. Questa connessione permanente consente di avviare, arrestare e riavviare il codice per quanto è necessario senza dover ristabilire una connessione.

Inoltre, Azure Dev Spaces offre un modo per replicare le variabili di ambiente e i file montati disponibili per i pod nel cluster AKS nel computer di sviluppo tramite il file *azds-local.env.* È inoltre possibile utilizzare questo file per creare nuove variabili di ambiente e supporti di volume.

## <a name="additional-configuration-with-azds-localenv"></a>Configurazione aggiuntiva con azds-local.env

Il file *azds-local.env* consente di replicare le variabili di ambiente e i file montati disponibili per i pod nel cluster AKS. È possibile specificare le seguenti azioni in un file *azds-local.env:*

* Scaricare un volume e impostare il percorso di tale volume come variabile di ambiente.
* Scaricare un singolo file o un set di file da un volume e montarlo nel computer di sviluppo.
* Rendere un servizio disponibile indipendentemente dal cluster a cui si è connessi.

Di seguito è riportato un esempio di file *azds-local.env:Here is an example azds-local.env* file:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Un file *azds-local.env* predefinito non viene creato automaticamente, pertanto è necessario creare manualmente il file nella radice del progetto.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

Quando si è connessi al cluster AKS, i log di diagnostica del cluster vengono registrati nella [directory temporanea][azds-tmp-dir]del computer di sviluppo. Utilizzando Visual Studio Code, è anche possibile usare il comando *Mostra informazioni di diagnostica* per stampare le variabili di ambiente correnti e le voci DNS dal cluster AKS.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a connettere il computer di sviluppo locale al cluster [AKS, vedere Connettere il computer di sviluppo a un cluster AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
