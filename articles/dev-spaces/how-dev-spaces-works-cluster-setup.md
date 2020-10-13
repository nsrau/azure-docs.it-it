---
title: Come funziona la configurazione di un cluster per Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Viene descritto come funziona la configurazione di un cluster del servizio Azure Kubernetes per Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972680"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Come funziona la configurazione di un cluster per Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes. Uno di questi modi consiste nell'abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes, in modo da poter [eseguire i servizi direttamente nel cluster][how-it-works-up] e usare [funzionalità di rete e routing aggiuntive][how-it-works-routing]. Questo articolo descrive cosa accade quando si prepara il cluster e si abilita Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Preparare il cluster del servizio Azure Kubernetes

Per preparare il cluster del servizio Azure Kubernetes per Dev Spaces, verificare che il cluster si trovi in un'area [supportata da Azure Dev Spaces][supported-regions] e che si usi Kubernetes 1.10.3 o versione successiva. È possibile abilitare Azure Dev Spaces dall'interfaccia della riga di comando di Azure eseguendo `az aks use-dev-spaces` .

Dopo essere stato abilitato nel cluster del servizio Azure Kubernetes, Azure Dev Spaces installa il controller per il cluster. Il controller è esterno al cluster del servizio Azure Kubernetes. Controlla il comportamento e la comunicazione tra gli strumenti lato client e il cluster. Dopo averlo abilitato, è possibile interagire con il controller usando gli strumenti lato client.

Il controller esegue le azioni seguenti:

* Gestisce la creazione e la selezione di spazi di Dev Spaces.
* Installa il grafico Helm dell'applicazione e crea gli oggetti Kubernetes.
* Compila l'immagine del contenitore dell'applicazione.
* Distribuisce l'applicazione nel servizio Azure Kubernetes.
* Esegue compilazioni incrementali e riavvii quando il codice sorgente viene modificato.
* Gestisce i log e le tracce HTTP.
* Inoltra stdout e stderr agli strumenti lato client.
* Configura il routing per le applicazioni all'interno di uno spazio e tra spazi padre e figlio.

Il controller è una risorsa di Azure separata esterna al cluster ed esegue le operazioni seguenti per le risorse del cluster:

* Crea o designa uno spazio dei nomi Kubernetes da usare come spazio di Dev Spaces.
* Rimuove qualsiasi spazio dei nomi Kubernetes denominato *azds*, se esistente, e ne crea uno nuovo.
* Distribuisce una configurazione webhook di Kubernetes.
* Distribuisce un server di ammissione webhook.

Usa la stessa entità servizio usata dal cluster del servizio Azure Kubernetes per effettuare chiamate di servizio ad altri componenti di Azure Dev Spaces.

![Preparazione del cluster in Azure Dev Spaces](media/how-dev-spaces-works/prepare-cluster.svg)

Per usare Azure Dev Spaces, deve essere presente almeno uno spazio di Dev Spaces. Azure Dev Spaces usa gli spazi dei nomi Kubernetes all'interno del cluster del servizio Azure Kubernetes per gli spazi di Dev Spaces. Quando si installa un controller, viene chiesto di creare un nuovo spazio dei nomi Kubernetes o di scegliere uno spazio dei nomi esistente da usare come primo spazio di Dev Spaces. Per impostazione predefinita, il controller offre di aggiornare lo spazio dei nomi *predefinito* Kubernetes al primo spazio di Dev Spaces.

Quando uno spazio dei nomi viene designato come spazio di Dev Spaces, il controller vi aggiunge l'etichetta *azds.io/space=true* per identificarlo come spazio di Dev Spaces. Lo spazio di Dev Spaces iniziale creato o designato è selezionato per impostazione predefinita dopo la preparazione del cluster. Quando viene selezionato uno spazio di Dev Spaces, Azure Dev Spaces lo usa per creare nuovi carichi di lavoro.

È possibile usare gli strumenti lato client per creare nuovi spazi di Dev Spaces e rimuovere spazi esistenti. A causa di una limitazione in Kubernetes, non è possibile rimuovere lo spazio di Dev Spaces *predefinito*. Il controller rimuove anche eventuali spazi dei nomi Kubernetes esistenti denominati *azds* per evitare conflitti con il comando `azds` usato dagli strumenti lato client.

Il server di ammissione webhook di Kubernetes viene usato per inserire nei pod tre contenitori durante la distribuzione per la strumentazione: un contenitore devspaces-proxy, un contenitore devspaces-proxy-init e un contenitore devspaces-build. **Tutti e tre i contenitori vengono eseguiti con accesso alla radice nel cluster del servizio Azure Kubernetes.** Usano inoltre la stessa entità servizio usata dal cluster del servizio Azure Kubernetes per effettuare chiamate di servizio ad altri componenti di Azure Dev Spaces.

![Server di ammissione webhook di Kubernetes in Azure Dev Spaces](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Il contenitore devspaces-proxy è un contenitore collaterale che gestisce tutto il traffico TCP in ingresso e in uscita dal contenitore dell'applicazione e consente di eseguire il routing. Il contenitore devspaces-proxy reindirizza i messaggi HTTP se determinati spazi sono in uso. Ad esempio, può essere utile per indirizzare i messaggi HTTP tra le applicazioni in spazi di Dev Spaces padre e figlio. Tutto il traffico non HTTP passa attraverso il contenitore devspaces-proxy senza modifiche. Il contenitore devspaces-proxy registra inoltre tutti i messaggi HTTP in ingresso e in uscita e li invia agli strumenti lato client come tracce. Queste tracce possono quindi essere visualizzate dallo sviluppatore per analizzare il comportamento dell'applicazione.

Il contenitore devspaces-proxy-init è un [contenitore di inizializzazione](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) che aggiunge altre regole di routing basate sulla gerarchia degli spazi al contenitore dell'applicazione. Le aggiunge aggiornando il file */etc/resolv.conf* del contenitore dell'applicazione e la configurazione di iptables prima dell'avvio. Gli aggiornamenti a */etc/resolv.conf* consentono la risoluzione DNS dei servizi negli spazi padre. Gli aggiornamenti alla configurazione di iptables assicurano che tutto il traffico TCP in ingresso e in uscita dal contenitore dell'applicazione venga instradato tramite devspaces-proxy. Tutti gli aggiornamenti da devspaces-proxy-init vengono eseguiti in aggiunta alle regole aggiunte da Kubernetes.

Il contenitore devspaces-build è un contenitore di inizializzazione in cui sono montati il codice sorgente del progetto e il socket di Docker. Il codice sorgente del progetto e l'accesso a Docker consentono di compilare il contenitore dell'applicazione direttamente dal pod.

> [!NOTE]
> Azure Dev Spaces usa lo stesso nodo per compilare il contenitore dell'applicazione ed eseguirlo. Di conseguenza, Azure Dev Spaces non necessita di un registro contenitori esterno per compilare ed eseguire l'applicazione.

Il server di ammissione webhook di Kubernetes rimane in ascolto di eventuali nuovi pod creati nel cluster del servizio Azure Kubernetes. Se il nuovo pod viene distribuito in uno spazio dei nomi con etichetta *azds.io/space=true*, il server inserisce contenitori aggiuntivi nel pod. Il contenitore devspaces-build viene inserito solo se il contenitore dell'applicazione viene eseguito con gli strumenti lato client.

Dopo aver preparato il cluster del servizio Azure Kubernetes, è possibile usare gli strumenti lato client per preparare ed eseguire il codice nello spazio di Dev Spaces.

## <a name="client-side-tooling"></a>Strumenti lato client

Gli strumenti lato client consentono all'utente di:
* Generare un Dockerfile, un grafico Helm e un file di configurazione di Azure Dev Spaces per l'applicazione.
* Creare spazi di Dev Spaces padre e figlio.
* Indicare al controller di compilare e avviare l'applicazione.

Inoltre, mentre l'applicazione è in esecuzione gli strumenti lato client:
* Ricevono e visualizzano stdout e stderr dall'applicazione in esecuzione nel servizio Azure Kubernetes.
* Usano [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) per consentire l'accesso Web all'applicazione tramite http:\//localhost.
* Collegano un debugger all'applicazione in esecuzione nel servizio Azure Kubernetes.
* Sincronizzano il codice sorgente con lo spazio di Dev Spaces quando viene rilevata una modifica per le compilazioni incrementali, consentendo un'iterazione rapida.
* Consentono di connettere il computer di sviluppo direttamente al cluster del servizio Azure Kubernetes.

È possibile usare gli strumenti lato client dalla riga di comando come parte del comando `azds`. Gli strumenti lato client possono anche essere usati con:

* Visual Studio Code con l'[estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio con il carico di lavoro Sviluppo di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso degli strumenti lato client per preparare ed eseguire il codice nello spazio di Dev Spaces, vedere [Come funziona la preparazione di un progetto per Azure Dev Spaces][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service