---
title: Uso di CI/CD con Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: 525e18cba48756e725cbc7d837c2352b0fec74fe
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280030"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Usare CI/CD con Azure Dev Spaces

Questo articolo illustra la configurazione di integrazione continua/distribuzione continua (CI/CD) nel servizio Azure Kubernetes con Dev Spaces abilitato. CI/CD nel servizio Azure Kubernetes consente di distribuire automaticamente gli aggiornamenti delle app ogni volta che viene eseguito il push di codice sottoposto a commit nel repository di origine. L'uso di CI/CD in combinazione con un cluster abilitato per Dev Spaces è utile perché consente di mantenere aggiornata una baseline dell'applicazione che il team può usare.

![Diagramma di CI/CD di esempio](../media/common/ci-cd-simple.png)

Anche se questo articolo usa Azure DevOps, gli stessi concetti si applicano a sistemi CI/CD come Jenkins, TeamCity e così via.

## <a name="prerequisites"></a>prerequisiti
* [Cluster del servizio Azure Kubernetes con Azure Dev Spaces abilitato](../get-started-netcore.md)
* [Interfaccia della riga di comando di Azure Dev Spaces installata](upgrade-tools.md)
* [Organizzazione di Azure DevOps con un progetto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registro Azure Container](../../container-registry/container-registry-get-started-azure-cli.md)
    * Dettagli sull'[account amministratore](../../container-registry/container-registry-authentication.md#admin-account) del Registro Azure Container disponibili
* [Autorizzare il cluster del servizio Azure Kubernetes a eseguire il pull dal Registro Azure Container](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Scaricare il codice di esempio
Per motivi di tempo, verrà creato un fork del repository GitHub del codice di esempio. Passare a https://github.com/Azure/dev-spaces e selezionare **Fork**. Al termine del processo di fork, **clonare** la versione del repository creata tramite fork in locale. Per impostazione predefinita, verrà estratto il ramo _principale_, ma per risparmiare tempo, sono state incluse alcune modifiche nel ramo _azds_updates_, che dovrebbe essere stato trasferito durante il fork. Il ramo _azds_updates_ contiene gli aggiornamenti che viene chiesto di eseguire manualmente nelle sezioni sulle esercitazioni di Dev Spaces, oltre ad alcuni file YAML e JSON già pronti per semplificare la distribuzione del sistema CI/CD. È possibile usare un comando come `git checkout -b azds_updates origin/azds_updates` per estrarre il ramo _azds_updates_ nel repository locale.

## <a name="dev-spaces-setup"></a>Configurazione di Dev Spaces
Creare un nuovo spazio denominato _dev_ usando il comando `azds space select`. Lo spazio _dev_ verrà usato dalla pipeline CI/CD per eseguire il push delle modifiche al codice. Verrà usato anche per creare _spazi figlio_ basati su _dev_.

```cmd
azds space select -n dev
```

Quando viene chiesto di selezionare uno spazio dev padre, selezionare _\<none\>_ .

Dopo aver creato lo spazio di sviluppo, è necessario determinare il suffisso host. Usare il comando `azds show-context` per visualizzare il suffisso host del controller di ingresso del Azure Dev Spaces.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

Nell'esempio precedente il suffisso host è _fedcba098.EUS.azds.io_. Questo valore viene usato in un secondo momento durante la creazione della definizione di versione.

Lo spazio _dev_ conterrà sempre lo stato più recente del repository, una baseline, in modo che gli sviluppatori possano creare _spazi figlio_ da _dev_ per testare le modifiche isolate nel contesto dell'app di dimensioni maggiori. Questo concetto viene discusso più dettagliatamente nelle esercitazioni su Dev Spaces.

## <a name="creating-the-build-definition"></a>Creazione della definizione di compilazione
Aprire il progetto team di Azure DevOps in un Web browser e passare alla sezione _Pipeline_. Fare prima clic sulla foto del profilo nell'angolo in alto a destra del sito di Azure DevOps, aprire il riquadro delle funzionalità in anteprima e disabilitare _Nuova esperienza di creazione pipeline YAML_:

![Apertura del riquadro delle funzionalità in anteprima](../media/common/preview-feature-open.png)

Opzione da disabilitare:

![Opzione Nuova esperienza di creazione pipeline YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> La funzionalità di anteprima _Nuova esperienza di creazione pipeline YAML_ di Azure DevOps è al momento in conflitto con la creazione di pipeline di compilazione predefinite. È necessario disabilitarla per il momento, per distribuire la pipeline di compilazione predefinita.

Nel ramo _azds_updates_ è stato incluso un semplice [schema YAML di Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) che definisce i passaggi di compilazione necessari per *mywebapi* e *webfrontend*.

A seconda del linguaggio scelto, lo schema YAML della pipeline è stato archiviato in un percorso simile a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Per creare una pipeline da questo file:
1. Nella pagina principale del progetto DevOps passare a pipeline > compilazioni.
1. Selezionare l'opzione per creare una **nuova** pipeline di compilazione.
1. Selezionare **GitHub** come origine, autorizzare con l'account github, se necessario, e selezionare il ramo _azds_updates_ dalla versione con fork del repository dell'applicazione di esempio _dev-Spaces_ .
1. Selezionare **configurazione come codice**o **YAML**come modello.
1. Viene visualizzata una pagina di configurazione per la pipeline di compilazione. Come indicato in precedenza, passare al percorso specifico della lingua per il **percorso del file YAML** usando il pulsante **..** .. Ad esempio `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Passare alla scheda **variabili** .
1. Aggiungere manualmente _dockerId_ come variabile, corrispondente al nome utente dell'[account amministratore del Registro Azure Container](../../container-registry/container-registry-authentication.md#admin-account) (indicato nei prerequisiti dell'articolo).
1. Aggiungere manualmente _dockerPassword_ come variabile, corrispondente alla password dell'[account amministratore del Registro Azure Container](../../container-registry/container-registry-authentication.md#admin-account). Assicurarsi di specificare _dockerPassword_ come segreto (selezionando l'icona a forma di lucchetto) per motivi di sicurezza.
1. Selezionare **salva & coda**.

A questo punto, è stata creata una soluzione CI che compilerà automaticamente *mywebapi* e *webfrontend* per tutti gli aggiornamenti di cui viene eseguito il push nel ramo _azds_updates_ del fork GitHub. È possibile verificare che le immagini Docker siano state spostate passando al portale di Azure, selezionando il Container Registry di Azure ed esplorando la scheda **repository** . Potrebbero essere necessari alcuni minuti prima che le immagini vengano compilate e visualizzate nel registro contenitori.

![Repository del Registro Azure Container](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Creazione della definizione di versione

1. Nella pagina principale del progetto di DevOps passare a Pipeline > Versioni
1. Se si usa un progetto di DevOps completamente nuovo che ancora non contiene una definizione di versione, è necessario prima di tutto creare una definizione di versione vuota prima di procedere. L'opzione di importazione non viene visualizzata nell'interfaccia utente se non esiste una definizione di versione.
1. A sinistra fare clic sul pulsante **+ nuovo** , quindi su **Importa una pipeline**.
1. Fare clic su **Sfoglia** e selezionare `samples/release.json` dal progetto.
1. Fare clic su **OK**. Si noti che nel riquadro Pipeline è stata caricata la pagina di modifica della definizione di versione. Si noti anche che sono presenti alcune icone di avviso rosse indicanti dettagli specifici del cluster che devono ancora essere configurati.
1. A sinistra del riquadro Pipeline fare clic sull'opzione **Add an artifact** (Aggiungi un elemento).
1. Nell'elenco a discesa **origine** selezionare la pipeline di compilazione creata in precedenza.
1. Per la **versione predefinita**, scegliere **più recente dal ramo predefinito della pipeline di compilazione con i tag**.
1. Lasciare i **tag** vuoti.
1. Impostare **Alias di origine** su `drop`. Il valore dell' **alias di origine** viene usato dalle attività di rilascio predefinite, quindi deve essere impostato.
1. Fare clic su **Aggiungi**.
1. Fare ora clic sull'icona del fulmine sull'origine dell'elemento `drop` appena creato, come illustrato sotto:

    ![Configurazione della distribuzione continua dell'elemento della versione](../media/common/release-artifact-cd-setup.png)
1. Abilitare il **trigger di distribuzione continua**.
1. Passare il puntatore sulla scheda **attività** accanto a **pipeline** e fare clic su _sviluppo_ per modificare le attività della fase di _sviluppo_ .
1. Verificare che **Azure Resource Manager** sia selezionato in **tipo di connessione.** verranno visualizzati i tre controlli elenco a discesa evidenziati in rosso: ![la definizione di versione](../media/common/release-setup-tasks.png)
1. Selezionare la sottoscrizione di Azure in uso con Azure Dev Spaces. Potrebbe anche essere necessario fare clic su **autorizza**.
1. Selezionare il gruppo di risorse e il cluster che si sta usando con Azure Dev Spaces.
1. Fare clic su **processo agente**.
1. Selezionare **Hosted Ubuntu 1604** nel **pool di agenti**.
1. Passare il puntatore del mouse sulle **attività** nella parte superiore, fare clic su _Prod_ per modificare le attività della fase _Prod_ .
1. Verificare che **Azure Resource Manager** sia selezionato in **tipo di connessione.** e selezionare la sottoscrizione di Azure, il gruppo di risorse e il cluster che si sta usando con Azure Dev Spaces.
1. Fare clic su **processo agente**.
1. Selezionare **Hosted Ubuntu 1604** nel **pool di agenti**.
1. Fare clic sulla scheda **variabili** per aggiornare le variabili per la versione.
1. Aggiornare il valore di **DevSpacesHostSuffix** da **UPDATE_ME** al suffisso host. Il suffisso host viene visualizzato quando è stato eseguito il comando `azds show-context` precedente.
1. Fare clic su **Salva** nell'angolo in alto a destra e quindi su **OK**.
1. Fare clic su **+ Versione** (accanto al pulsante Salva) e quindi su **Crea una versione**.
1. In **artefatti**verificare che sia selezionata l'ultima compilazione dalla pipeline di compilazione.
1. Fare clic su **Create**(Crea).

Inizierà ora un processo di rilascio automatizzato, che distribuirà i grafici *mywebapi* e *webfrontend* nel cluster di Kubernetes nello spazio principale _dev_. È possibile monitorare lo stato di avanzamento della versione sul portale Web di Azure DevOps:

1. Passare alla sezione **versioni** in **pipeline**.
1. Fare clic sulla pipeline di versione per l'applicazione di esempio.
1. Fare clic sul nome della versione più recente.
1. Passare il mouse su **dev** box in **fasi** e fare clic su **log**.

La versione viene eseguita al termine di tutte le attività.

> [!TIP]
> Se la versione ha esito negativo con un messaggio di errore simile a *AGGIORNAMENTO NON RIUSCITO: timeout in attesa della condizione*, provare a esaminare i pod nel cluster [usando il dashboard di Kubernetes](../../aks/kubernetes-dashboard.md). Se i Pod non vengono avviati con messaggi di errore come il *pull dell'immagine "azdsexample.azurecr.io/mywebapi:122": errore RPC: code = Unknown desc = errore risposta dal daemon: Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: Unauthorized: Authentication Required*, potrebbe essere che il cluster non è stato autorizzato a eseguire il pull dalla container Registry di Azure. Verificare di avere completato il prerequisito [Autorizzare il cluster del servizio Azure Kubernetes a eseguire il pull dal Registro Azure Container](../../aks/cluster-container-registry-integration.md).

È ora disponibile una pipeline CI/CD completamente automatizzata per il fork di GitHub delle app di esempio di Dev Spaces. Ogni volta che si eseguono il commit e il push del codice, la pipeline di compilazione compilerà ed eseguirà il push delle immagini *mywebapi* e *webfrontend* nell'istanza del Registro Azure Container personalizzata. La pipeline di versione distribuirà quindi il grafico Helm per ogni app nello spazio _dev_ nel cluster abilitato per Dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Accesso ai servizi _dev_
Dopo la distribuzione, la versione _dev_ di *webfrontend* è accessibile con un URL pubblico, ad esempio: `http://dev.webfrontend.fedcba098.eus.azds.io`. È possibile trovare questo URL eseguendo il comando `azds list-uri`: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Distribuzione nell'ambiente di produzione

Per alzare manualmente il livello una determinata versione a _prod_ usando il sistema CI/CD creato in questa esercitazione:
1. Passare alla sezione **versioni** in **pipeline**.
1. Fare clic sulla pipeline di versione per l'applicazione di esempio.
1. Fare clic sul nome della versione più recente.
1. Passare il mouse sulla casella **Prod** in **fasi** e fare clic su **Distribuisci**.
    ![alzare di livello alla produzione](../media/common/prod-promote.png)
1. Passare il puntatore del mouse sulla casella **Prod** in **fasi** e fare clic su **log**.

La versione viene eseguita al termine di tutte le attività.

La fase _Prod_ della pipeline di integrazione continua/recapito continuo usa un servizio di bilanciamento del carico anziché il controller di ingresso di spazi di sviluppo per fornire l'accesso ai servizi _Prod_ . I servizi distribuiti nella fase _Prod_ sono accessibili come indirizzi IP anziché come nomi DNS. In un ambiente di produzione è possibile scegliere di creare un controller di ingresso personalizzato per ospitare i servizi in base alla propria configurazione DNS.

Per determinare l'indirizzo IP del servizio WebFrontEnd, fare clic sul passaggio di **stampa IP pubblico del WebFrontEnd** per espandere l'output del log. Usare l'IP visualizzato nell'output del log per accedere all'applicazione **WebFrontEnd** .

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Strumentazione di Dev Spaces nell'ambiente di produzione
Anche se la strumentazione di Dev Spaces _non_ è stata progettata come parte nel normale funzionamento dell'applicazione, è consigliabile eseguire i carichi di lavoro di produzione in uno spazio dei nomi di Kubernetes non abilitato con Dev Spaces. L'uso di questo tipo di spazio dei nomi di Kubernetes prevede che si crei lo spazio dei nomi di produzione tramite l'interfaccia della riga di comando di `kubectl` o che si consenta al sistema CI/CD di crearlo durante la prima distribuzione di Helm. Se si _seleziona_ o si crea in altro modo uno spazio usando gli strumenti di Dev Spaces, la strumentazione di Dev Spaces verrà aggiunta a tale spazio dei nomi.

Ecco una struttura di spazi dei nomi di esempio che supporta lo sviluppo di funzionalità, l'ambiente "dev" _e_ la produzione in un singolo cluster Kubernetes:

![Struttura di spazi dei nomi di esempio](../media/common/cicd-namespaces.png)

> [!Tip]
> Se è già stato creato uno spazio `prod` e lo si vuole solo escludere dalla strumentazione di Dev Spaces (senza eliminarlo), è possibile eseguire il comando dell'interfaccia della riga di comando di Dev Spaces seguente:
>
> `azds space remove -n prod --no-delete`
>
> Dopo questa operazione, potrebbe essere necessario eliminare tutti i pod nello spazio dei nomi `prod` in modo che possano essere ricreati senza la strumentazione di Dev Spaces.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo in team tramite Azure Dev Spaces](../team-development-netcore.md)