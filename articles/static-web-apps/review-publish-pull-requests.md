---
title: Esaminare le richieste pull negli ambienti di pre-produzione in App Web statiche di Azure
description: Informazioni su come usare gli ambienti di pre-produzione per esaminare le modifiche alle richieste pull in App Web statiche di Azure.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 61c5917c1e4cb9dbf96e90af9a30777ea7c2e66c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594110"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Esaminare le richieste pull negli ambienti di pre-produzione in App Web statiche di Azure (anteprima)

Questo articolo illustra come usare gli ambienti di pre-produzione per esaminare le modifiche apportate alle applicazioni distribuite con [App Web statiche di Azure](overview.md).

Un ambiente di pre-produzione (staging) è una versione di staging completamente funzionale dell'applicazione che include modifiche non disponibili nell'ambiente di produzione.

App Web statiche di Azure genera un flusso di lavoro di GitHub Actions nel repository. Quando viene creata una richiesta pull per un ramo che il flusso di lavoro controlla, viene creato l'ambiente di pre-produzione. L'ambiente di pre-produzione installa l'app per poterla esaminare prima di eseguire il push nell'ambiente di produzione.

Più ambienti di pre-produzione possono coesistere nello stesso momento con App Web statiche di Azure. Ogni volta che si crea una richiesta pull nel ramo controllato, una versione di staging con le modifiche viene distribuita in un ambiente di pre-produzione distinto.

L'uso degli ambienti di pre-produzione offre molti vantaggi. Ad esempio, è possibile:

- Esaminare le modifiche visive tra l'ambiente di produzione e quello di staging. Ad esempio, visualizzare gli aggiornamenti al contenuto e al layout.
- Mostrare al team le modifiche apportate.
- Confrontare versioni diverse dell'applicazione.
- Convalidare le modifiche usando i test di accettazione.
- Eseguire controlli di integrità prima della distribuzione nell'ambiente di produzione.

> [!NOTE]
> Durante la fase di anteprima, è consentito un [massimo di un solo ambiente di staging](quotas.md) alla volta.

## <a name="prerequisites"></a>Prerequisiti

- Repository GitHub esistente configurato con App Web statiche di Azure. Se non è ancora stato creato, vedere [Compilazione della prima app statica](getting-started.md).

## <a name="make-a-change"></a>Apportare una modifica

Per iniziare, apportare una modifica nel repository. È possibile eseguire questa operazione direttamente in GitHub, come illustrato nei passaggi seguenti.

1. Passare al repository del progetto in GitHub, quindi fare clic sul pulsante **Branch** (Ramo) per creare un nuovo ramo.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Creare un nuovo ramo usando l'interfaccia di GitHub":::

    Digitare un nome di ramo e fare clic su **Create branch** (Crea ramo).

1. Passare alla cartella _app_ e modificare il contenuto di testo. Ad esempio, è possibile modificare un titolo o un paragrafo. Dopo aver trovato il file da modificare, fare clic su **Edit** (Modifica) per apportare la modifica.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Pulsante di modifica file nell'interfaccia di GitHub":::

1. Dopo avere apportato le modifiche, fare clic su **Commit changes** (Commit delle modifiche) per eseguire il commit delle modifiche nel ramo.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Pulsante di commit delle modifiche nell'interfaccia di GitHub":::

## <a name="create-a-pull-request"></a>Creare una richiesta pull

Successivamente, creare una richiesta pull da questa modifica.

1. Aprire la scheda **Pull request** (Richiesta pull) del progetto in GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Scheda Richiesta pull in un repository GitHub":::

1. Fare clic sul pulsante **Compare & pull request** (Confronta e richiesta pull) del ramo.

1. Facoltativamente, è possibile specificare alcuni dettagli sulle modifiche, quindi fare clic su **Create pull request** (Crea richiesta pull).

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Creazione della richiesta pull in GitHub":::

Se necessario, è possibile assegnare revisori e aggiungere commenti per discutere le modifiche.

> [!NOTE]
> È possibile apportare più modifiche ed eseguire il push di nuovi commit nel ramo. La richiesta pull viene quindi aggiornata automaticamente per riflettere tutte le modifiche.

## <a name="review-changes"></a>Verificare le modifiche

Dopo la creazione della richiesta pull, il flusso di lavoro di distribuzione di [GitHub Actions](https://github.com/features/actions) viene eseguito e distribuisce le modifiche in un ambiente di pre-produzione.

Quando il flusso di lavoro ha completato la compilazione e la distribuzione dell'app, il bot GitHub aggiunge alla richiesta pull un commento che contiene l'URL dell'ambiente di pre-produzione. È possibile fare clic su questo collegamento per visualizzare le modifiche preparate per il commit.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Commento alla richiesta pull con l'URL di pre-produzione":::

Fare clic sull'URL generato per visualizzare le modifiche.

Se si esamina più da vicino l'URL, è possibile osservare che è composto come segue: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`.

Per una determinata richiesta pull, l'URL rimane invariato anche se si esegue il push di nuovi aggiornamenti. Oltre all'URL che rimane costante, lo stesso ambiente di pre-produzione viene riutilizzato per tutta la durata della richiesta pull.

## <a name="publish-changes"></a>Pubblicare modifiche

Una volta approvate le modifiche, è possibile pubblicare le modifiche nell'ambiente di produzione tramite l'unione della richiesta pull.

Fare clic su **Merge pull request** (Esegui merge della richiesta pull):

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Pulsante di esecuzione merge della richiesta pull nell'interfaccia di GitHub":::

Il merge copia le modifiche apportate al ramo monitorato, ovvero il ramo di "produzione". Quindi, il flusso di lavoro di distribuzione viene avviato nel ramo monitorato e le modifiche diventano attive dopo la ricompilazione dell'applicazione.

Per verificare le modifiche nell'ambiente di produzione, aprire l'URL di produzione per caricare la versione live del sito Web.

## <a name="limitations"></a>Limitazioni

Le versioni di staging dell'applicazione sono attualmente accessibili pubblicamente tramite il relativo URL, anche se il repository GitHub è privato.

> [!WARNING]
> Prestare attenzione quando si pubblicano contenuti sensibili nelle versioni di staging, in quanto l'accesso agli ambienti di pre-produzione non è limitato.

Il numero di ambienti di pre-produzione disponibili per ogni app distribuita con App Web statiche dipende dal livello di SKU in uso. Nel livello gratuito, ad esempio, è disponibile 1 ambiente di pre-produzione oltre all'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare un dominio personalizzato](custom-domain.md)
