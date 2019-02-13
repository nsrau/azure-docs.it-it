---
title: Uso di CI/CD con Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: article
manager: yuvalm
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: 0abe2902248c8203046cfe891d136ca7d5d0a75b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665972"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Usare CI/CD con Azure Dev Spaces

Questo articolo illustra la configurazione di integrazione continua/distribuzione continua (CI/CD) nel servizio Azure Kubernetes con Dev Spaces abilitato. CI/CD nel servizio Azure Kubernetes consente di distribuire automaticamente gli aggiornamenti delle app ogni volta che viene eseguito il push di codice sottoposto a commit nel repository di origine. L'uso di CI/CD in combinazione con un cluster abilitato per Dev Spaces è utile perché consente di mantenere aggiornata una baseline dell'applicazione che il team può usare.

![Diagramma di CI/CD di esempio](../media/common/ci-cd-simple.png)

Anche se questo articolo usa Azure DevOps, gli stessi concetti si applicano a sistemi CI/CD come Jenkins, TeamCity e così via.

## <a name="prerequisites"></a>Prerequisiti
* [Cluster del servizio Azure Kubernetes con Azure Dev Spaces abilitato](../get-started-netcore.md)
* [Interfaccia della riga di comando di Azure Dev Spaces installata](upgrade-tools.md)
* [Organizzazione di Azure DevOps con un progetto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registro Azure Container](../../container-registry/container-registry-get-started-azure-cli.md)
    * Dettagli sull'[account amministratore](../../container-registry/container-registry-authentication.md#admin-account) del Registro Azure Container disponibili
* [Autorizzare il cluster del servizio Azure Kubernetes a eseguire il pull dal Registro Azure Container](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Scaricare il codice di esempio
Per motivi di tempo, verrà creato un fork del repository GitHub del codice di esempio. Passare a https://github.com/Azure/dev-spaces e selezionare **Fork**. Al termine del processo di fork, **clonare** la versione del repository creata tramite fork in locale. Per impostazione predefinita, verrà estratto il ramo _principale_, ma per risparmiare tempo, sono state incluse alcune modifiche nel ramo _azds_updates_, che dovrebbe essere stato trasferito durante il fork. Il ramo _azds_updates_ contiene gli aggiornamenti che viene chiesto di eseguire manualmente nelle sezioni sulle esercitazioni di Dev Spaces, oltre ad alcuni file YAML e JSON già pronti per semplificare la distribuzione del sistema CI/CD. È possibile usare un comando come `git checkout -b azds_updates origin/azds_updates` per estrarre il ramo _azds_updates_ nel repository locale.

## <a name="dev-spaces-setup"></a>Configurazione di Dev Spaces
Creare un nuovo spazio denominato _dev_ usando il comando `azds space select`. Lo spazio _dev_ verrà usato dalla pipeline CI/CD per eseguire il push delle modifiche al codice. Verrà usato anche per creare _spazi figlio_ basati su _dev_.

```cmd
azds space select -n dev
```

Quando viene chiesto di selezionare uno spazio dev padre, selezionare _\<none\>_.

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
1. Nella pagina principale del progetto di DevOps passare a Pipeline > Compilazioni
1. Selezionare l'opzione per creare una **nuova** pipeline di compilazione
1. Selezionare **GitHub** come origine, eseguire l'autorizzazione con l'account GitHub se necessario e selezionare il ramo _azds_updates_ dalla versione creata tramite fork del repository di app di esempio dev-spaces
1. Selezionare **Configurazione come codice** o **YAML** come modello
1. Viene visualizzata una pagina di configurazione per la pipeline di compilazione. Come indicato in precedenza, immettere il percorso specifico del linguaggio per il **percorso del file YAML**. Ad esempio: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`
1. Passare alla scheda Variabili
1. Aggiungere manualmente _dockerId_ come variabile, corrispondente al nome utente dell'[account amministratore del Registro Azure Container](../../container-registry/container-registry-authentication.md#admin-account) (indicato nei prerequisiti dell'articolo).
1. Aggiungere manualmente _dockerPassword_ come variabile, corrispondente alla password dell'[account amministratore del Registro Azure Container](../../container-registry/container-registry-authentication.md#admin-account). Assicurarsi di specificare _dockerPassword_ come segreto (selezionando l'icona a forma di lucchetto) per motivi di sicurezza.
1. Selezionare **Salva e accoda**

A questo punto, è stata creata una soluzione CI che compilerà automaticamente *mywebapi* e *webfrontend* per tutti gli aggiornamenti di cui viene eseguito il push nel ramo _azds_updates_ del fork GitHub. Per verificare le immagini Docker di cui è stato eseguito il push, passare al portale di Azure, selezionare il Registro Azure Container ed esplorare la scheda _Repository_:

![Repository del Registro Azure Container](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Creazione della definizione di versione

1. Nella pagina principale del progetto di DevOps passare a Pipeline > Versioni
1. Se si usa un progetto di DevOps completamente nuovo che ancora non contiene una definizione di versione, è necessario prima di tutto creare una definizione di versione vuota prima di procedere. L'opzione di importazione non viene visualizzata nell'interfaccia utente se non esiste una definizione di versione.
1. A sinistra fare clic sul pulsante **+ Nuovo**, quindi fare clic su **Import a pipeline** (Importa una pipeline)
1. Selezionare un file JSON in `samples/release.json`
1. Fare clic su Ok. Si noti che nel riquadro Pipeline è stata caricata la pagina di modifica della definizione di versione. Si noti anche che sono presenti alcune icone di avviso rosse indicanti dettagli specifici del cluster che devono ancora essere configurati.
1. A sinistra del riquadro Pipeline fare clic sull'opzione **Add an artifact** (Aggiungi un elemento).
1. Nell'elenco a discesa **Origine** selezionare la pipeline di compilazione creata poco sopra in questo documento.
1. Per **Versione predefinita**, è consigliabile scegliere **Latest from the build pipeline default branch** (Più recente del ramo predefinito di pipeline di compilazione). Non è necessario specificare tag.
1. Impostare **Alias di origine** su `drop`. **Alias di origine** deve essere impostato perché viene usato dalle attività della versione predefinita.
1. Fare clic su **Aggiungi**.
1. Fare ora clic sull'icona del fulmine sull'origine dell'elemento `drop` appena creato, come illustrato sotto:

    ![Configurazione della distribuzione continua dell'elemento della versione](../media/common/release-artifact-cd-setup.png)
1. Abilitare il **trigger di distribuzione continua**
1. Passare ora al riquadro Attività. Dovrebbe essere selezionata la fase _dev_ e dovrebbero essere visualizzati tre controlli a discesa rossi, come di seguito:

    ![Configurazione della definizione di rilascio](../media/common/release-setup-tasks.png)
1. Specificare la sottoscrizione di Azure, il gruppo di risorse e il cluster usati con Azure Dev Spaces.
1. A questo punto deve essere presente solo un'altra sezione rossa, la sezione **Processo agente**. Passare a tale sezione e specificare **Hosted Ubuntu 1604** (Hosting Ubuntu 1604) come pool di agenti per questa fase.
1. Passare il puntatore sul selettore Attività nella parte superiore e selezionare _prod_.
1. Specificare la sottoscrizione di Azure, il gruppo di risorse e il cluster usati con Azure Dev Spaces.
1. In **Processo agente** configurare **Hosted Ubuntu 1604** (Hosting Ubuntu 1604) come pool di agenti.
1. Fare clic su **Salva** nell'angolo in alto a destra e quindi su **OK**.
1. Fare clic su **+ Versione** (accanto al pulsante Salva) e quindi su **Crea una versione**.
1. Verificare che nella sezione Artefatti sia selezionata la compilazione più recente della pipeline di compilazione e fare clic su **Crea**.

Inizierà ora un processo di rilascio automatizzato, che distribuirà i grafici *mywebapi* e *webfrontend* nel cluster di Kubernetes nello spazio principale _dev_. È possibile monitorare lo stato della versione nel portale Web di Azure DevOps.

> [!TIP]
> Se la versione ha esito negativo con un messaggio di errore simile a *AGGIORNAMENTO NON RIUSCITO: timeout in attesa della condizione*, provare a esaminare i pod nel cluster [usando il dashboard di Kubernetes](../../aks/kubernetes-dashboard.md). Se si osserva che non è possibile avviare i pod con messaggi di errore simili a *Non è stato possibile eseguire il pull dell'immagine "azdsexample.azurecr.io/mywebapi:122": errore RPC: codice = Sconosciuto descrizione = Risposta di errore dal daemon: Ottenere https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: autorizzazione mancante: autenticazione richiesta*, può essere dovuto al fatto che il cluster non è stato autorizzato a eseguire il pull dal Registro Azure Container. Verificare di avere completato il prerequisito [Autorizzare il cluster del servizio Azure Kubernetes a eseguire il pull dal Registro Azure Container](../../container-registry/container-registry-auth-aks.md).

È ora disponibile una pipeline CI/CD completamente automatizzata per il fork di GitHub delle app di esempio di Dev Spaces. Ogni volta che si eseguono il commit e il push del codice, la pipeline di compilazione compilerà ed eseguirà il push delle immagini *mywebapi* e *webfrontend* nell'istanza del Registro Azure Container personalizzata. La pipeline di versione distribuirà quindi il grafico Helm per ogni app nello spazio _dev_ nel cluster abilitato per Dev Spaces.

## <a name="accessing-your-dev-services"></a>Accesso ai servizi _dev_
Dopo la distribuzione, la versione _dev_ di *webfrontend* è accessibile con un URL pubblico, ad esempio: `http://dev.webfrontend.<hash>.<region>.aksapp.io`.

È possibile trovare questo URL usando l'interfaccia della riga di comando *kubectl*:
```cmd
kubectl get ingress -n dev webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="deploying-to-production"></a>Distribuzione nell'ambiente di produzione
Fare clic su **Modifica** nella definizione di versione. Come si può notare, è stata definita una fase _prod_:

![Fase di produzione](../media/common/prod-env.png)

Per alzare manualmente il livello una determinata versione a _prod_ usando il sistema CI/CD creato in questa esercitazione:
1. Aprire una versione completata in precedenza nel portale di DevOps
1. Passare il puntatore sulla fase "prod"
1. Selezionare Distribuisci

![Alzare il livello alla produzione](../media/common/prod-promote.png)

L'esempio di pipeline CI/CD usa le variabili per modificare il prefisso DNS per *webfrontend* a seconda dell'ambiente da distribuire. Per accedere ai servizi "prod", è quindi possibile usare un URL come: `http://prod.webfrontend.<hash>.<region>.aksapp.io`.

Dopo la distribuzione, è possibile trovare questo URL usando l'interfaccia della riga di comando *kubectl*: <!-- TODO update below to use list-uris when the product has been updated to list non-azds ingresses #769297 -->

```cmd
kubectl get ingress -n prod webfrontend -o=jsonpath="{.spec.rules[0].host}"
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