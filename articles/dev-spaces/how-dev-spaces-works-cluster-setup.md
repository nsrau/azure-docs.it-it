---
title: Funzionamento della configurazione di un cluster per Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive il funzionamento della configurazione di un cluster di servizi Azure Kubernetes per Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241725"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Funzionamento della configurazione di un cluster per Azure Dev Spaces

Azure Dev Spaces offre diversi modi per scorrere rapidamente ed eseguire il debug delle applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes (AKS). Un modo consiste nell'abilitare Azure Dev Spaces nel cluster AKS in modo da poter [eseguire i servizi direttamente nel cluster][how-it-works-up] e usare funzionalità di rete e routing [aggiuntive.][how-it-works-routing] Questo articolo descrive cosa accade quando si prepara il cluster e si abilita Azure Dev Spaces.This article describes what happens when you prepare your cluster and enable Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Preparare il cluster AKSPrepare your AKS cluster

Per preparare il cluster AKS per Dev Spaces, verificare che il cluster AKS si trovi in un'area [supportata da Azure Dev Spaces][supported-regions] e che si stia eseguendo Kubernetes 1.10.3 o versioni successive. Per abilitare Spazi di sviluppo di Azure nel cluster dal portale di Azure, passare al cluster, fare clic su *Spazi dev*, modificare *Usa spazi dev* in *Sì*e fare clic su *Salva*. È anche possibile abilitare Spazi di `az aks use-dev-spaces`sviluppo di Azure dall'interfaccia della riga di comando di Azure eseguendo .

Per un esempio di configurazione di un cluster AKS per Dev Spaces, vedere la [guida introduttiva][quickstart-team]allo sviluppo del team.

Quando Azure Dev Spaces è abilitato nel cluster AKS, installa il controller per il cluster. Il controller si trova all'esterno del cluster AKS. Guida il comportamento e la comunicazione tra gli strumenti lato client e il cluster AKS. Una volta abilitato, è possibile interagire con il controller utilizzando gli strumenti lato client.

Il controller esegue le seguenti azioni:

* Gestisce la creazione e la selezione dello spazio di sviluppo.
* Installa il grafico Helm dell'applicazione e crea oggetti Kubernetes.
* Compila l'immagine del contenitore dell'applicazione.
* Distribuisce l'applicazione in AKS.
* Esegue compilazioni incrementali e viene riavviato quando il codice sorgente viene modificato.
* Gestisce i log e le tracce HTTP.
* Inoltra stdout e stderr agli strumenti lato client.
* Configura il routing per le applicazioni all'interno di uno spazio, nonché tra gli spazi padre e figlio.

Il controller è una risorsa di Azure separata all'esterno del cluster ed esegue le operazioni seguenti per le risorse del cluster:The controller is a separate Azure resource outside of your cluster and does the following to resources in your cluster:

* Crea o designa uno spazio dei nomi Kubernetes da utilizzare come spazio di sviluppo.
* Rimuove qualsiasi spazio dei nomi Kubernetes denominato *azds*, se esistente, e ne crea uno nuovo.
* Distribuisce una configurazione webhook Kubernetes.
* Distribuisce un server di ammissione webhook.

Usa la stessa entità servizio usata dal cluster AKS per effettuare chiamate di servizio ad altri componenti di Azure Dev Spaces.It uses the same service principal that your AKS cluster uses to make service calls to other Azure Dev Spaces components.

![Azure Dev Spaces prepara il cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Per usare Gli spazi di sviluppo di Azure, è necessario che sia presente almeno uno spazio di sviluppo. Gli spazi di sviluppo di Azure usano gli spazi dei nomi Kubernetes all'interno del cluster AKS per gli spazi di sviluppo. Quando viene installato un controller, viene richiesto di creare un nuovo spazio dei nomi Kubernetes o di scegliere uno spazio dei nomi esistente da utilizzare come primo spazio di sviluppo. Per impostazione predefinita, il controller offre di aggiornare lo spazio dei nomi Kubernetes *predefinito* esistente al primo spazio di sviluppo.

Quando uno spazio dei nomi è designato come spazio di sviluppo, il controller aggiunge l'etichetta *azds.io/space=true* a tale spazio dei nomi per identificarlo come spazio di sviluppo. Lo spazio di sviluppo iniziale creato o designato viene selezionato per impostazione predefinita dopo la preparazione del cluster. Quando viene selezionato uno spazio, viene usato da Azure Dev Spaces per la creazione di nuovi carichi di lavoro.

È possibile utilizzare gli strumenti lato client per creare nuovi spazi di sviluppo e rimuovere gli spazi di sviluppo esistenti. A causa di una limitazione in Kubernetes, lo spazio di sviluppo *predefinito* non può essere rimosso. Il controller rimuove anche tutti gli spazi dei nomi Kubernetes esistenti denominati *azds* per evitare conflitti con il `azds` comando utilizzato dagli strumenti lato client.

Il server di ammissione webhook Kubernetes viene utilizzato per inserire pod con tre contenitori durante la distribuzione per la strumentazione: un contenitore devspaces-proxy, un contenitore devspaces-proxy-init e un contenitore devspaces-build. **Tutti e tre questi contenitori vengono eseguiti con accesso root nel cluster AKS.** Usano anche la stessa entità servizio usata dal cluster AKS per effettuare chiamate di servizio ad altri componenti di Azure Dev Spaces.They also use the same service principal that your AKS cluster uses to make service calls to other Azure Dev Spaces components.

![Server di ammissione di Azure Dev Spaces Kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Il contenitore devspaces-proxy è un contenitore sidecar che gestisce tutto il traffico TCP in entrata e in uscita dal contenitore dell'applicazione e consente di eseguire il routing. Il contenitore devspaces-proxy reindirizza i messaggi HTTP se vengono utilizzati determinati spazi. Ad esempio, può aiutare a instradare i messaggi HTTP tra le applicazioni negli spazi padre e figlio. Tutto il traffico non HTTP passa attraverso devspaces-proxy non modificato. Il contenitore devspaces-proxy registra anche tutti i messaggi HTTP in ingresso e in uscita e li invia agli strumenti sul lato client come tracce. Queste tracce possono quindi essere visualizzate dallo sviluppatore per controllare il comportamento dell'applicazione.

Il contenitore devspaces-proxy-init è un [contenitore init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) che aggiunge regole di routing aggiuntive in base alla gerarchia di vani al contenitore dell'applicazione. Aggiunge regole di routing aggiornando il file */etc/resolv.conf* del contenitore dell'applicazione e la configurazione iptables prima dell'avvio. Gli aggiornamenti a */etc/resolv.conf* consentono la risoluzione DNS dei servizi negli spazi padre. Gli aggiornamenti di configurazione iptables garantiscono che tutto il traffico TCP in entrata e in uscita dal contenitore dell'applicazione venga instradato tramite devspaces-proxy. Tutti gli aggiornamenti da devspaces-proxy-init si verificano in aggiunta alle regole aggiunte da Kubernetes.

Il contenitore devspaces-build è un contenitore init e dispone del codice sorgente del progetto e del socket Docker montati. Il codice sorgente del progetto e l'accesso a Docker consentono al contenitore dell'applicazione di essere compilato direttamente dal contenitore.

> [!NOTE]
> Azure Dev Spaces usa lo stesso nodo per compilare il contenitore dell'applicazione ed eseguirlo. Di conseguenza, Azure Dev Spaces non necessita di un registro dei contenitori esterno per la compilazione e l'esecuzione dell'applicazione.

Il server di ammissione webhook Kubernetes è in ascolto di qualsiasi nuovo pod creato nel cluster AKS. Se il pod viene distribuito in qualsiasi spazio dei nomi con l'etichetta *azds.io/space=true,* inserisce tale pod con i contenitori aggiuntivi. Il contenitore devspaces-build viene inserito solo se il contenitore dell'applicazione viene eseguito utilizzando gli strumenti sul lato client.

Dopo aver preparato il cluster AKS, è possibile usare gli strumenti lato client per preparare ed eseguire il codice nello spazio di sviluppo.

## <a name="client-side-tooling"></a>Utensili lato client

Gli strumenti lato client consentono all'utente di:
* Generare un file di configurazione Dockerfile, Helm e Azure Dev Spaces per l'applicazione.
* Creare spazi di sviluppo padre e figlio.
* Indicare al controller di compilare e avviare l'applicazione.

Durante l'esecuzione dell'applicazione, anche gli strumenti sul lato client:While your application is running, the client-side tooling also:
* Riceve e visualizza stdout e stderr dall'applicazione in esecuzione in AKS.
* Utilizza [portforward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) per consentire l'accesso Web\/all'applicazione tramite http: /localhost.
* Connette un debugger all'applicazione in esecuzione in AKS.
* Sincronizza il codice sorgente nello spazio di sviluppo quando viene rilevata una modifica per le compilazioni incrementali, consentendo un'iterazione rapida.
* Consente di connettere il computer di sviluppo direttamente al cluster AKS.

È possibile utilizzare gli strumenti lato client dalla `azds` riga di comando come parte del comando. È inoltre possibile utilizzare gli strumenti lato client con:

* Codice di Visual Studio tramite [l'estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio con [Visual Studio Tools per Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso degli strumenti lato client per preparare ed eseguire il codice nello spazio di sviluppo, vedere Funzionamento della preparazione di un progetto per Azure Dev Spaces.To learn more about using the client-side tooling to prepare and run your code in your dev space, see [How preparing a project for Azure Dev Spaces works.][how-it-works-prep]

Per iniziare a usare Azure Dev Spaces per lo sviluppo in team, vedere la guida introduttiva allo sviluppo di team in Azure Dev Spaces.To get started using Azure Dev Spaces for team development, see the [team development in Azure Dev Spaces][quickstart-team] quickstart.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md