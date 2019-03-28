---
title: File di inclusione
description: File di inclusione
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439531"
---
# <a name="team-development-with-azure-dev-spaces"></a>Team di sviluppo con Azure Dev Spaces

In questa esercitazione si apprenderà come un team di sviluppatori può collaborare contemporaneamente nello stesso cluster Kubernetes usando Dev Spaces.

## <a name="learn-about-team-development"></a>Informazioni sui team di sviluppo
Finora è stato eseguito il codice dell'applicazione come se si fosse l'unico sviluppatore a lavorare sull'app. In questa sezione, si apprenderà come Azure Dev Spaces semplifichi lo sviluppo in team:
* Consente a un team di sviluppatori di lavorare nello stesso ambiente, usando uno spazio di sviluppo condiviso o spazi di sviluppo distinti in base alle esigenze.
* Supporta ogni sviluppatore che itera sul proprio codice in isolamento senza il timore di interrompere gli altri.
* Permette di testare il codice end-to-end, prima di eseguirne il commit, senza dover creare simulazioni delle dipendenze.

### <a name="challenges-with-developing-microservices"></a>Problemi con lo sviluppo di microservizi
L'applicazione di esempio non è molto complessa al momento. Nello sviluppo nel mondo reale, tuttavia, le sfide emergono appena si aggiungono più servizi e il team di sviluppo cresce. Può diventare irrealistico eseguire tutto in locale per lo sviluppo.

* Il computer di sviluppo potrebbe non disporre di risorse sufficienti per eseguire contemporaneamente tutti i servizi necessari.
* Alcuni servizi potrebbero dover essere raggiungibili pubblicamente. Ad esempio, per un servizio potrebbe essere necessario un endpoint che risponda a un webhook.
* Se si vuole eseguire un subset dei servizi, è necessario conoscere la gerarchia completa delle dipendenze tra tutti i servizi. Determinarlo può essere difficile, soprattutto con l'aumentare del numero di servizi.
* Alcuni sviluppatori ricorrono alla simulazione di molte delle loro dipendenze di servizio. Questo approccio può essere, ma la gestione di quelle simulazioni può influire presto sui costi di sviluppo. Inoltre, con questo approccio l'ambiente di sviluppo è visto in modo molto diverso dalla produzione e possono insorgere bug.
* È quindi difficile eseguire qualsiasi tipo di test di integrazione. I test di integrazione possono avvenire in modo realistico solo dopo il commit, il che significa che i problemi verranno riscontrati più avanti nel ciclo di sviluppo.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Lavorare in uno spazio di sviluppo condiviso
Con Azure Dev Spaces è possibile configurare uno spazio di sviluppo *condiviso* in Azure. Ogni sviluppatore può concentrarsi solo sulla propria parte dell'applicazione e può sviluppare in modo iterativo *codice pre-commit* in uno spazio di sviluppo che contiene già tutti gli altri servizi e le risorse cloud da cui dipendono i propri scenari. Le dipendenze sono sempre aggiornate e gli sviluppatori lavorano in un modo che rispecchia la produzione.

### <a name="work-in-your-own-space"></a>Lavorare nello spazio personale
Quando si sviluppa codice per un servizio, prima che sia pronto per l'archiviazione, il codice spesso non è in buono stato. Sono ancora in corso attività iterative di modellazione, test e sperimentazione con le soluzioni. Azure Dev Spaces fornisce il concetto di uno **spazio**, che consente di lavorare in isolamento senza timore di interrompere i membri del team.

## <a name="use-dev-spaces-for-team-development"></a>Usare Dev Spaces per lo sviluppo in team
È possibile dimostrare queste idee con un esempio concreto, usando l'applicazione di esempio *webfrontend* -> *mywebapi*. Si immagini uno scenario in cui uno sviluppatore, Scott, deve apportare una modifica al servizio *mywebapi*, e *solo* a tale servizio. L'applicazione *webfrontend* non deve cambiare come parte dell'aggiornamento di Scott.

_Senza_ usare Dev Spaces, Scott avrebbe a disposizione alcuni modi per sviluppare e testare l'aggiornamento, nessuno dei quali è ideale:
* Eseguire TUTTI i componenti in locale. Ciò richiede un computer di sviluppo più potente con Docker installato e potenzialmente MiniKube.
* Eseguire TUTTI i componenti in uno spazio dei nomi isolato nel cluster Kubernetes. Dal momento che l'applicazione *webfrontend* non viene modificata, si tratta di uno spreco di risorse cluster.
* Eseguire SOLO *mywebapi* ed eseguire chiamate REST manuali per il test. Non viene testato il flusso end-to-end.
* Aggiungere codice mirato allo sviluppo all'applicazione *webfrontend* per consentire allo sviluppatore di inviare richieste a un'istanza differente di *mywebapi*. Ciò complica il servizio *webfrontend*.

### <a name="set-up-your-baseline"></a>Impostare la linea di base
Prima di tutto è necessario distribuire una baseline dei servizi. Questa distribuzione rappresenterà l'"ultima valida" per poter confrontare facilmente il comportamento del codice locale rispetto alla versione archiviata. Verrà quindi creato uno spazio figlio basato su questa baseline per poter testare le modifiche a *mywebapi* nel contesto dell'applicazione principale.

1. Clonare l'[applicazione di esempio Dev Spaces](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Estrarre il ramo remoto *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Selezionare lo spazio _dev_: `azds space select --name dev`. Quando viene chiesto di selezionare uno spazio dev padre, selezionare _\<none\>_.
1. Passare alla directory _mywebapi_ ed eseguire: `azds up -d`
1. Passare alla directory _webfrontend_ ed eseguire: `azds up -d`
1. Eseguire `azds list-uris` per visualizzare l'endpoint pubblico per _webfrontend_

> [!TIP]
> La procedura precedente consente di impostare una baseline, ma si consiglia ai team di usare CI/CD per mantenere automaticamente aggiornata la baseline con il codice di commit.
>
> Consultare la [guida all'impostazione di CI/CD con Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md) per creare un flusso di lavoro simile al diagramma seguente.
>
> ![Diagramma di CI/CD di esempio](../articles/dev-spaces/media/common/ci-cd-complex.png)

A questo punto, la baseline dovrebbe essere in esecuzione. Con il comando `azds list-up --all` si otterrà un output simile al seguente:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

La colonna DevSpace mostra che entrambi i servizi sono in esecuzione in uno spazio denominato _dev_. Chiunque apra l'URL pubblico e navighi verso l'app Web invocherà il percorso del codice archiviato che viene eseguito attraverso entrambi i servizi. Supponiamo ora di voler continuare a sviluppare _mywebapi_. Come si possono apportare modifiche al codice e testarle e non interrompere gli altri sviluppatori che usano l'ambiente di sviluppo? A tale scopo, è necessario impostare il proprio spazio.

### <a name="create-a-dev-space"></a>Creare uno spazio di sviluppo
Per eseguire la propria versione di _mywebapi_ in uno spazio diverso da _dev_ è possibile creare il proprio spazio usando il comando seguente:

```cmd
azds space select --name scott
```

Quando viene richiesto, selezionare _dev_ come **spazio di sviluppo padre**. Questo significa che il nuovo spazio _dev/scott_ deriverà dallo spazio _dev_. Più avanti si vedrà in che modo questo approccio può semplificare i test.

Proseguendo con l'ipotesi introduttiva, è stato usato il nome _scott_ per il nuovo spazio, in modo che i colleghi possano identificare chi ci sta lavorando. È comunque possibile assegnare qualsiasi nome allo spazio con la massima flessibilità di significato, ad esempio _sprint4_ o _demo_. In entrambi i casi _dev_ funge da baseline per tutti gli sviluppatori che lavorano su una parte di questa applicazione:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Eseguire il comando `azds space list` per visualizzare un elenco di tutti gli spazi presenti nell'ambiente di sviluppo. La colonna _Selezionato_ indica quale spazio è attualmente selezionato (true/false). Nel caso specifico, lo spazio denominato _dev/scott_ è stato selezionato automaticamente al momento della creazione. È possibile selezionare un altro spazio in qualsiasi momento con il comando `azds space select`.

Provalo.