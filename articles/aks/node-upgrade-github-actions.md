---
title: Gestire gli aggiornamenti del nodo AKS con azioni di GitHub
titleSuffix: Azure Kubernetes Service
description: Informazioni su come aggiornare i nodi AKS usando le azioni di GitHub
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 7a24911fd771663c7edbbdf0c8d2d763a74fc586
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96535215"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Applicare automaticamente gli aggiornamenti della sicurezza ai nodi del servizio Azure Kubernetes usando le azioni di GitHub

Gli aggiornamenti della sicurezza sono una parte essenziale del mantenimento della sicurezza e della conformità del cluster AKS con le correzioni più recenti per il sistema operativo sottostante. Questi aggiornamenti includono correzioni della sicurezza del sistema operativo o gli aggiornamenti del kernel. Per completare il processo, alcuni aggiornamenti richiedono il riavvio del nodo.

`az aks upgrade`L'esecuzione di offre un modo per applicare gli aggiornamenti senza tempi di inattività. Il comando gestisce l'applicazione degli aggiornamenti più recenti a tutti i nodi del cluster, tramite il cordoning e lo svuotamento del traffico verso i nodi e il riavvio dei nodi, consentendo quindi il traffico ai nodi aggiornati. Se si aggiornano i nodi con un metodo diverso, AKS non riavvierà automaticamente i nodi.

> [!NOTE]
> La differenza principale tra `az aks upgrade` se usato con il `--node-image-only` flag è che, quando viene usata, verranno aggiornate solo le immagini del nodo. Se omesso, verranno aggiornate sia le immagini del nodo che la versione del piano di controllo Kubernetes. Per informazioni più dettagliate, vedere [la documentazione per gli aggiornamenti gestiti nei nodi][managed-node-upgrades-article] e [la documentazione per gli aggiornamenti del cluster][cluster-upgrades-article] .

Tutti i nodi di Kubernetes vengono eseguiti in una macchina virtuale (VM) standard di Azure. Queste macchine virtuali possono essere basate su Windows o su Linux. Le macchine virtuali basate su Linux usano un'immagine Ubuntu, con il sistema operativo configurato per verificare automaticamente la disponibilità di aggiornamenti ogni notte.

Quando si usa il `az aks upgrade` comando, l'interfaccia della riga di comando di Azure crea un picco di nuovi nodi con gli aggiornamenti più recenti relativi alla sicurezza e al kernel. questi nodi vengono inizialmente sottoposti a cordoning per impedire che le app vengano pianificate fino al termine dell'aggiornamento. Al termine, Azure cordons (rende il nodo non disponibile per la pianificazione di nuovi carichi di lavoro) e svuota (sposta i carichi di lavoro esistenti in un altro nodo) i nodi meno recenti e uncordonare quelli nuovi, trasferendo in modo efficace tutte le applicazioni pianificate ai nuovi nodi.

Questo processo è migliore dell'aggiornamento manuale dei kernel basati su Linux perché per Linux è necessario riavviare il computer quando viene installato un nuovo aggiornamento del kernel. Se si aggiorna manualmente il sistema operativo, è anche necessario riavviare la macchina virtuale, mediante il cordoning manuale e lo svuotamento di tutte le app.

Questo articolo illustra come automatizzare il processo di aggiornamento dei nodi AKS. Verranno usate le azioni di GitHub e l'interfaccia della riga di comando di Azure per creare un'attività di aggiornamento basata su `cron` che viene eseguita automaticamente.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Questo articolo presuppone anche che l'utente disponga di un account [GitHub][github] in cui creare le azioni.

## <a name="create-a-timed-github-action"></a>Creare un'azione GitHub a tempo

`cron` è un'utilità che consente di eseguire un set di comandi, o di processi, in base a una pianificazione automatizzata. Per creare un processo per aggiornare i nodi AKS in una pianificazione automatica, è necessario un repository per ospitare le azioni. In genere, le azioni di GitHub sono configurate nello stesso repository dell'applicazione, ma è possibile usare qualsiasi repository. Per questo articolo verrà usato il repository del [profilo][profile-repository]. Se non è presente, creare un nuovo repository con lo stesso nome del nome utente di GitHub.

1. Passare al repository in GitHub
1. Fare clic sulla scheda **azioni** nella parte superiore della pagina.
1. Se è già stato configurato un flusso di lavoro in questo repository, si verrà indirizzati all'elenco delle esecuzioni completate, in questo caso fare clic sul pulsante **nuovo flusso di lavoro** . Se si tratta del primo flusso di lavoro nel repository, GitHub presenta alcuni modelli di progetto, fare clic sul collegamento **configura un flusso di lavoro** sotto il testo della descrizione.
1. Modificare il flusso `name` di lavoro e i `on` tag in modo simile al seguente. Le azioni di GitHub usano la stessa [sintassi di POSIX cron][cron-syntax] di qualsiasi sistema basato su Linux. In questa pianificazione viene indicato che il flusso di lavoro viene eseguito ogni 15 giorni alle 3.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Creare un nuovo processo usando quanto riportato di seguito. Questo processo è denominato `upgrade-node` , viene eseguito su un agente Ubuntu e si connetterà all'account dell'interfaccia della riga di comando di Azure per eseguire i passaggi necessari per aggiornare i nodi.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Configurare l'interfaccia della riga di comando di Azure nel flusso di lavoro

Nella `steps` chiave è possibile definire tutte le operazioni che verranno eseguite dal flusso di lavoro per aggiornare i nodi.

Scaricare e accedere all'interfaccia della riga di comando di Azure.

1. Sul lato destro della schermata azioni di GitHub trovare la barra di ricerca del *Marketplace* e digitare **"login di Azure"**.
1. Si otterrà quindi un'azione denominata **Azure login** pubblicata **da Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Risultati della ricerca che mostrano due righe, la prima azione è denominata &quot;Azure login e la seconda&quot; Azure Container Registry login":::

1. Fare clic su **account di accesso di Azure**. Nella schermata successiva fare clic sull' **icona copia** in alto a destra nell'esempio di codice.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Riquadro dei risultati dell'azione di accesso di Azure con l'esempio di codice seguente, il quadrato rosso intorno a un'icona di copia evidenzia l'area di clic":::

1. Incollare quanto segue sotto la `steps` chiave:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Dall'interfaccia della riga di comando di Azure, eseguire il comando seguente per generare un nuovo nome utente e una nuova password.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    L'output dovrebbe essere simile al codice JSON seguente:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **In una nuova finestra del browser** passare al repository GitHub e aprire la scheda **Settings (impostazioni** ) del repository. Fare clic su **Secrets** e quindi su **New repository Secret**.
1. Per *Nome* usare `AZURE_CREDENTIALS`.
1.  In *valore* aggiungere l'intero contenuto dall'output del passaggio precedente in cui sono stati creati un nuovo nome utente e una nuova password.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Modulo che mostra AZURE_CREDENTIALS come titolo del segreto e l'output del comando eseguito incollato come JSON":::

1. Fare clic su **Aggiungi segreto**.

L'interfaccia della riga di comando usata dall'azione verrà registrata nell'account Azure ed è pronta per l'esecuzione dei comandi.

Per creare i passaggi per eseguire i comandi dell'interfaccia della riga di comando di Azure.

1. Passare alla **pagina di ricerca** in *GitHub Marketplace* sul lato destro dello schermo e cercare l'azione dell'interfaccia della riga di comando di *Azure*. Scegliere *azione CLI di Azure da Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Risultato della ricerca per l'azione dell'interfaccia della riga di comando di Azure con il primo risultato visualizzato come fatto da Azure":::

1. Fare clic sul pulsante Copy (copia) nel *risultato di GitHub Marketplace* e incollare il contenuto dell'azione nell'editor principale, sotto il passaggio *Azure login* , simile al seguente:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only
    ```

    > [!TIP]
    > È possibile separare i `-g` parametri e `-n` dal comando aggiungendoli a segreti simili ai passaggi precedenti. Sostituire i `{resourceGroupName}` `{aksClusterName}` segnaposto e in base alle rispettive controparti segrete, ad esempio `${{secrets.RESOURCE_GROUP_NAME}}` e `${{secrets.AKS_CLUSTER_NAME}}`

1. Rinominare il file in `upgrade-node-images`.
1. Fare clic su **Avvia commit**, aggiungere un titolo del messaggio e salvare il flusso di lavoro.

Una volta creato il commit, il flusso di lavoro verrà salvato e pronto per l'esecuzione.

> [!NOTE]
> Per aggiornare un pool a nodo singolo anziché tutti i pool di nodi nel cluster, aggiungere il `--name` parametro al `az aks nodepool upgrade` comando per specificare il nome del pool di nodi. Ad esempio:
> ```
> inlineScript: az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Eseguire manualmente l'azione GitHub

È possibile eseguire il flusso di lavoro manualmente, oltre all'esecuzione pianificata, aggiungendo un nuovo `on` trigger denominato `workflow_dispatch` . Il file finito dovrebbe essere simile al seguente:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases) per informazioni sulle immagini del nodo più recenti.
- Informazioni su come aggiornare la versione di Kubernetes con l' [aggiornamento di un cluster AKS][cluster-upgrades-article].
- Altre informazioni su più pool di nodi e su come aggiornare i pool di nodi con [creare e gestire pool][use-multiple-node-pools]di nodi multipli.
- Altre informazioni sui [pool di nodi di sistema][system-pools]
- Per informazioni su come risparmiare sui costi usando le istanze di spot, vedere [aggiungere un pool di nodi spot a AKS][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
