---
title: Sviluppo in team con Java e Visual Studio Code
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 400aec21d028011523fc5b441ed8ba77a1a46074
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325532"
---
# <a name="team-development-using-java-and-visual-studio-code-with-azure-dev-spaces"></a>Sviluppo in team con Java e Visual Studio Code con Azure Dev Spaces

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

    ![](media/common/microservices-challenges.png)

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
1. Selezionare lo spazio _dev_: `azds space select --name dev`. Quando viene chiesto di selezionare uno spazio dev padre, selezionare _\<none\>_ .
1. Passare alla directory _mywebapi_ ed eseguire: `azds up -d`
1. Passare alla directory _webfrontend_ ed eseguire: `azds up -d`
1. Eseguire `azds list-uris` per visualizzare l'endpoint pubblico per _webfrontend_

> [!TIP]
> La procedura precedente consente di impostare una baseline, ma si consiglia ai team di usare CI/CD per mantenere automaticamente aggiornata la baseline con il codice di commit.
>
> Consultare la [guida all'impostazione di CI/CD con Azure DevOps](how-to/setup-cicd.md) per creare un flusso di lavoro simile al diagramma seguente.
>
> ![Diagramma di CI/CD di esempio](media/common/ci-cd-complex.png)

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

![](media/common/ci-cd-space-setup.png)

Eseguire il comando `azds space list` per visualizzare un elenco di tutti gli spazi presenti nell'ambiente di sviluppo. La colonna _Selezionato_ indica quale spazio è attualmente selezionato (true/false). Nel caso specifico, lo spazio denominato _dev/scott_ è stato selezionato automaticamente al momento della creazione. È possibile selezionare un altro spazio in qualsiasi momento con il comando `azds space select`.

Provalo.

### <a name="make-a-code-change"></a>Effettuare una modifica del codice
Passare alla finestra di VS Code per `mywebapi` e apportare una modifica al codice per il metodo `String index()`in `src/main/java/com/ms/sample/mywebapi/Application.java`, ad esempio:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

### <a name="run-the-service"></a>Eseguire il servizio

Per eseguire il servizio, premere F5 oppure digitare `azds up` nella finestra del terminale. Il servizio verrà automaticamente eseguito nello spazio _dev/scott_ appena selezionato. Verificare se il servizio è in esecuzione nel proprio spazio eseguendo `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Notare che ora un'istanza di *mywebapi* è in esecuzione nello spazio _dev/scott_. La versione in esecuzione in _dev_ è ancora in esecuzione ma non è elencata.

Eseguire `azds list-uris` per elencare gli URL per lo spazio corrente.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

All'URL del punto di accesso pubblico per *webfrontend* è stato aggiunto il prefisso *scott.s*. Questo URL è univoco per lo spazio _dev/scott_. Il prefisso dell'URL indica al controller Ingress di instradare le richieste alla versione _dev/scott_ di un servizio. Quando una richiesta con questo URL viene gestita da Dev Spaces, il controller Ingress prova prima a instradarla al servizio *webfrontend* nello spazio _dev/scott_. Se il tentativo non riesce, la richiesta verrà instradata al servizio *webfrontend* nello spazio _dev_ come fallback. Si noti inoltre che è presente un URL di localhost per l'accesso al servizio tramite localhost usando la funzionalità *port-forward* di Kubernetes. Per altre informazioni sugli URL e sul routing in Azure Dev Spaces, vedere [Come funziona e come p configurato Azure Dev Spaces](how-dev-spaces-works.md).

![Routing dello spazio](media/common/Space-Routing.png)

Questa funzionalità incorporata di Azure Dev Spaces consente di testare codice in uno spazio condiviso senza richiedere a ogni sviluppatore ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede al codice dell'app di inoltrare le intestazioni di propagazione, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-in-a-space"></a>Test del codice in uno spazio
Per testare la nuova versione di *mywebapi* in combinazione con *webfrontend*, aprire il browser all'URL del punto di accesso pubblico per *webfrontend* e passare alla pagina delle informazioni. Dovrebbe essere visualizzato il nuovo messaggio.

A questo punto, rimuovere parte dell'URL "scott.s." e aggiornare il browser. Verrà visualizzato il comportamento precedente con la versione di *mywebapi* in esecuzione in _dev_.

Dopo aver impostato uno spazio _dev_ che contiene sempre le ultime modifiche, e presupponendo che l'applicazione sia progettata per trarre vantaggio dal routing basato su spazio di DevSpace descritto in questa sezione dell'esercitazione, dovrebbe risultare evidente quanto può essere utile usare Dev Spaces per testare le nuove funzionalità nel contesto dell'applicazione più grande. Invece di dover distribuire _tutti_ i servizi nello spazio privato, è possibile creare uno spazio privato che deriva da _dev_ e attivare solo i servizi effettivamente in uso. L'infrastruttura di routing di Dev Spaces gestirà il resto utilizzando tutti i servizi dello spazio privato che riesce a trovare, mentre per impostazione predefinita torna all'ultima versione in esecuzione nello spazio _dev_. E soprattutto _più_ sviluppatori possono sviluppare attivamente diversi servizi contemporaneamente nel proprio spazio senza interrompersi a vicenda.

### <a name="well-done"></a>Ecco fatto!
La Guida introduttiva è stata completata. Si è appreso come:

> [!div class="checklist"]
> * Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
> * Sviluppare codice in modo iterativo nei contenitori.
> * Sviluppare in modo indipendente due servizi distinti e utilizzare l'individuazione del servizio DNS di Kubernetes per effettuare una chiamata a un altro servizio.
> * Sviluppare e testare il codice in modo produttivo in un ambiente di team.
> * Stabilire una baseline di funzionalità usando Dev Spaces per facilitare il test delle modifiche isolate nel contesto di un'applicazione di microservizi più grande

Ora che si è preso visione di Azure Dev Spaces, [condividere lo spazio di sviluppo con un membro del team](how-to/share-dev-spaces.md) e iniziare a collaborare.

## <a name="clean-up"></a>Eseguire la pulizia
Per eliminare completamente un'istanza di Azure Dev Spaces in un cluster, inclusi tutti gli spazi di sviluppo e i servizi in esecuzione al suo interno, usare il comando `az aks remove-dev-spaces`. Tenere presente che questa operazione è irreversibile. È possibile aggiungere di nuovo il supporto per Azure Dev Spaces nel cluster, ma sarà come iniziare da zero. I vecchi spazi e servizi non verranno ripristinati.

L'esempio seguente elenca i controller Azure Dev Spaces nella sottoscrizione attiva e quindi elimina il controller Azure Dev Spaces associato al cluster servizio Azure Kubernetes "myservizio Azure Kubernetes" nel gruppo di risorse "myservizio Azure Kubernetes-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
