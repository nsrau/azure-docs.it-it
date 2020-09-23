---
title: Migrazione a Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 09/21/2020
ms.topic: conceptual
description: Descrive i processi che alimentano Azure Dev Spaces
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori, Bridge per Kubernetes
ms.openlocfilehash: b585ee20efb7b377a041152996ef41d8c59c539e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997169"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrazione a Bridge to Kubernetes

Bridge to Kubernetes offre un'alternativa più semplice a molti degli scenari di sviluppo che funzionano con Azure Dev Spaces. Bridge to Kubernetes è un'esperienza solo sul lato client che usa le estensioni in [Visual Studio][vs]   e [Visual Studio Code][vsc].  

Bridge to Kubernetes consente di migliorare l'esperienza di sviluppo consentendo a un'applicazione Kubernetes stabilita di includere un servizio in esecuzione nella workstation di sviluppo locale. A differenza degli spazi di sviluppo, Bridge to Kubernetes consente di ridurre le complessità dei cicli interni in base alla necessità di creare configurazioni Docker e Kubernetes, consentendo agli sviluppatori di concentrarsi esclusivamente sulla logica di business del codice del microservizio.

Bridge to Kubernetes consente di eseguire l'iterazione del codice in esecuzione nel computer di sviluppo, sfruttando al tempo stesso le dipendenze e la configurazione esistente dall'ambiente Kubernetes. Al contrario, Azure Dev Spaces distribuisce il microservizio nell'ambiente Kubernetes prima di poter eseguire il debug del servizio in modalità remota ed eseguire l'iterazione sul codice.

Questo articolo fornisce un confronto tra Azure Dev Spaces e Bridge per Kubernetes, nonché istruzioni per la migrazione da Azure Dev Spaces a Bridge a Kubernetes.

## <a name="development-approaches"></a>Approcci di sviluppo

![Approcci di sviluppo](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces aiutato gli sviluppatori Kubernetes a usare il codice in esecuzione direttamente nel cluster AKS, evitando la necessità di un ambiente locale non simile all'ambiente distribuito. Questo approccio ha migliorato alcuni aspetti dello sviluppo, ma ha anche introdotto un prerequisito per l'apprendimento e la gestione di concetti aggiuntivi quali Docker, Kubernetes e Helm prima di iniziare a usare Azure Dev Spaces.

Bridge to Kubernetes consente agli sviluppatori di lavorare direttamente nei propri computer di sviluppo interagendo con il resto del cluster. Questo approccio sfrutta la familiarità e la velocità di esecuzione del codice direttamente nei computer di sviluppo, condividendo al tempo stesso le dipendenze e l'ambiente forniti dal cluster. Questo approccio sfrutta anche la fedeltà e la scalabilità che deriva dall'esecuzione in Kubernetes.

## <a name="feature-comparison"></a>Confronto tra le funzionalità

Azure Dev Spaces e Bridge per Kubernetes hanno funzionalità simili, ma si differenziano anche per diverse aree:

| Requisito  | Azure Dev Spaces  | Bridge per Kubernetes  |
|---------------|-------------------|--------------------------------|
| Servizio Azure Kubernetes | In 15 aree di Azure | Qualsiasi area del servizio AKS    |
| **Sicurezza** |
| Accesso di sicurezza necessario per il cluster  | Collaboratore cluster AKS  | Kubernetes RBAC-aggiornamento della distribuzione   |
| Accesso di sicurezza necessario nel computer di sviluppo  | N/D  | Amministrazione locale/sudo   |
| **Usabilità** |
| Indipendente dagli elementi Kubernetes e Docker  | No  | Sì   |
| Rollback automatico delle modifiche, post-debug  | No  | Sì   |
| **Ambienti** |
| Funziona con Visual Studio 2019  | Sì  | Sì   |
| Funziona con Visual Studio Code  | Sì  | Sì   |
| Funziona con l'interfaccia della riga di comando  | Sì  | No   |
| **Compatibilità sistema operativo** |
| Funziona in Windows 10  | Sì  | Sì  |
| Funziona in Linux  | Sì  | Sì  |
| Funziona in macOS  | Sì  | Sì  |
| **Capabilities** |
| Isolamento dello sviluppatore o sviluppo di Team  | Sì  | Sì  |
| Sovrascrivere selettivamente le variabili di ambiente  | No  | Sì  |
| Creazione di Dockerfile e del grafico Helm  | Sì  | No  |
| Distribuzione persistente del codice in Kubernetes  | Sì  | No  |
| Debug remoto in un pod Kubernetes  | Sì  | No  |
| Debug locale, connesso a Kubernetes  | No  | Sì  |
| Debug di più servizi nello stesso momento, sulla stessa workstation  | Sì  | Sì  |

## <a name="kubernetes-inner-loop-development"></a>Sviluppo di cicli interni Kubernetes

La differenza principale tra Azure Dev Spaces e Bridge per Kubernetes è la posizione in cui viene eseguito il codice. Azure Dev Spaces aiuta a sviluppare ed eseguire il debug del codice del microservizio, ma richiede l'esecuzione del codice nel cluster. Bridge to Kubernetes consente di sviluppare ed eseguire il debug del codice del microservizio direttamente nel computer di sviluppo, sempre nel contesto dell'applicazione più grande in esecuzione in Kubernetes. Bridge to Kubernetes estende il perimetro del cluster Kubernetes e consente ai processi locali di ereditare la configurazione da Kubernetes.

![Sviluppo di cicli interni](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Quando si usa Bridge per Kubernetes, viene stabilita una connessione di rete tra il computer di sviluppo e il cluster.Per la durata della connessione viene aggiunto un proxy al cluster al posto della distribuzione di Kubernetes che reindirizza le richieste al servizio al computer di sviluppo. Quando si disconnette, la distribuzione dell'applicazione verrà ripristinata usando la versione originale della distribuzione in esecuzione nel cluster. Questo approccio è diverso Azure Dev Spaces da quello in cui il codice viene sincronizzato con il cluster, compilato, quindi eseguito. Azure Dev Spaces non esegue anche il rollback del codice.

Il Bridge per Kubernetes offre la flessibilità necessaria per lavorare con le applicazioni in esecuzione in Kubernetes, indipendentemente dal metodo di distribuzione. Se si usa CI/CD per compilare ed eseguire l'applicazione, indipendentemente dal fatto che si usino strumenti o script personalizzati, è comunque possibile usare Bridge per Kubernetes per sviluppare ed eseguire il debug del codice.

> [!TIP]
> L' [estensione Microsoft Kubernetes][kubernetes-extension] consente di sviluppare rapidamente manifesti Kubernetes con IntelliSense e aiuta i grafici Helm di impalcature.  

### <a name="use-visual-studio-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Usare Visual Studio per passare a Bridge to Kubernetes da Azure Dev Spaces

1. Aggiornare l'IDE di Visual Studio alla versione 16,7 o successiva e installare l'estensione Bridge per Kubernetes dalla [Visual Studio Marketplace][vs-marketplace].
1. Disabilitare il controller di Azure Dev Spaces usando il portale di Azure o l'interfaccia della riga di comando [Azure Dev Spaces][azds-delete].
1. Rimuovere il `azds.yaml` file dal progetto.
1. Ridistribuire l'applicazione.
1. Configurare Bridge per Kubernetes nell'applicazione distribuita. Per altre informazioni sull'uso di Bridge per Kubernetes in Visual Studio, vedere [usare Bridge per Kubernetes][use-btk-vs].
1. Avviare il debug in Visual Studio usando il Bridge appena creato per Kubernetes il profilo di debug.

### <a name="use-visual-studio-code-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Usare Visual Studio Code per eseguire la transizione da Bridge a Kubernetes da Azure Dev Spaces

1. Installare il [Bridge per l'estensione Kubernetes][vsc-marketplace].
1. Disabilitare il controller di Azure Dev Spaces usando il portale di Azure o l'interfaccia della riga di comando [Azure Dev Spaces][azds-delete].
1. Rimuovere il `azds.yaml` file dal progetto.
1. Ridistribuire l'applicazione.
1. Configurare Bridge per Kubernetes nell'applicazione distribuita. Per altre informazioni sull'uso di Bridge per Kubernetes in Visual Studio Code, vedere [usare Bridge per Kubernetes][use-btk-vsc].
1. Avviare il debug in Visual Studio Code usando il nuovo profilo di avvio Bridge to Kubernetes.

## <a name="team-development-in-a-shared-cluster"></a>Sviluppo di team in un cluster condiviso

È anche possibile usare il routing specifico per gli sviluppatori con Bridge per Kubernetes. Lo scenario di sviluppo del team Azure Dev Spaces usa più spazi dei nomi Kubernetes per isolare un servizio dal resto dell'applicazione usando il concetto di spazi dei nomi padre e figlio. Bridge to Kubernetes offre la stessa funzionalità, ma con caratteristiche di prestazioni migliorate e nello stesso spazio dei nomi dell'applicazione.

Sia Bridge a Kubernetes che Azure Dev Spaces richiedono che le intestazioni HTTP siano presenti e propagate nell'intera applicazione. Se l'applicazione è già stata configurata per gestire la propagazione dell'intestazione per Azure Dev Spaces, è necessario aggiornare l'intestazione. Per eseguire la transizione da Bridge a Kubernetes da Azure Dev Spaces, aggiornare l'intestazione configurata da *azds-route-As* a *Kubernetes-route-As*.

## <a name="evaluate-bridge-to-kubernetes"></a>Valutare Bridge per Kubernetes

Se si vuole provare a usare Bridge per Kubernetes prima di disabilitare Azure Dev Spaces nel cluster, il modo più semplice consiste nell'usare un nuovo cluster. Se si tenta di usare Azure Dev Spaces e Bridge per Kubernetes contemporaneamente nello stesso cluster, si verificano problemi quando si usano le funzionalità di routing in entrambi i casi.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Usare Visual Studio per valutare il Bridge per Kubernetes

1. Aggiornare l'IDE di Visual Studio alla versione 16,7 o successiva e installare l'estensione Bridge per Kubernetes dalla [Visual Studio Marketplace][vs-marketplace].
1. Creare un nuovo cluster AKS e distribuire l'applicazione. È anche possibile usare un' [applicazione di esempio][btk-sample-app].
1. Configurare Bridge per Kubernetes nell'applicazione distribuita. Per altre informazioni sull'uso di Bridge per Kubernetes in Visual Studio, vedere [usare Bridge per Kubernetes][use-btk-vs].
1. Avviare il debug in Visual Studio usando il Bridge appena creato per Kubernetes il profilo di debug.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Usare Visual Studio Code per valutare il Bridge per Kubernetes

1. Installare il [Bridge per l'estensione Kubernetes][vsc-marketplace].
1. Creare un nuovo cluster AKS e distribuire l'applicazione. È anche possibile usare un' [applicazione di esempio][btk-sample-app].
1. Configurare Bridge per Kubernetes nell'applicazione distribuita. Per altre informazioni sull'uso di Bridge per Kubernetes in Visual Studio Code, vedere [usare Bridge per Kubernetes][use-btk-vsc].
1. Avviare il debug in Visual Studio usando il nuovo profilo di avvio Bridge to Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Bridge to Kubernetes.

> [!div class="nextstepaction"]
> [Come funziona Bridge per Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/