---
title: Azure DevOps task for Azure Data Explorer
description: In questo argomento imparerai a creare una pipeline di rilascio e a distribuire
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472044"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps Task for Azure Data Explorer

[Servizi DevOps](https://azure.microsoft.com/services/devops/) di Azure offre strumenti di collaborazione di sviluppo, ad esempio pipeline ad alte prestazioni, repository Git privati gratuiti, schede Kanban configurabili e funzionalità complete di test automatizzati e continui. [Pipeline di Azure](https://azure.microsoft.com/services/devops/pipelines/) è una funzionalità DevOps di Azure che consente di gestire CI/CD per distribuire il codice con pipeline ad alte prestazioni che funzionano con qualsiasi lingua, piattaforma e cloud.
[Azure Data Explorer: comandi di amministrazione](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) è l'attività Pipeline di Azure che consente di creare pipeline di rilascio e distribuire le modifiche del database nei database di Azure Data Explorer.Azure Data Explorer - Admin Commands is the Azure Pipelines task that enables you to create release pipelines and deploy your database changes to your Azure Data Explorer databases. È disponibile gratuitamente in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Questo documento descrive un semplice esempio sull'uso dell'attività **Azure Data Explorer - Comandi di amministrazione** per distribuire le modifiche dello schema nel database. Per le pipeline CI/CD complete, vedere la documentazione di [Azure DevOps.](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Configurazione del cluster di Azure Data Explorer:Azure Data Explorer Cluster setup:
    * Un cluster e un database di [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    * Creare un'app Azure Active Directory (Azure AD) eseguendo il provisioning di [un'applicazione Azure AD.](/azure/kusto/management/access-control/how-to-provision-aad-app)
    * Concedere l'accesso all'app Azure AD nel database di Azure Data Explorer gestendo le autorizzazioni del [database di Azure Data Explorer.](/azure/data-explorer/manage-database-permissions)
* Configurazione di Azure DevOps:Azure DevOps setup:
    * [Iscriviti per un'organizzazione gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Creare un'organizzazione](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Creare un progetto in Azure DevOpsCreate a project in Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Codice con Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Creare cartelle

Creare le cartelle di esempio seguenti (*Funzioni*, *Criteri*, *Tabelle*) nel repository Git . Copiare i file da [qui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) nelle rispettive cartelle come illustrato di seguito ed eseguire il commit delle modifiche. I file di esempio vengono forniti per eseguire il flusso di lavoro seguente.

![Creare cartelle](media/devops/create-folders.png)

> [!TIP]
> Quando si crea un flusso di lavoro personalizzato, è consigliabile rendere il codice idempotente. Ad esempio, utilizzare [la tabella .create-merge](/azure/kusto/management/create-table-command#create-merge-table) anziché [.create table](/azure/kusto/management/create-table-command)e la funzione [.create-or-alter](/azure/kusto/management/create-alter-function) anziché [.create.](/azure/kusto/management/create-function)

## <a name="create-a-release-pipeline"></a>Creare una pipeline di versione

1. Accedere [all'organizzazione DevOps di Azure.](https://dev.azure.com/)
1. Selezionare**Rilasci** **pipeline** > dal menu a sinistra e selezionare **Nuova pipeline**.

    ![Nuova pipeline](media/devops/new-pipeline.png)

1. Verrà visualizzata la finestra **Nuova pipeline di rilascio.** Nel riquadro **Selezionare un modello** della scheda **Pipeline** selezionare **Processo vuoto.**

     ![Selezionare un modello:](media/devops/select-template.png)

1. Selezionare il pulsante **Fase.** Nel riquadro **Fase,** aggiungere il **nome dello stage**. Selezionare **Salva** per salvare la pipeline.

    ![Assegnare un nome alla fase](media/devops/stage-name.png)

1. Selezionare **il pulsante Aggiungi un elemento.** Nel **riquadro Aggiungi un elemento** selezionare il repository in cui è presente il codice, immettere le informazioni pertinenti e fare clic su **Aggiungi**. Selezionare **Salva** per salvare la pipeline.

    ![Aggiungere un elemento](media/devops/add-artifact.png)

1. Nella scheda **Variabili,** selezionare **Aggiungi** per creare una variabile per **l'URL dell'endpoint** che verrà utilizzata nell'attività. Scrivere il **nome** e il **valore** dell'endpoint. Selezionare **Salva** per salvare la pipeline. 

    ![Crea variabile](media/devops/create-variable.png)

    Per trovare la Endpoint_URL, la pagina di panoramica del cluster di Azure Data Explorer nel portale di Azure contiene l'URI del cluster di Azure Data Explorer.To find your Endpoint_URL, the overview page of your **Azure Data Explorer Cluster** in the Azure portal contains the Azure Data Explorer cluster URI. Costruire l'URI nel `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`formato seguente.  Ad esempio,\/https: /kustodocs.westus.kusto.windows.net?DatabaseName/SampleDB

    ![Azure Data Explorer cluster URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Creare attività da distribuire

1. Nella scheda **Pipeline** fare clic su **1 processo, 0 attività** per aggiungere attività. 

    ![Aggiungere attività](media/devops/add-task.png)

1. Creare tre attività per distribuire **tabelle,** **funzioni**e **criteri**, in questo ordine. 

1. Nella scheda **Attività** **+** selezionare in base al **processo agente**. Cercare **Esplora dati di Azure**. In **Marketplace**installare l'estensione **Azure Data Explorer - Comandi di amministrazione.** Selezionare **quindi Aggiungi** in Esegui comando di Esplora dati di Azure . **Run Azure Data Explorer Command**

     ![Aggiungere comandi di amministrazione](media/devops/add-admin-commands.png)

1. Clicca su **Comando Kusto** a sinistra e aggiorna l'attività con le seguenti informazioni:
    * **Nome visualizzato**: Nome dell'attività
    * **Percorso file**: nell'attività **Tabelle,** specificare */Tables/*.csl poiché i file di creazione della tabella si trovano nella cartella *Table.*
    * **URL endpoint**: `EndPoint URL`immettere la variabile creata nel passaggio precedente.
    * Selezionare **Usa endpoint del servizio** e selezionare **Nuovo**.

    ![Attività di comando Aggiorna Kusto](media/devops/kusto-command-task.png)

1. Completare le informazioni seguenti nella finestra di connessione del servizio Aggiungi Azure Data Explorer:Complete the following information in the **Add Azure Data Explorer service connection** window:

    |Impostazione  |Valore consigliato  |
    |---------|---------|
    |**Nome connessione**     |    Immettere un nome per identificare l'endpoint del servizio     |
    |**Cluster Url**    |    Il valore è disponibile nella sezione Panoramica del cluster di Azure Data Explorer nel portale di AzureValue can be found in the overview section of your Azure Data Explorer Cluster in the Azure portal | 
    |**ID entità servizioService Principal Id**    |    Immettere l'ID app AAD (creato come prerequisito)     |
    |**Chiave dell'app dell'entità servizioService Principal App Key**     |    Immettere la chiave dell'app AAD (creata come prerequisito)    |
    |**AAD tenant Id**    |      Immettere il tenant AAD (ad esempio microsoft.com, contoso.com...)    |

    Selezionare la casella di controllo **Consenti a tutte le pipeline di usare questa connessione.** Selezionare **OK**.

    ![Aggiungi connessione al servizio](media/devops/add-service-connection.png)

1. Ripetere i passaggi da 1 a 5 altre due volte per distribuire i file dalle cartelle *Funzioni* e *Criteri.* Selezionare **Salva**. Nella scheda **Attività** vedere le tre attività create: **Distribuisci tabelle**, **Distribuisci funzioni**e **Distribuisci criteri**.

    ![Distribuire tutte le cartelle](media/devops/deploy-all-folders.png)

1. Per creare una release, **selezionare : Rilascia** > **versione Crea versione.**

    ![Creare una release](media/devops/create-release.png)

1. Nella scheda **Registri** verificare che lo stato della distribuzione sia stato eseguito correttamente.

    ![La distribuzione è riuscita](media/devops/deployment-successful.png)

È stata completata la creazione di una pipeline di rilascio per la distribuzione di tre attività in pre-produzione.