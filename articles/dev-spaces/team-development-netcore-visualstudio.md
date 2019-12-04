---
title: Sviluppo in team con .NET Core e Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 895d2edbb268eab9944909ecda7193ce945bbf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325542"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Sviluppo in team usando .NET Core e Visual Studio con Azure Dev Spaces

In questa esercitazione si apprenderà come un team di sviluppatori può collaborare contemporaneamente nello stesso cluster Kubernetes usando Dev Spaces.

## <a name="learn-about-team-development"></a>Informazioni sui team di sviluppo

Finora il codice dell'applicazione è stato eseguito come se si fosse l'unico sviluppatore a lavorare sull'app. In questa sezione, si apprenderà come Azure Dev Spaces semplifichi lo sviluppo in team:
* Consente a un team di sviluppatori di lavorare nello stesso ambiente, usando uno spazio di sviluppo condiviso o spazi di sviluppo distinti in base alle esigenze.
* Supporta ogni sviluppatore che itera sul proprio codice in isolamento senza il timore di interrompere gli altri.
* Permette di testare il codice end-to-end, prima di eseguirne il commit, senza dover creare simulazioni delle dipendenze.

### <a name="challenges-with-developing-microservices"></a>Problemi con lo sviluppo di microservizi
L'applicazione di esempio non è complessa al momento. Nello sviluppo nel mondo reale, tuttavia, le sfide emergono appena si aggiungono più servizi e il team di sviluppo cresce.

* Il computer di sviluppo potrebbe non disporre di risorse sufficienti per eseguire contemporaneamente tutti i servizi necessari.
* Alcuni servizi potrebbero dover essere raggiungibili pubblicamente. Ad esempio, per un servizio potrebbe essere necessario un endpoint che risponda a un webhook.
* Se si vuole eseguire un sottoinsieme dei servizi, è necessario conoscere la gerarchia completa delle dipendenze tra tutti i servizi. Determinarlo può essere difficile, soprattutto con l'aumentare del numero di servizi.
* Alcuni sviluppatori ricorrono alla simulazione di molte delle loro dipendenze di servizio. Questo approccio può essere utile, ma la gestione di quelle simulazioni può influire presto sui costi di sviluppo. Inoltre, con questo approccio l'ambiente di sviluppo è visto in modo diverso rispetto a quello di produzione, il che può generare l'insorgere di bug.
* Di conseguenza, l'esecuzione di qualsiasi tipo di test di integrazione diventa difficile. I test di integrazione possono avvenire in modo realistico solo dopo il commit, il che significa che i problemi verranno riscontrati più avanti nel ciclo di sviluppo.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Lavorare in uno spazio di sviluppo condiviso
Con Azure Dev Spaces è possibile configurare uno spazio di sviluppo *condiviso* in Azure. Ogni sviluppatore può concentrarsi solo sulla propria parte dell'applicazione e può sviluppare in modo iterativo *codice pre-commit* in uno spazio di sviluppo che contiene già tutti gli altri servizi e le risorse cloud da cui dipendono i propri scenari. Le dipendenze sono sempre aggiornate e gli sviluppatori lavorano in un modo che rispecchia la produzione.

### <a name="work-in-your-own-space"></a>Lavorare nello spazio personale
Quando si sviluppa codice per un servizio, prima che sia pronto per l'archiviazione, il codice spesso non è in buono stato. Sono ancora in corso attività iterative di modellazione, test e sperimentazione con le soluzioni. Azure Dev Spaces fornisce il concetto di uno **spazio**, che consente di lavorare in isolamento senza timore di interrompere i membri del team.

## <a name="use-dev-spaces-for-team-development"></a>Usare Dev Spaces per lo sviluppo in team
È possibile dimostrare queste idee con un esempio concreto, usando l'applicazione di esempio *webfrontend* -> *mywebapi*. Si immagini uno scenario in cui uno sviluppatore, Scott, deve apportare una modifica al servizio *mywebapi*, e *solo* a tale servizio. L'applicazione *webfrontend* non deve cambiare come parte dell'aggiornamento di Scott.

_Senza_ usare Dev Spaces, Scott avrebbe a disposizione diversi modi per sviluppare e testare l'aggiornamento, nessuno dei quali è ideale:
* Eseguire TUTTI i componenti in locale, il che richiede un computer di sviluppo più potente con Docker installato e possibilmente MiniKube.
* Eseguire TUTTI i componenti in uno spazio dei nomi isolato nel cluster Kubernetes. Dal momento che l'applicazione *webfrontend* non viene modificata, l'uso di uno spazio dei nomi isolato comporta uno spreco di risorse del cluster.
* Eseguire SOLO *mywebapi* ed eseguire chiamate REST manuali per il test. In questo caso non viene testato il flusso completo end-to-end.
* Aggiungere codice mirato allo sviluppo all'applicazione *webfrontend* per consentire allo sviluppatore di inviare richieste a un'istanza differente di *mywebapi*. L'aggiunta di questo codice complica il servizio *webfrontend*.

### <a name="set-up-your-baseline"></a>Impostare la baseline
Prima di tutto è necessario distribuire una baseline dei servizi. Questa distribuzione rappresenterà l'"ultima valida" per poter confrontare facilmente il comportamento del codice locale rispetto alla versione archiviata. Verrà quindi creato uno spazio figlio basato su questa baseline per poter testare le modifiche a *mywebapi* nel contesto dell'applicazione principale.

1. Clonare l'[applicazione di esempio Dev Spaces](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Estrarre il ramo remoto *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Chiudere qualsiasi sessione di debug/F5 per entrambi i servizi, ma tenere aperti i progetti nelle finestre di Visual Studio.
1. Passare alla finestra di Visual Studio con il progetto _mywebapi_.
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**.
1. Selezionare la scheda **Debug** a sinistra per visualizzare le impostazioni di Azure Dev Spaces.
1. Selezionare **Cambia** per creare lo spazio che verrà usato quando si spreme F5 o CTRL+F5 per il servizio.
1. Nell'elenco a discesa Spazio, selezionare **\<Crea nuovo spazio\>** .
1. Assicurarsi che lo spazio padre sia impostato su **\<nessuno\>** e immettere il nome dello spazio **dev**. Fare clic su OK.
1. Premere CTRL+F5 per eseguire _mywebapi_ senza il debugger collegato.
1. Passare alla finestra di Visual Studio con il progetto _webfrontend_ e premere CTRL+F5 per eseguire anche questo.

> [!Note]
> In alcuni casi è necessario aggiornare il browser dopo la visualizzazione iniziale della pagina Web dopo CTRL+F5.

> [!TIP]
> La procedura precedente consente di impostare una baseline, ma si consiglia ai team di usare CI/CD per mantenere automaticamente aggiornata la baseline con il codice di commit.
>
> Consultare la [guida all'impostazione di CI/CD con Azure DevOps](how-to/setup-cicd.md) per creare un flusso di lavoro simile al diagramma seguente.
>
> ![Diagramma di CI/CD di esempio](media/common/ci-cd-complex.png)

Chiunque apra l'URL pubblico e navighi all'app Web richiama il percorso del codice scritto in precedenza che viene eseguito in entrambi i servizi con lo spazio _dev_ predefinito. Si supponga di voler continuare a sviluppare *mywebapi*. Come è possibile farlo senza interrompere altri sviluppatori che usano lo spazio di sviluppo? A tale scopo, è necessario impostare il proprio spazio.

### <a name="create-a-new-dev-space"></a>Creare un nuovo spazio di sviluppo
Da Visual Studio, è possibile creare spazi aggiuntivi che verranno utilizzati quando si utilizza F5 o CTRL+F5 nel servizio. È possibile assegnare qualsiasi nome allo spazio con la massima flessibilità di significato (ad esempio _sprint4_ o _demo_).

Per creare un nuovo spazio, eseguire questa operazione:
1. Passare alla finestra di Visual Studio con il progetto *mywebapi*.
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**.
3. Selezionare la scheda **Debug** a sinistra per visualizzare le impostazioni di Azure Dev Spaces.
4. A questo punto, è possibile modificare o creare il cluster e/o lo spazio che sarà utilizzato quando si usa F5 o CTRL+F5. *Assicurarsi che sia selezionato lo spazio Azure Dev Spaces creato in precedenza*.
5. Nell'elenco a discesa Spazio, selezionare **\<Crea nuovo spazio\>** .

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Nella finestra di dialogo **Aggiungi spazio** impostare lo spazio padre su **dev** e immettere un nome per il nuovo spazio. È possibile utilizzare il proprio nome (ad esempio "scott") per il nuovo spazio in modo che ai colleghi sia chiaro quale spazio sia in uso dallo sviluppatore. Fare clic su **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Verrà ora visualizzato il cluster servizio Azure Kubernetes e il nuovo spazio selezionato nella pagina delle proprietà del progetto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aggiornare il codice per *mywebapi*

1. Nel progetto *mywebapi* apportare una modifica al codice per il metodo `string Get(int id)` nel file `Controllers/ValuesController.cs` come indicato di seguito:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Impostare un punto di interruzione in questo blocco di codice aggiornato (potrebbe essercene già uno impostato in precedenza).
3. Premere F5 per avviare il servizio _mywebapi_. Il servizio viene avviato nel cluster usando lo spazio selezionato, che in questo caso è _scott_.

Di seguito è riportato un diagramma che aiuta a capire il funzionamento dei diversi spazi. Il percorso viola mostra una richiesta tramite lo spazio _dev_, che è il percorso predefinito usato se non viene anteposto un altro spazio all'URL. Il percorso rosa indica una richiesta tramite lo spazio _dev/scott_.

![](media/common/Space-Routing.png)

Questa funzionalità integrata di Azure Dev Spaces consente di testare codice end-to-end in un ambiente condiviso senza richiedere a ogni sviluppatore di ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede l'inoltro delle intestazioni di propagazione nel codice dell'app, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-running-in-the-_devscott_-space"></a>Testare il codice in esecuzione nello spazio _dev/scott_
Per testare la nuova versione di *mywebapi* in combinazione con *webfrontend*, aprire il browser all'URL del punto di accesso pubblico per *webfrontend*, ad esempio http://dev.webfrontend.123456abcdef.eus.azds.io) e passare alla pagina delle informazioni. Verrà visualizzato il messaggio originale "Salve da webfrontend e Salve da mywebapi".

A questo punto, aggiungere "scott.s". all'URL in modo che diventi simile a http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io e aggiornare il browser. Si dovrebbe raggiungere il punto di interruzione impostato nel progetto *mywebapi*. Premere F5 per continuare e nel browser dovrebbe essere visualizzato il nuovo messaggio "Salve da webfrontend e mywebapi ora dice qualcosa di nuovo". Ciò è dovuto al fatto che il percorso del codice aggiornato in *mywebapi* è in esecuzione nello spazio _dev/scott_.

Dopo aver impostato uno spazio _dev_ che contiene sempre le ultime modifiche, e presupponendo che l'applicazione sia progettata per trarre vantaggio dal routing basato su spazio di DevSpace descritto in questa sezione dell'esercitazione, dovrebbe risultare evidente quanto può essere utile usare Dev Spaces per testare le nuove funzionalità nel contesto dell'applicazione più grande. Invece di dover distribuire _tutti_ i servizi nello spazio privato, è possibile creare uno spazio privato che deriva da _dev_ e attivare solo i servizi effettivamente in uso. L'infrastruttura di routing di Dev Spaces gestirà il resto utilizzando tutti i servizi dello spazio privato che riesce a trovare, mentre per impostazione predefinita torna all'ultima versione in esecuzione nello spazio _dev_. E soprattutto _più_ sviluppatori possono sviluppare attivamente diversi servizi contemporaneamente nel proprio spazio senza interrompersi a vicenda.

### <a name="well-done"></a>Ecco fatto!
La Guida introduttiva è stata completata. Si è appreso come:

> [!div class="checklist"]
> * Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
> * Sviluppare codice in modo iterativo nei contenitori.
> * Sviluppare in modo indipendente due servizi distinti e utilizzare l'individuazione del servizio DNS di Kubernetes per effettuare una chiamata a un altro servizio.
> * Sviluppare e testare il codice in modo produttivo in un ambiente di team.
> * Stabilire una baseline di funzionalità usando Dev Spaces per facilitare il test delle modifiche isolate nel contesto di un'applicazione di microservizi più grande

Ora che si è preso visione di Azure Dev Spaces, [condividere lo spazio di sviluppo con un membro del team](how-to/share-dev-spaces.md) per dimostrare come sia facile collaborare.

## <a name="clean-up"></a>Eseguire la pulizia
Per eliminare completamente un'istanza di Azure Dev Spaces in un cluster, inclusi tutti gli spazi di sviluppo e i servizi in esecuzione al suo interno, usare il comando `az aks remove-dev-spaces`. Tenere presente che questa operazione è irreversibile. È possibile aggiungere di nuovo il supporto per Azure Dev Spaces nel cluster, ma sarà come iniziare da zero. I vecchi spazi e servizi non verranno ripristinati.

L'esempio seguente elenca i controller Azure Dev Spaces nella sottoscrizione attiva e quindi elimina il controller Azure Dev Spaces associato al cluster servizio Azure Kubernetes "myservizio Azure Kubernetes" nel gruppo di risorse "myservizio Azure Kubernetes-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
