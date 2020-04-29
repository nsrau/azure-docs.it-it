---
title: Come configurare un cluster per Azure Dev Spaces funziona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Viene descritto come configurare un cluster di servizi Kubernetes di Azure per Azure Dev Spaces funziona
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241725"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Come configurare un cluster per Azure Dev Spaces funziona

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team in un cluster di Azure Kubernetes Service (AKS). Un modo consiste nell'abilitare Azure Dev Spaces nel cluster AKS, in modo da poter [eseguire i servizi direttamente nel cluster][how-it-works-up] e usare [altre funzionalità di rete e routing][how-it-works-routing]. Questo articolo descrive cosa accade quando si prepara il cluster e si Abilita Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Preparare il cluster AKS

Per preparare il cluster AKS per gli spazi di sviluppo, verificare che il cluster AKS si trovi in un'area [supportata da Azure Dev Spaces][supported-regions] e che sia in esecuzione Kubernetes 1.10.3 o versione successiva. Per abilitare Azure Dev Spaces nel cluster dalla portale di Azure, passare al cluster, fare clic su *spazi di sviluppo*, modificare *USA spazi di sviluppo* in *Sì*e fare clic su *Salva*. È anche possibile abilitare Azure Dev Spaces dall'interfaccia della riga `az aks use-dev-spaces`di comando di Azure eseguendo.

Per un esempio di configurazione di un cluster AKS per gli spazi di sviluppo, vedere la [Guida introduttiva allo sviluppo team][quickstart-team].

Quando Azure Dev Spaces è abilitato nel cluster AKS, viene installato il controller per il cluster. Il controller risiede all'esterno del cluster AKS. Guida il comportamento e la comunicazione tra gli strumenti lato client e il cluster AKS. Una volta abilitata, è possibile interagire con il controller usando gli strumenti lato client.

Il controller esegue le azioni seguenti:

* Gestisce la creazione e la selezione dello spazio di sviluppo.
* Installa il grafico Helm dell'applicazione e crea gli oggetti Kubernetes.
* Compila l'immagine del contenitore dell'applicazione.
* Distribuisce l'applicazione in AKS.
* Esegue le compilazioni incrementali e viene riavviato quando il codice sorgente viene modificato.
* Gestisce i log e le tracce HTTP.
* Invia stdout e stderr agli strumenti lato client.
* Configura il routing per le applicazioni in uno spazio, oltre che negli spazi padre e figlio.

Il controller è una risorsa di Azure separata all'esterno del cluster ed esegue le operazioni seguenti per le risorse del cluster:

* Crea o designa uno spazio dei nomi Kubernetes da usare come spazio di sviluppo.
* Rimuove qualsiasi spazio dei nomi Kubernetes denominato *azds*, se esistente, e ne crea uno nuovo.
* Distribuisce una configurazione webhook Kubernetes.
* Distribuisce un server di ammissione del webhook.

Usa la stessa entità servizio usata dal cluster AKS per effettuare chiamate al servizio ad altri componenti Azure Dev Spaces.

![Azure Dev Spaces preparare il cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Per usare Azure Dev Spaces, deve essere presente almeno uno spazio di sviluppo. Azure Dev Spaces usa gli spazi dei nomi Kubernetes all'interno del cluster AKS per gli spazi di sviluppo. Quando si installa un controller, viene richiesto di creare un nuovo spazio dei nomi Kubernetes o scegliere uno spazio dei nomi esistente da usare come primo spazio di sviluppo. Per impostazione predefinita, il controller consente di aggiornare lo spazio dei nomi Kubernetes *predefinito* esistente al primo spazio di sviluppo.

Quando uno spazio dei nomi viene designato come spazio di sviluppo, il controller aggiunge l'etichetta *azds.io/Space=true* a tale spazio dei nomi per identificarlo come uno spazio di sviluppo. Lo spazio di sviluppo iniziale creato o designato è selezionato per impostazione predefinita dopo aver preparato il cluster. Quando si seleziona uno spazio, questo viene usato da Azure Dev Spaces per la creazione di nuovi carichi di lavoro.

È possibile usare gli strumenti lato client per creare nuovi spazi di sviluppo e rimuovere gli spazi di sviluppo esistenti. A causa di una limitazione in Kubernetes, non è possibile rimuovere lo spazio di sviluppo *predefinito* . Il controller rimuove anche eventuali spazi dei nomi Kubernetes esistenti denominati *azds* per evitare `azds` conflitti con il comando utilizzato dagli strumenti lato client.

Il server di ammissione del webhook Kubernetes viene usato per inserire i pod con tre contenitori durante la distribuzione per la strumentazione: un contenitore devspaces-proxy, un contenitore devspaces-proxy-init e un contenitore devspaces-Build. **Tutti e tre questi contenitori vengono eseguiti con accesso alla radice nel cluster AKS.** Usano anche la stessa entità servizio utilizzata dal cluster AKS per effettuare chiamate al servizio ad altri componenti Azure Dev Spaces.

![Server di ammissione del webhook Azure Dev Spaces Kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Il contenitore devspaces-proxy è un contenitore sidecar che gestisce tutto il traffico TCP da e verso il contenitore dell'applicazione e consente di eseguire il routing. Il contenitore devspaces-Proxy reindirizza i messaggi HTTP se vengono usati determinati spazi. Ad esempio, può essere utile per indirizzare i messaggi HTTP tra le applicazioni negli spazi padre e figlio. Tutto il traffico non HTTP passa attraverso devspaces-proxy senza modifiche. Il contenitore devspaces-proxy registra anche tutti i messaggi HTTP in ingresso e in uscita e li invia agli strumenti lato client come tracce. Queste tracce possono quindi essere visualizzate dallo sviluppatore per esaminare il comportamento dell'applicazione.

Il contenitore devspaces-proxy-init è un [contenitore init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) che aggiunge regole di routing aggiuntive basate sulla gerarchia dello spazio al contenitore dell'applicazione. Aggiunge le regole di routing aggiornando il file */etc/resolv.conf* del contenitore dell'applicazione e la configurazione di iptables prima dell'avvio. Gli aggiornamenti di */etc/resolv.conf* consentono la risoluzione DNS dei servizi negli spazi padre. Gli aggiornamenti della configurazione di iptables assicurano che tutto il traffico TCP all'interno e all'esterno del contenitore dell'applicazione venga instradato tramite devspaces-proxy. Tutti gli aggiornamenti da devspaces-proxy-init si verificano oltre alle regole aggiunte da Kubernetes.

Il contenitore devspaces-Build è un contenitore init ed è installato il codice sorgente del progetto e il socket docker. Il codice sorgente del progetto e l'accesso a Docker consentono di compilare il contenitore dell'applicazione direttamente dal Pod.

> [!NOTE]
> Azure Dev Spaces usa lo stesso nodo per compilare il contenitore dell'applicazione ed eseguirlo. Di conseguenza, Azure Dev Spaces non necessita di un registro contenitori esterno per la compilazione e l'esecuzione dell'applicazione.

Il server di ammissione del webhook Kubernetes è in attesa di qualsiasi nuovo pod creato nel cluster AKS. Se il Pod viene distribuito in qualsiasi spazio dei nomi con l'etichetta *azds.io/Space=true* , inserisce tale Pod con i contenitori aggiuntivi. Il contenitore devspaces-Build viene inserito solo se il contenitore dell'applicazione viene eseguito con gli strumenti lato client.

Dopo aver preparato il cluster AKS, è possibile usare gli strumenti lato client per preparare ed eseguire il codice nello spazio di sviluppo.

## <a name="client-side-tooling"></a>Strumenti lato client

Gli strumenti lato client consentono all'utente di:
* Generare un Dockerfile, un grafico Helm e un file di configurazione Azure Dev Spaces per l'applicazione.
* Creare spazi di sviluppo padre e figlio.
* Informare il controller di compilare e avviare l'applicazione.

Mentre l'applicazione è in esecuzione, anche gli strumenti lato client:
* Riceve e visualizza stdout e stderr dall'applicazione in esecuzione in AKS.
* Usa la [portabilità diretta](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) per consentire l'accesso Web all'applicazione tramite http\/:/localhost.
* Connette un debugger all'applicazione in esecuzione in AKS.
* Sincronizza il codice sorgente allo spazio di sviluppo quando viene rilevata una modifica per le compilazioni incrementali, consentendo un'iterazione rapida.
* Consente di connettere il computer di sviluppo direttamente al cluster AKS.

È possibile usare gli strumenti lato client dalla riga di comando come parte del `azds` comando. È inoltre possibile utilizzare gli strumenti lato client con:

* Visual Studio Code usando l' [estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio con [Visual Studio Tools per Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo degli strumenti lato client per preparare ed eseguire il codice nello spazio di sviluppo, vedere la pagina [relativa alla preparazione di un progetto per Azure Dev Spaces funziona][how-it-works-prep].

Per iniziare a usare Azure Dev Spaces per lo sviluppo in team, vedere la pagina relativa [allo sviluppo di team in Azure Dev Spaces][quickstart-team] Guida introduttiva.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md