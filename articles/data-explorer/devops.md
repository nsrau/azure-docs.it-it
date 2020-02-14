---
title: Attività DevOps di Azure per Azure Esplora dati
description: In questo argomento viene illustrato come creare una pipeline di versione e distribuire
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 6394d7149bd4e80f0a17a59a6259eedf4c806fd4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188169"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Attività DevOps di Azure per Azure Esplora dati

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) offre strumenti di collaborazione per lo sviluppo, ad esempio pipeline ad alte prestazioni, repository git privati gratuiti, lavagne Kanban configurabili e funzionalità complete di test automatizzati e continui. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) è una funzionalità di Azure DevOps che consente di gestire ci/CD per distribuire il codice con pipeline ad alte prestazioni che funzionano con qualsiasi linguaggio, piattaforma e cloud.
[Comandi di amministrazione Esplora dati di Azure](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) è l'attività Azure Pipelines che consente di creare pipeline di versione e distribuire le modifiche al database nei database di Esplora dati di Azure. È disponibile gratuitamente nel [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Questo documento descrive un semplice esempio di uso dell'attività **comandi di Azure Esplora dati – admin** per distribuire le modifiche dello schema nel database. Per le pipeline CI/CD complete, vedere la [documentazione di Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Prerequisites

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Installazione del cluster di Esplora dati di Azure:
    * Un [cluster e un database di Azure Esplora dati](/azure/data-explorer/create-cluster-database-portal)
    * Creare un'app Azure Active Directory (Azure AD) effettuando il [provisioning di un'applicazione Azure ad](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Concedere l'accesso all'App Azure AD nel database di Esplora dati di Azure [gestendo le autorizzazioni di azure Esplora dati database](/azure/data-explorer/manage-database-permissions).
* Installazione di Azure DevOps:
    * [Iscriversi per ottenere un'organizzazione gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Creare un'organizzazione](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Creare un progetto in Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Codice con git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Creare cartelle

Creare le cartelle di esempio seguenti (*funzioni*, *criteri*e *tabelle*) nel repository git. Copiare i file da [qui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) nelle rispettive cartelle come illustrato di seguito ed eseguire il commit delle modifiche. Per eseguire il flusso di lavoro seguente, vengono forniti i file di esempio.

![Creare cartelle](media/devops/create-folders.png)

> [!TIP]
> Quando si crea un flusso di lavoro personalizzato, è consigliabile rendere il codice idempotente. Utilizzare, ad esempio, [. Create-merge table](/azure/kusto/management/create-table-command#create-merge-table) anziché [. Create Table](/azure/kusto/management/create-table-command)e utilizzare la funzione [. Create-o-ALTER](/azure/kusto/management/functions#create-or-alter-function) invece della funzione [. Create](/azure/kusto/management/functions#create-function) .

## <a name="create-a-release-pipeline"></a>Creare una pipeline di versione

1. Accedere all' [organizzazione DevOps di Azure](https://dev.azure.com/).
1. Selezionare **pipeline** > **versioni** dal menu a sinistra e selezionare **nuova pipeline**.

    ![Nuova pipeline](media/devops/new-pipeline.png)

1. Verrà visualizzata la finestra **nuova pipeline di rilascio** . Nel riquadro **selezionare un modello** della scheda **pipeline** selezionare **processo vuoto**.

     ![Selezionare un modello:](media/devops/select-template.png)

1. Selezionare il pulsante **stage** . Nel riquadro **fase** aggiungere il **nome della fase**. Selezionare **Save (Salva** ) per salvare la pipeline.

    ![Assegnare un nome alla fase](media/devops/stage-name.png)

1. Selezionare il pulsante **Aggiungi elemento** . Nel riquadro **Aggiungi un artefatto** selezionare il repository in cui è presente il codice, compilare le informazioni rilevanti e fare clic su **Aggiungi**. Selezionare **Save (Salva** ) per salvare la pipeline.

    ![Aggiungere un elemento](media/devops/add-artifact.png)

1. Nella scheda **variabili** selezionare **+ Aggiungi** per creare una variabile per l' **URL dell'endpoint** che verrà utilizzato nell'attività. Scrivere il **nome** e il **valore** dell'endpoint. Selezionare **Save (Salva** ) per salvare la pipeline. 

    ![Crea variabile](media/devops/create-variable.png)

    Per trovare la Endpoint_URL, la pagina Panoramica del **cluster di azure Esplora dati** nel portale di Azure contiene l'URI del cluster di Azure Esplora dati. Costruire l'URI nel formato seguente `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Ad esempio, https:\//kustodocs.westus.kusto.Windows.NET? DatabaseName = SampleDB

    ![URI del cluster di Azure Esplora dati](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Creazione di attività da distribuire

1. Nella scheda **pipeline** fare clic su **1 processo, 0 attività** per aggiungere attività. 

    ![Aggiungere attività](media/devops/add-task.png)

1. Creare tre attività per la distribuzione di **tabelle**, **funzioni**e **criteri**in questo ordine. 

1. Nella scheda **attività** selezionare **+** in base al **processo di Agent**. Cercare **Esplora dati di Azure**. In **Marketplace**installare l'estensione **Azure Esplora dati – admin Commands** . Selezionare quindi **Aggiungi** in **Esegui comando di Azure Esplora dati**.

     ![Aggiungere comandi di amministratore](media/devops/add-admin-commands.png)

1. Fare clic sul **comando kusto** a sinistra e aggiornare l'attività con le informazioni seguenti:
    * **Nome visualizzato**: nome dell'attività
    * **Percorso file**: nell'attività **tabelle** specificare */Tables/* . CSL poiché i file di creazione della tabella si trovano nella cartella della *tabella* .
    * **URL endpoint**: immettere la variabile `EndPoint URL`creata nel passaggio precedente.
    * Selezionare **Usa endpoint servizio** e selezionare **+ nuovo**.

    ![Attività Aggiorna comando kusto](media/devops/kusto-command-task.png)

1. Completare le seguenti informazioni nella finestra **Aggiungi connessione al servizio Esplora dati di Azure** :

    |Impostazione  |Valore consigliato  |
    |---------|---------|
    |**Nome connessione**     |    Immettere un nome per identificare l'endpoint di servizio     |
    |**URL del cluster**    |    Il valore si trova nella sezione Panoramica del cluster di Azure Esplora dati nel portale di Azure | 
    |**ID entità servizio**    |    Immettere l'ID app AAD (creato come prerequisito)     |
    |**Chiave dell'app dell'entità servizio**     |    Immettere la chiave dell'app AAD (creata come prerequisito)    |
    |**ID tenant AAD**    |      Immettere il tenant di AAD (ad esempio microsoft.com, contoso.com...)    |

    Selezionare **la casella di controllo Consenti a tutte le pipeline di utilizzare questa connessione** . Selezionare **OK**.

    ![Aggiungi connessione al servizio](media/devops/add-service-connection.png)

1. Ripetere i passaggi 1-5 altre due volte per distribuire i file dalle cartelle *funzioni* e *criteri* . Selezionare **Salva**. Nella scheda **attività** , vedere le tre attività create: **deploy Tables**, **deploy Functions**e **deploy Policies**.

    ![Distribuisci tutte le cartelle](media/devops/deploy-all-folders.png)

1. Selezionare **+ release** > **Crea versione** per creare una versione.

    ![Creare una versione](media/devops/create-release.png)

1. Nella scheda **logs** verificare che lo stato della distribuzione sia completato.

    ![La distribuzione è riuscita](media/devops/deployment-successful.png)

A questo punto è stata completata la creazione di una pipeline di versione per la distribuzione di tre attività in pre-produzione.