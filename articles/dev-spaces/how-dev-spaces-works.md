---
title: Funzionamento di Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi che alimentano gli spazi di sviluppo di AzureDescribes the processes that power Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234972"
---
# <a name="how-azure-dev-spaces-works"></a>Funzionamento di Azure Dev Spaces

Lo sviluppo di un'applicazione Kubernetes può essere difficile. Sono necessari i file di configurazione Docker e Kubernetes. È necessario capire come testare l'applicazione in locale e interagire con altri servizi dipendenti. Potrebbe essere necessario gestire lo sviluppo e il test di più servizi contemporaneamente e con un team di sviluppatori.

Azure Dev Spaces offre diversi modi per scorrere rapidamente ed eseguire il debug delle applicazioni Kubernetes e collaborare con il team. Questo articolo descrive cosa può fare Azure Dev Spaces e come funziona.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Eseguire rapidamente l'iterazione e il debug dell'applicazione Kubernetes

Azure Dev Spaces riduce lo sforzo di sviluppare, testare e iterare l'applicazione Kubernetes nel contesto del cluster AKS. Questa riduzione dello sforzo consente agli sviluppatori di concentrarsi sulla logica di business delle applicazioni e non configurare i servizi per l'esecuzione in Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Collegare il computer di sviluppo ad AKS

Con Azure Dev Spaces, è possibile connettere il computer di sviluppo al cluster AKS, consentendo di eseguire ed eseguire il debug di codice nel computer di sviluppo come se fosse in esecuzione nel cluster. Azure Dev Spaces reindirizza il traffico tra il cluster AKS connesso eseguendo un pod nel cluster che funge da agente remoto per reindirizzare il traffico tra il computer di sviluppo e il cluster. Questo reindirizzamento del traffico consente al codice nel computer di sviluppo e ai servizi in esecuzione nel cluster AKS di comunicare come se si trovano nello stesso cluster AKS. Per ulteriori informazioni sulla connessione del computer di sviluppo ad AKS, vedere Funzionamento della connessione del computer di [sviluppo al cluster AKS][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Eseguire il codice in AKSRun your code in AKS

Oltre a reindirizzare il traffico tra il computer di sviluppo e il cluster AKS, con Azure Dev Spaces è possibile configurare ed eseguire rapidamente il codice direttamente in AKS. Con Visual Studio, Visual Studio Code o l'interfaccia della riga di comando Di Azure Dev Spaces, gli spazi di Azure Dev caricheranno il codice nel cluster, quindi lo compilano ed eseguiranno. Gli spazi di sviluppo di Azure possono anche sincronizzare in modo intelligente le modifiche al codice e riavviare il servizio per riflettere le modifiche in base alle esigenze. Durante l'esecuzione del codice, i log di compilazione e le tracce HTTP vengono trasmessi al client in modo da poter monitorare lo stato di avanzamento e diagnosticare eventuali problemi. È anche possibile usare Azure Dev Spaces per connettere il debugger in Visual Studio e Visual Studio Code ai servizi Java, Node.js e .NET Core.You can also use Azure Dev Spaces, to attach the debugger in Visual Studio and Visual Studio Code to Java, Node.js, and .NET Core services. Per altre informazioni, vedere Funzionamento della preparazione di [un progetto per Azure Dev Spaces,][how-it-works-prep]Funzionamento dell'esecuzione del codice con Azure Dev [Spaces][how-it-works-up]e Funzionamento del debug remoto del codice con Azure [Dev Spaces][how-it-works-remote-debugging].

## <a name="team-development"></a>Sviluppo in team

Azure Dev Spaces consente ai team di lavorare in modo produttivo sulle proprie applicazioni nello stesso cluster AKS senza interrompere l'operazione.

### <a name="intelligent-routing-between-dev-spaces"></a>Routing intelligente tra gli spazi di sviluppo

Con Azure Dev Spaces, un team può condividere un singolo cluster AKS che esegue un'applicazione cloud-native e creare spazi di sviluppo isolati in cui il team può sviluppare, testare ed eseguire il debug senza interferire con gli altri spazi di sviluppo. Una versione di base dell'applicazione viene eseguita in uno spazio di sviluppo radice. I membri del team creano quindi spazi di sviluppo figlio indipendenti in base allo spazio radice per lo sviluppo, il test e il debug delle modifiche nell'applicazione. Tramite le funzionalità di routing in Dev Spaces, gli spazi di sviluppo figlio possono instradare le richieste tra i servizi in esecuzione nello spazio di sviluppo figlio e lo spazio di sviluppo padre. Questo routing consente agli sviluppatori di eseguire la propria versione di un servizio durante il riutilizzo dei servizi dipendenti dallo spazio padre. Ogni spazio figlio ha il proprio URL univoco, che può essere condiviso e accessibile da altri per la collaborazione. Per altre informazioni sul funzionamento del routing in Azure Dev Spaces, vedere [Funzionamento del routing con Azure Dev Spaces.][how-it-works-routing]

### <a name="live-testing-an-open-pull-request"></a>Test in tempo reale di una richiesta pull aperta

È anche possibile usare le azioni GitHub con azure Dev Spaces per testare le modifiche all'applicazione in una richiesta pull direttamente nel cluster prima dell'unione. Gli spazi di sviluppo di Azure possono distribuire automaticamente una versione di revisione dell'applicazione nel cluster, consentendo all'autore e ad altri membri del team di esaminare le modifiche nel contesto dell'intera applicazione. Utilizzando le funzionalità di routing di Azure Dev Spaces, questa versione di revisione dell'applicazione viene distribuita anche nel cluster senza influire su altri spazi di sviluppo. Tutte queste funzionalità consentono di approvare e unire con sicurezza le richieste pull. Per un esempio di azioni GitHub e spazi di Azure Dev, vedere [GitHub Actions & Azure Kubernetes Service][pr-flow].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a connettere il computer di sviluppo locale al cluster [AKS, vedere Connettere il computer di sviluppo a un cluster AKS][connect].

Per iniziare a usare Azure Dev Spaces per lo sviluppo in team, vedere la guida introduttiva allo sviluppo di team in Azure Dev Spaces.To get started using Azure Dev Spaces for team development, see the [team development in Azure Dev Spaces][quickstart-team] quickstart.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development