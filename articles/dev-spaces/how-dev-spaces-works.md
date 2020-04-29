---
title: Funzionamento di Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi che alimentano Azure Dev Spaces
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234972"
---
# <a name="how-azure-dev-spaces-works"></a>Funzionamento di Azure Dev Spaces

Lo sviluppo di un'applicazione Kubernetes può risultare complesso. Sono necessari i file di configurazione Docker e Kubernetes. È necessario capire come testare l'applicazione in locale e interagire con altri servizi dipendenti. Potrebbe essere necessario gestire lo sviluppo e il testing di più servizi contemporaneamente e con un team di sviluppatori.

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team. Questo articolo descrive le operazioni che Azure Dev Spaces possibile e il suo funzionamento.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Scorrere rapidamente ed eseguire il debug dell'applicazione Kubernetes

Azure Dev Spaces riduce lo sforzo necessario per sviluppare, testare ed eseguire l'iterazione dell'applicazione Kubernetes nel contesto del cluster AKS. Questa riduzione dello sforzo consente agli sviluppatori di concentrarsi sulla logica di business delle applicazioni e non sulla configurazione dei servizi per l'esecuzione in Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Connettere il computer di sviluppo ad AKS

Con Azure Dev Spaces, è possibile connettere il computer di sviluppo al cluster AKS, consentendo di eseguire ed eseguire il debug del codice nel computer di sviluppo come se fosse in esecuzione nel cluster. Azure Dev Spaces reindirizza il traffico tra il cluster AKS connesso eseguendo un pod nel cluster che funge da agente remoto per reindirizzare il traffico tra il computer di sviluppo e il cluster. Questo reindirizzamento del traffico consente il codice nel computer di sviluppo e i servizi in esecuzione nel cluster AKS per comunicare come se si trovassero nello stesso cluster AKS. Per ulteriori informazioni sulla connessione del computer di sviluppo ad AKS, vedere la pagina relativa alla [modalità di connessione del computer di sviluppo al cluster AKS][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Eseguire il codice in AKS

Oltre a reindirizzare il traffico tra il computer di sviluppo e il cluster AKS, con Azure Dev Spaces è possibile configurare ed eseguire rapidamente il codice direttamente in AKS. Con Visual Studio, Visual Studio Code o l'interfaccia della riga di comando di Azure Dev Spaces, Azure Dev Spaces caricherà il codice nel cluster, quindi lo creerà ed eseguirà. Azure Dev Spaces può anche sincronizzare in modo intelligente le modifiche al codice e riavviare il servizio per riflettere le modifiche in base alle esigenze. Durante l'esecuzione del codice, i log di compilazione e le tracce HTTP vengono trasmessi al client in modo da poter monitorare lo stato di avanzamento e diagnosticare eventuali problemi. È anche possibile usare Azure Dev Spaces per aggiungere il debugger in Visual Studio e Visual Studio Code ai servizi Java, node. js e .NET Core. Per ulteriori informazioni, vedere [come preparare un progetto per Azure Dev Spaces funziona][how-it-works-prep], [come eseguire il codice con Azure Dev Spaces funziona][how-it-works-up]e come eseguire il [debug remoto del codice con Azure Dev Spaces funziona][how-it-works-remote-debugging].

## <a name="team-development"></a>Sviluppo in team

Azure Dev Spaces consente ai team di lavorare in modo produttivo sulla propria applicazione nello stesso cluster AKS senza compromettere l'attività.

### <a name="intelligent-routing-between-dev-spaces"></a>Routing intelligente tra gli spazi di sviluppo

Con Azure Dev Spaces, un team può condividere un singolo cluster AKS che esegue un'applicazione nativa del cloud e creare spazi di sviluppo isolati in cui il team può sviluppare, testare ed eseguire il debug senza interferire con gli altri spazi di sviluppo. Una versione di base dell'applicazione viene eseguita in uno spazio di sviluppo radice. I membri del team creano quindi spazi di sviluppo figlio indipendenti in base allo spazio radice per lo sviluppo, il test e il debug delle modifiche all'applicazione. Tramite le funzionalità di routing negli spazi di sviluppo, gli spazi di sviluppo figlio possono instradare le richieste tra i servizi in esecuzione nello spazio di sviluppo figlio e lo spazio dev padre. Questo routing consente agli sviluppatori di eseguire la propria versione di un servizio mentre riutilizza i servizi dipendenti dallo spazio padre. Ogni spazio figlio dispone di un proprio URL univoco, che può essere condiviso e accessibile da altri utenti per la collaborazione. Per ulteriori informazioni sul funzionamento del routing in Azure Dev Spaces, vedere funzionamento del routing [con Azure Dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Test in tempo reale di una richiesta pull aperta

È anche possibile usare le azioni di GitHub con Azure Dev Spaces per testare le modifiche apportate all'applicazione in una richiesta pull direttamente nel cluster prima dell'Unione. Azure Dev Spaces possibile distribuire automaticamente una versione di verifica dell'applicazione nel cluster, consentendo all'autore e ad altri membri del team di rivedere le modifiche nel contesto dell'intera applicazione. Usando le funzionalità di routing di Azure Dev Spaces, anche questa versione di revisione dell'applicazione viene distribuita nel cluster senza alcun effetto sugli altri spazi di sviluppo. Tutte queste funzionalità consentono di approvare e unire in modo sicuro le richieste pull. Per vedere un esempio di azioni e Azure Dev Spaces di GitHub, vedere [azioni di github & servizio Azure Kubernetes][pr-flow].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a connettere il computer di sviluppo locale al cluster AKS, vedere [connettere il computer di sviluppo a un cluster AKS][connect].

Per iniziare a usare Azure Dev Spaces per lo sviluppo in team, vedere la pagina relativa [allo sviluppo di team in Azure Dev Spaces][quickstart-team] Guida introduttiva.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development