---
title: Distribuire le configurazioni con GitOps nel cluster Kubernetes abilitato per Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare GitOps per la configurazione di cluster abilitata per Azure Arc (anteprima)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 142c131f0382eb887d51185db920511ccf4eb735
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541629"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuire le configurazioni con GitOps nel cluster Kubernetes abilitato per Arc (anteprima)

GitOps è la pratica della dichiarazione dello stato desiderato della configurazione Kubernetes (distribuzioni, spazi dei nomi e così via) in un repository git seguito da una distribuzione basata su polling e pull di queste configurazioni al cluster tramite un operatore. Questo documento illustra la configurazione di tali flussi di lavoro nei cluster Kubernetes abilitati per Azure Arc.

La connessione tra il cluster e uno o più repository Git viene rilevata in Azure Resource Manager come risorsa di estensione di `sourceControlConfiguration`. Le proprietà della risorsa `sourceControlConfiguration` rappresentano la posizione e la modalità di flusso delle risorse Kubernetes da Git al cluster. I `sourceControlConfiguration` dati vengono archiviati crittografati inattivi in un database di Azure Cosmos DB per garantire la riservatezza dei dati.

Il `config-agent` in esecuzione nel cluster è responsabile dell'osservazione delle risorse di estensione nuove o aggiornate `sourceControlConfiguration` nella risorsa Kubernetes abilitata per Azure Arc, nella distribuzione di un operatore Flux per il controllo del repository git e nella propagazione di tutti gli aggiornamenti apportati a `sourceControlConfiguration` . È anche possibile creare più `sourceControlConfiguration` risorse con ambito nello `namespace` stesso cluster Kubernetes abilitato per Azure Arc per ottenere il multi-tenant. In tal caso, ogni operatore può distribuire le configurazioni solo nel rispettivo spazio dei nomi.

Il repository Git può contenere qualsiasi risorsa Kubernetes valida, tra cui spazi dei nomi, distribuzioni, elementi ConfigMap e DaemonSet e così via.  Esso può anche contenere grafici Helm per la distribuzione di applicazioni. Un set di scenari comune prevede la definizione di una configurazione di base per l'organizzazione, che può includere ruoli e associazioni RBAC comuni, agenti di monitoraggio o registrazione o servizi a livello di cluster.

Lo stesso modello può essere usato per gestire una raccolta di cluster più ampia, che può essere distribuita in ambienti eterogenei. Ad esempio, è possibile disporre di un unico repository che definisce la configurazione di base per l'organizzazione e applicarlo a decine di cluster Kubernetes contemporaneamente. [Criteri di Azure consente di automatizzare](use-azure-policy.md) la creazione di un oggetto `sourceControlConfiguration` con un set specifico di parametri in tutte le risorse Kubernetes abilitate per Azure Arc in un ambito (sottoscrizione o gruppo di risorse).

Questa guida introduttiva illustra come applicare un set di configurazioni con ambito amministrazione del cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia già disponibile un cluster connesso Kubernetes con abilitazione di Azure Arc. Se è necessario un cluster connesso, vedere l'[avvio rapido di connessione di un cluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Creare una configurazione

Il [repository di esempio](https://github.com/Azure/arc-k8s-demo) usato in questo documento è strutturato intorno all'utente di un operatore cluster che desidera effettuare il provisioning di alcuni spazi dei nomi, distribuire un carico di lavoro comune e fornire una configurazione specifica del team. L'uso di questo repository crea nel cluster le risorse seguenti:

**Spazi dei nomi:** `cluster-config`, `team-a`, `team-b`
**Distribuzione:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

Il esegue il `config-agent` polling di Azure per la nuova o l'aggiornamento `sourceControlConfiguration` ogni 30 secondi, ovvero il tempo massimo impiegato da `config-agent` per selezionare una configurazione nuova o aggiornata.
Se si associa un repository privato con `sourceControlConfiguration` , assicurarsi di completare anche i passaggi in [applicare la configurazione da un repository git privato](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Usando l'estensione dell'interfaccia della riga di comando di Azure per `k8sconfiguration` , collegare il cluster connesso a un [repository git di esempio](https://github.com/Azure/arc-k8s-demo). A questa configurazione verrà assegnato un nome `cluster-config`; indicare all'agente di distribuire l'operatore nello spazio dei nomi `cluster-config` e concedere all'operatore autorizzazioni `cluster-admin`.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>Parametro repository-URL

Di seguito sono riportati gli scenari supportati per il valore del parametro --repository-URL.

| Scenario | Format | Descrizione |
| ------------- | ------------- | ------------- |
| Repository GitHub privato - SSH | git@github.com:username/repo | KeyPair SSH generato da Flux.  L'utente deve aggiungere la chiave pubblica all'account GitHub come chiave di distribuzione. |
| Repository GitHub pubblico | `http://github.com/username/repo` o git://github.com/username/repo   | Repository Git pubblico  |

Questi scenari sono supportati da Flux ma non ancora da sourceControlConfiguration. 

| Scenario | Format | Descrizione |
| ------------- | ------------- | ------------- |
| Repository GitHub privato - HTTPS | `https://github.com/username/repo` | Flux non genera alcun KeyPair SSH.  [Istruzioni](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| Host Git privato | user@githost:path/to/repo | [Istruzioni](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| Repository GitHub privato - SSH (Bring Your Own Key) | git@github.com:username/repo | [Usare il proprio KeyPair SSH](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>Parametri aggiuntivi

Per personalizzare la creazione della configurazione, di seguito sono riportati alcuni parametri aggiuntivi:

`--enable-helm-operator` : *Facoltativo*: attivarlo per abilitare il supporto per le distribuzioni del grafico Helm.

`--helm-operator-chart-values` : *Facoltativo*: valori del grafico per l'operatore Helm (se abilitato).  Ad esempio, '--set helm.versions=v3'.

`--helm-operator-chart-version` : *Facoltativo*: versione del grafico per l'operatore Helm (se abilitato). Predefinito: "0.6.0".

`--operator-namespace` : *Facoltativo*: nome per lo spazio dei nomi dell'operatore. Impostazione predefinita: "default"

`--operator-params` : *Facoltativo*: parametri per l'operatore. Deve essere specificato tra virgolette singole. Ad esempio, usare ```--operator-params='--git-readonly --git-path=releases' ```

Opzioni supportate in --operator-params

| Opzione | Descrizione |
| ------------- | ------------- |
| --git-branch  | Ramo del repository Git da usare per i manifesti Kubernetes. Il valore predefinito è "master". |
| --git-path  | Percorso relativo all'interno del repository Git per permettere a Flux di individuare i manifesti Kubernetes. |
| --git-readonly | Il repository Git verrà considerato di sola lettura. Flux non tenterà di scrivervi. |
| --manifest-generation  | Se abilitato, Flux cercherà solo .flux.yaml ed eseguirà Kustomize o altri generatori di manifesti. |
| --git-poll-interval  | Periodo in cui eseguire il polling del repository Git per i nuovi commit. Il valore predefinito è "5m" (5 minuti). |
| --sync-garbage-collection  | Se abilitato, Flux eliminerà le risorse create, che tuttavia non saranno più presenti in Git. |
| --git-label  | Etichetta per tenere traccia dello stato di avanzamento della sincronizzazione, usata per contrassegnare il ramo Git.  Il valore predefinito è "flux-sync". |
| --git-user  | Nome utente per il commit Git. |
| --git-email  | Indirizzo di posta elettronica da usare per il commit Git. |

* Se "--git-user" o "--git-email" non sono impostati (il che significa che non si vuole che Flux scriva nel repository), --git-readonly verrà impostato automaticamente (se non è già stato impostato).

* Se enableHelmOperator è true, le stringhe operatorInstanceName e operatorNamespace non potranno superare i 47 caratteri combinati.  Se non si riesce a rispettare questo limite, si verificherà l'errore seguente:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Per ulteriori informazioni, vedere la [documentazione di Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> È possibile creare un sourceControlConfiguration anche nella portale di Azure nella scheda **configurazioni** del pannello della risorsa Kubernetes abilitata per Azure Arc.

## <a name="validate-the-sourcecontrolconfiguration"></a>Convalidare sourceControlConfiguration

Usando l'interfaccia della riga di comando di Azure, verificare che `sourceControlConfiguration` sia stato creato correttamente.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Si noti che la risorsa `sourceControlConfiguration` viene aggiornata con lo stato di conformità, i messaggi e le informazioni di debug.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Quando viene creato `sourceControlConfiguration`, si verificano alcuni aspetti dietro le quinte:

1. `config-agent` di Azure Arc monitora Azure Resource Manager per le configurazioni nuove o aggiornate (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`)
1. `config-agent` rileva la nuova configurazione `Pending`
1. `config-agent` legge le proprietà di configurazione e prepara la distribuzione di un'istanza gestita di `flux`
    * `config-agent` crea lo spazio dei nomi di destinazione
    * `config-agent` prepara un account del servizio Kubernetes con l'autorizzazione appropriata (ambito `cluster` o `namespace`)
    * `config-agent` distribuisce un'istanza di `flux`
    * `flux` genera una chiave SSH e registra la chiave pubblica
1. `config-agent` segnala lo stato a `sourceControlConfiguration`

Durante il processo di provisioning, `sourceControlConfiguration` scorrerà alcune modifiche di stato. Monitorare lo stato di avanzamento con il comando `az k8sconfiguration show ...` precedente:

1. `complianceStatus` -> `Pending`: rappresenta gli stati iniziali e in corso
1. `complianceStatus` -> `Installed`: `config-agent` è stato in grado di configurare correttamente il cluster e distribuire `flux` senza errori
1. `complianceStatus` -> `Failed`: `config-agent` ha riscontrato un errore durante la distribuzione di `flux`; i dettagli sono disponibili nel corpo della risposta `complianceStatus.message`

## <a name="apply-configuration-from-a-private-git-repository"></a>Applicare la configurazione da un repository Git privato

Se si usa un repository git privato, è necessario eseguire un'altra attività per chiudere il ciclo: aggiungere la chiave pubblica generata da `flux` come **chiave deploy** nel repository.

**Ottenere la chiave pubblica usando l'interfaccia della riga di comando di Azure**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Ottenere la chiave pubblica dal portale di Azure**

1. Nel portale di Azure, passare alla risorsa cluster connessa.
2. Nella pagina delle risorse, selezionare "Configurazioni" e visualizzare l'elenco delle configurazioni per il cluster.
3. Selezionare la configurazione usata dal repository Git privato.
4. Nella parte inferiore della finestra di contesto visualizzata, copiare la **chiave pubblica del repository**.

**Aggiungere la chiave pubblica come chiave di distribuzione al repository Git**

1. Aprire GitHub, passare al fork, quindi a **Impostazioni** e a **Deploy keys** (Distribuisci chiavi)
2. Fare clic su **Add deploy key** (Aggiungi chiave di distribuzione)
3. Fornire un titolo
4. Spuntare **Allow write access** (Consenti accesso in scrittura)
5. Incollare la chiave pubblica (escluse le virgolette circostanti)
6. Fare clic su **Aggiungi chiave**

Per altre informazioni su come gestire queste chiavi, vedere la documentazione di GitHub.

**Se si usa un repository Azure DevOps, aggiungere la chiave alle chiavi SSH**

1. In **Impostazioni utente** in alto a destra, accanto all'immagine del profilo, fare clic su **Chiavi pubbliche SSH**
1. Selezionare **+ New Key** (Aggiungi nuova chiave)
1. Specificare un nome
1. Incollare la chiave pubblica, escluse le virgolette circostanti
1. Fare clic su **Aggiungi**

## <a name="validate-the-kubernetes-configuration"></a>Convalidare la configurazione di Kubernetes

Dopo che `config-agent` avrà installato l'istanza di `flux`, le risorse contenute nel repository Git inizieranno a fluire nel cluster. Verificare che gli spazi dei nomi, le distribuzioni e le risorse siano stati creati:

```console
kubectl get ns --show-labels
```

**Output:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

È possibile osservare che spazi dei nomi `team-a`, `team-b`, `itops`e `cluster-config` sono stati creati.

L'operatore `flux` è stato distribuito nello spazio dei nomi `cluster-config`, come indicato da `sourceControlConfig`:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Output:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Esplorazione aggiuntiva

È possibile esplorare le altre risorse distribuite come parte del repository di configurazione:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Eliminare una configurazione

Eliminare un `sourceControlConfiguration` usando l'interfaccia della riga di comando di Azure o portale di Azure.  Dopo aver avviato il comando DELETE, la `sourceControlConfiguration` risorsa verrà eliminata immediatamente in Azure, ma potrebbe essere necessaria fino a un'ora per l'eliminazione completa degli oggetti associati dal cluster (è presente un elemento backlog per ridurre questo intervallo di tempo).

> [!NOTE]
> Dopo la creazione di un sourceControlConfiguration con ambito dello spazio dei nomi, è possibile che gli utenti con `edit` Binding Role nello spazio dei nomi distribuiscano i carichi di lavoro in questo spazio dei nomi. Quando `sourceControlConfiguration` viene eliminato questo oggetto con ambito dello spazio dei nomi, lo spazio dei nomi viene lasciato intatto e non verrà eliminato per evitare di suddividere gli altri carichi di lavoro.
> Qualsiasi modifica apportata al cluster risultante dalle distribuzioni dal repository git rilevato non viene eliminata quando `sourceControlConfiguration` viene eliminato.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare Helm con la configurazione del controllo del codice sorgente](./use-gitops-with-helm.md)
- [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
