---
title: Attività di Azure DevOps per Esplora dati di Azure
description: In questo argomento illustra come creare una pipeline di rilascio e distribuire
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: a70a887ccb19d9c1cbdb5f8ebf6aa8d4b25a0dfd
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65161071"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Attività di DevOps di Azure per dati di Azure Explorer

[Servizi di Azure DevOps](https://azure.microsoft.com/services/devops/) fornisce strumenti per la collaborazione, ad esempio le pipeline a prestazioni elevate, repository Git privati gratuiti, lavagne Kanban configurabili e numerose funzionalità test automatizzate e continuata sviluppo. [Le pipeline di Azure](https://azure.microsoft.com/services/devops/pipelines/) è una funzionalità DevOps di Azure che consente di gestire l'integrazione continua/recapito Continuo per distribuire il codice con le pipeline a prestazioni elevate che funzionano con qualsiasi linguaggio, piattaforme e cloud.
[Azure Esplora dati - i comandi di amministratore](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) è l'attività di pipeline di Azure che ti permette di creare pipeline di rilascio e distribuire il database delle modifiche ai database di Esplora dati di Azure. È disponibile gratuitamente nel [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Questo documento viene descritto un semplice esempio sull'uso delle **Esplora dati di Azure: i comandi di amministratore** modifiche di attività per distribuire il proprio schema al database. Per le pipeline CI/CD completate, consultare [documentazione di Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Installazione del Cluster di Esplora dati di Azure:
    * Un [cluster Esplora dati di Azure e database](/azure/data-explorer/create-cluster-database-portal)
    * Creare app di Azure Active Directory (Azure AD) per [il provisioning di un'applicazione Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Concedere l'accesso all'app di Azure AD nel database di Esplora dati di Azure da [gestione delle autorizzazioni di database di Azure Data Explorer](/azure/data-explorer/manage-database-permissions).
* Programma di installazione di Azure DevOps:
    * [Iscriversi per un'organizzazione gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Creazione di un'organizzazione](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Creare un progetto in Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Il codice con Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Creare cartelle

Creare le cartelle di esempio seguenti (*funzioni*, *criteri*, *tabelle*) nel repository Git. Copiare i file dalla [qui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) nelle rispettive cartelle come illustrato nella seguente e salvare le modifiche. Per eseguire il flusso di lavoro seguente sono disponibili i file di esempio.

![Creare cartelle](media/devops/create-folders.png)

> [!TIP]
> Quando si crea il proprio flusso di lavoro, è consigliabile rendere idempotenti il codice. Ad esempio, usare [tabella relative-merge](/azure/kusto/management/tables#create-merge-tables) invece di [tabella relative](/azure/kusto/management/tables#create-table)e usare [relative o alter](/azure/kusto/management/functions#create-or-alter-function) funzione anziché [relative](/azure/kusto/management/functions#create-function) funzione.

## <a name="create-a-release-pipeline"></a>Creare una pipeline di versione

1. Accedi per i [organizzazione di Azure DevOps](https://dev.azure.com/).
1. Selezionare **pipeline** > **versioni** dal menu a sinistra e selezionare **nuova pipeline**.

    ![Nuova pipeline](media/devops/new-pipeline.png)

1. Il **nuova pipeline di rilascio** verrà visualizzata la finestra. Nel **pipeline** nella scheda il **selezionare un modello** riquadro, selezionare **Comm**.

     ![Selezionare un modello:](media/devops/select-template.png)

1. Selezionare **fase** pulsante. Nelle **Stage** riquadro, aggiungere il **nome fase**. Selezionare **salvare** per salvare la pipeline.

    ![Nome fase](media/devops/stage-name.png)

1. Selezionare **aggiungere un elemento** pulsante. Nel **aggiungere un elemento** riquadro, selezionare il repository in cui è presente il codice, compilare le informazioni rilevanti e fare clic su **Add**. Selezionare **salvare** per salvare la pipeline.

    ![Aggiungere un elemento](media/devops/add-artifact.png)

1. Nel **variabili** scheda, seleziona **+ Aggiungi** per creare una variabile per **URL dell'Endpoint** che verrà usato nell'attività. Scrivere il **Name** e il **valore** dell'endpoint. Selezionare **salvare** per salvare la pipeline. 

    ![Creare una variabile](media/devops/create-variable.png)

    Per trovare le Endpoint_URL, la pagina di panoramica delle **Cluster di Azure Data Explorer** nel portale di Azure, portale contiene l'URI del cluster di Esplora dati di Azure. Costruire l'URI nel formato seguente `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Ad esempio: https://kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI del cluster Esplora dati Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Creare attività da distribuire

1. Nel **Pipeline** scheda, fare clic su **1 processo, 0 attività** per aggiungere le attività. 

    ![Aggiungere attività](media/devops/add-task.png)

1. Creare tre attività per distribuire **tabelle**, **funzioni**, e **criteri**, nell'ordine indicato. 

1. Nel **attività** scheda, seleziona **+** da **processo dell'agente**. Cercare **Esplora dati di Azure**. Nelle **Marketplace**, installare il **Esplora dati di Azure: i comandi di amministratore** estensione. Quindi, selezionare **Add** nelle **esecuzione del comando Esplora dati Azure**.

     ![Aggiungere i comandi di amministratore](media/devops/add-admin-commands.png)

1. Fare clic su **Kusto comando** a sinistra e aggiornare l'attività con le informazioni seguenti:
    * **Nome visualizzato**: Nome dell'attività
    * **Percorso del file**: Nel **tabelle** attività, specificare */Tables/*.csl poiché i file di creazione della tabella sono nella *tabella* cartella.
    * **URL dell'endpoint**: immettere il `EndPoint URL`variabile creata nel passaggio precedente.
    * Selezionare **Endpoint di servizio di utilizzo** e selezionare **+ nuovo**.

    ![Attività di comando di aggiornamento Kusto](media/devops/kusto-command-task.png)

1. Completare le informazioni seguenti nel **connessione del servizio aggiungere Esplora dati di Azure** finestra:

    |Impostazione  |Valore consigliato  |
    |---------|---------|
    |**Nome connessione**     |    Immettere un nome per identificare questo endpoint di servizio     |
    |**Url del cluster**    |    Valore sono reperibili nella sezione Panoramica del Cluster di Esplora dati di Azure nel portale di Azure | 
    |**Id dell'entità servizio**    |    Immettere l'ID dell'App AAD (creato come prerequisito)     |
    |**Chiave dell'entità servizio App**     |    Immettere la chiave dell'App AAD (creato come prerequisito)    |
    |**Id tenant AAD**    |      Immettere il tenant di AAD (ad esempio microsoft.com, contoso.com...)    |

    Selezionare **Consenti tutte le pipeline di usare tale connessione** casella di controllo. Selezionare **OK**.

    ![Aggiungi connessione al servizio](media/devops/add-service-connection.png)

1. Ripetere i passaggi da 1 a 5 un altro due volte per distribuire i file dal *funzioni* e *criteri* cartelle. Selezionare **Salva**. Nel **attività** scheda, vedere le tre attività creata: **Distribuire le tabelle**, **distribuire funzioni**, e **distribuire i criteri**.

    ![Distribuire tutte le cartelle](media/devops/deploy-all-folders.png)

1. Selezionare **+ Release** > **Crea versione** per creare una versione.

    ![Creare una versione](media/devops/create-release.png)

1. Nel **registri** scheda, controllare lo stato di distribuzione ha esito positivo.

    ![La distribuzione ha esito positivo](media/devops/deployment-successful.png)

A questo punto è stata completata la creazione di una pipeline di rilascio per la distribuzione delle tre attività di pre-produzione.