---
title: Source control in Azure Data Factory
description: Learn how to configure source control in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 264c60c719ffdd94664ae3a85fc67894d14f394d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484458"
---
# <a name="source-control-in-azure-data-factory"></a>Source control in Azure Data Factory

The Azure Data Factory user interface experience (UX) has two experiences available for visual authoring:

- Creare direttamente con il servizio Data Factory
- Author with Azure Repos Git or GitHub integration

> [!NOTE]
> Only authoring directly with the Data Factory service is supported in the Azure Government Cloud.

## <a name="author-directly-with-the-data-factory-service"></a>Creare direttamente con il servizio Data Factory

While authoring directly with the Data Factory service, the only way to save changes is via the **Publish All** button. Once clicked, all changes that you made are published directly to the Data Factory service. 

![Modalità Pubblica](media/author-visually/data-factory-publish.png)

Authoring directly with the Data Factory service has the following limitations:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche.
- Il servizio Data Factory non è ottimizzato per la collaborazione o il controllo della versione.

> [!NOTE]
> Authoring directly with the Data Factory service is disabled in the Azure Data Factory UX when a Git repository is configured. Changes can be made directly to the service via PowerShell or an SDK.

## <a name="author-with-azure-repos-git-integration"></a>Creare con l'integrazione di GIT Azure Repos

La soluzione che prevede l'uso dell'integrazione di GIT Azure Repos supporta funzionalità per il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. È possibile associare una data factory a un repository di organizzazione GIT Azure Repos per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Una singola organizzazione GIT Azure Repos può avere più repository, ma un repository GIT Azure Repos può essere associato a una sola data factory. Se non si ha un'organizzazione o un repository Azure Repos, seguire [queste istruzioni](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare le risorse.

> [!NOTE]
> È possibile archiviare file di script e di dati in un repository GIT Azure Repos. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di dati o di script archiviati in un repository GIT Azure Repos.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurare un repository GIT Azure Repos con Azure Data Factory

È possibile configurare un repository GIT Azure Repos con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuration method 1: Azure Data Factory home page

On the Azure Data Factory home page, select **Set up Code Repository**.

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente
In the Azure Data Factory UX authoring canvas, select the **Data Factory** drop-down menu, and then select **Set up Code Repository**.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Both methods open the repository settings configuration pane.

![Configurare le impostazioni del repository di codice](media/author-visually/repo-settings.png)

The configuration pane shows the following Azure Repos code repository settings:

| Impostazione | Description | Value |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps Git or GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Organizzazione Azure Repos** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Nome progetto** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. By default, it’s `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT Azure Repos. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

> [!NOTE]
> If you are using Microsoft Edge and do not see any values in your Azure DevOps Account dropdown, add https://*.visualstudio.com to the trusted sites list.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usare un tenant di Azure Active Directory diverso

È possibile creare un repository GIT Azure Repos in un tenant di Azure Active Directory diverso. Per specificare un tenant di Azure AD diverso, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata.

### <a name="use-your-personal-microsoft-account"></a>Usare l'account Microsoft personale

Per usare un account Microsoft personale per l'integrazione con Git, è possibile collegare il repository di Azure personale con l'istanza di Active Directory dell'organizzazione.

1. Aggiungere come guest l'account Microsoft personale all'istanza di Active Directory dell'organizzazione. Per altre informazioni, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../active-directory/b2b/add-users-administrator.md).

2. Accedere al portale di Azure con l'account Microsoft personale. Passare quindi all'istanza di Active Directory dell'organizzazione.

3. Passare alla sezione Azure DevOps, in cui è possibile visualizzare il repository personale. Selezionare il repository e connettersi ad Active Directory.

Dopo questi passaggi di configurazione, il repository personale è disponibile quando si configura l'integrazione di Git nell'interfaccia utente di Data Factory.

Per altre informazioni sulla connessione di Azure Repos all'istanza di Active Directory dell'organizzazione, vedere [Connect your Azure DevOps organization to Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad) (Connettere un'organizzazione di Azure DevOps ad Azure Active Directory).

## <a name="author-with-github-integration"></a>Creazione tramite l'integrazione di GitHub

La soluzione che prevede l'uso dell'integrazione di GitHub supporta funzionalità per il controllo del codice sorgente e la collaborazione per le attività sulle pipeline di data factory. È possibile associare una data factory a un repository di account GitHub per il controllo del codice sorgente, la collaborazione e il controllo delle versioni. Un singolo account GitHub può avere più repository, ma un repository GitHub può essere associato a una sola data factory. In assenza di un account o un repository GitHub seguire  [queste istruzioni](https://github.com/join)  per creare le risorse.

L'integrazione di GitHub con Data Factory supporta GitHub pubblico (ovvero [https://github.com](https://github.com)) e GitHub Enterprise. È possibile usare i repository GitHub pubblici e privati con Data Factory purché siano state ottenute le autorizzazioni di lettura e scrittura nel repository in GitHub.

To configure a GitHub repo, you must have administrator permissions for the Azure subscription that you're using.

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configure a GitHub repository with Azure Data Factory

È possibile configurare un repository GitHub con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuration method 1: Azure Data Factory home page

On the Azure Data Factory home page, select **Set up Code Repository**.

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente

In the Azure Data Factory UX authoring canvas, select the **Data Factory** drop-down menu, and then select **Set up Code Repository**.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Both methods open the repository settings configuration pane.

![Impostazioni del repository GitHub](media/author-visually/github-integration-image2.png)

The configuration pane shows the following GitHub repository settings:

| **Impostazione** | **Descrizione**  | **Valore**  |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos. | GitHub |
| **Use GitHub Enterprise** (Usa GitHub Enterprise) | Casella di controllo per selezionare GitHub Enterprise | unselected (default) |
| **GitHub Enterprise URL** (URL GitHub Enterprise) | URL radice di GitHub Enterprise. Ad esempio: https://github.mydomain.com. Required only if **Use GitHub Enterprise** is selected | `<your GitHub enterprise url>` |                                                           
| **Account GitHub** | Nome dell'account GitHub. This name can be found from https:\//github.com/{account name}/{repository name}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub. | `<your GitHub account name>` |
| **Repository Name**  | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account. | `<your repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di GitHub usato per la pubblicazione. By default, its master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifies whether to import existing data factory resources from the UX authoring canvas into a GitHub repository. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

### <a name="known-github-limitations"></a>Known GitHub limitations

- È possibile archiviare file di script e di dati in un repository GitHub. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di script o di dati archiviati in un repository GitHub.

- GitHub Enterprise in una versione precedente alla 2.14.0 non funziona nel browser Microsoft Edge.

- GitHub integration with the Data Factory visual authoring tools only works in the generally available version of Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Cambiare repository Git

To switch to a different Git repo, click the **Git Repo Settings** icon in the upper right corner of the Data Factory overview page. Se l'icona non è visibile, cancellare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

![Git icon](media/author-visually/remove-repo.png)

Once the Repository Settings pane appears, select **Remove Git**. Enter your data factory name and click **confirm** to remove the Git repository associated with your data factory.

![Rimuovere l'associazione al repository Git corrente](media/author-visually/remove-repo2.png)

After you remove the association with the current repo, you can configure your Git settings to use a different repo and then import existing Data Factory resources to the new repo. 

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

### <a name="creating-feature-branches"></a>Creating feature branches

Ogni repository GIT Azure Repos associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Users can also create feature branches by clicking **+ New Branch** in the branch dropdown. Once the new branch pane appears, enter the name of your feature branch.

![Create a new branch](media/author-visually/new-branch.png)

When you are ready to merge the changes from your feature branch to your collaboration branch, click on the branch dropdown and select **Create pull request**. Viene visualizzata la pagina GIT Azure Repos, in cui è possibile generare richieste di pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per configurare il ramo di pubblicazione, vale a dire, il ramo in cui vengono salvati i modelli di Resource Manager, aggiungere un file `publish_config.json` nella cartella radice nel ramo di collaborazione. Data Factory legge questo file, cerca il campo `publishBranch` e crea un nuovo ramo (se non esiste già) con il valore specificato. Quindi salva tutti i modelli di Resource Manager nel percorso specificato. ad esempio:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando si specifica un nuovo ramo di pubblicazione, Data Factory non elimina il ramo di pubblicazione precedente. If you want to remove the previous publish branch, delete it manually.

> [!NOTE]
> Data Factory legge il file `publish_config.json` solo al momento del caricamento della factory. Se la factory è già caricata nel portale, aggiornare il browser per rendere effettive le modifiche.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

After you have merged changes to the collaboration branch (`master` is the default), click **Publish** to manually publish your code changes in the master branch to the Data Factory service.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

A side pane will open where you confirm that the publish branch and pending changes are correct. Once you verify your changes, click **OK** to confirm the publish.

![Confermare il ramo di pubblicazione corretto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Il ramo principale non è rappresentativo di ciò che viene distribuito nel servizio Data Factory. Il ramo master *deve* essere pubblicato manualmente nel servizio Data Factory.

## <a name="advantages-of-git-integration"></a>Vantaggi dell'integrazione con GIT

-   **Controllo del codice sorgente**. Man mano che i carichi di lavoro della data factory assumono un'importanza cruciale, può essere opportuno integrare la factory con GIT per sfruttare alcuni vantaggi del controllo del codice sorgente, come i seguenti:
    -   Possibilità di controllare le modifiche e tenerne traccia.
    -   Possibilità di annullare le modifiche che hanno introdotto bug.
-   **Salvataggi parziali**. Quando si apportano numerose modifiche nella factory, si noterà che nella normale modalità LIVE non è possibile salvare le modifiche come bozza perché non si è pronti o non si vogliono perdere le modifiche in caso di arresto anomalo del computer. Grazie all'integrazione con GIT, è possibile continuare a salvare le modifiche in modo incrementale ed eseguire la pubblicazione nella factory solo quando si è pronti. GIT funziona come un punto di gestione temporanea finché le modifiche non sono state testate e risultano soddisfacenti.
-   **Collaborazione e controllo**. Se più membri del team partecipano alla stessa factory, può essere opportuno offrire loro l'opportunità di collaborare tramite un processo di revisione del codice. È anche possibile configurare la factory in modo che non tutti i collaboratori abbiano le autorizzazioni per eseguire distribuzioni nella factory. I membri del team potrebbero essere autorizzati solo ad apportare modifiche tramite GIT, mentre solo determinati utenti potrebbero disporre dell'autorizzazione a "pubblicare" le modifiche nella factory.
-   **Visualizzazione delle differenze**. In modalità GIT è possibile visualizzare un diff del payload che sta per essere pubblicato nella factory. Il diff mostra tutte le risorse o le entità che sono state modificate, aggiunte o eliminate dall'ultima pubblicazione nella factory. In base a questo diff, è possibile proseguire con la pubblicazione o tornare indietro e controllare le modifiche prima di riprendere il processo.
-   **Integrazione continua/Distribuzione continua più efficaci**. Se si usa la modalità GIT, è possibile configurare la pipeline di versione in modo che venga attivata automaticamente non appena vengono apportate modifiche nella factory di sviluppo. È anche possibile personalizzare le proprietà nella factory che sono disponibili come parametri nel modello di Resource Manager. Ciò può essere utile per mantenere come parametri solo il set di proprietà richiesto e impostare tutto il resto come hardcoded.
-   **Prestazioni migliori**. An average factory loads ten times faster in Git mode than in regular LIVE mode, because the resources are downloaded via Git.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

### <a name="permissions"></a>autorizzazioni

Typically you don’t want every team member to have permissions to update the factory. The following permissions settings are recommended:

*   Tutti i membri del team devono avere le autorizzazioni di accesso in lettura alla data factory.
*   Only a select set of people should be allowed to publish to the factory. To do so, they must have the **Data Factory contributor** role on the factory. For more information on permissions, see [Roles and permissions for Azure Data Factory](concepts-roles-permissions.md).
   
It's recommended to not allow direct check-ins to the collaboration branch. This restriction can help prevent bugs as every check-in will go through a pull request review process described in [Creating feature branches](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Using passwords from Azure Key Vault

its recommended to use Azure Key Vault to store any connection strings or passwords for Data Factory Linked Services. For security reasons, we don’t store any such secret information in Git, so any changes to Linked Services are published immediately to the Azure Data Factory service.

Using Key Vault also makes continuous integration and deployment easier as you will not have to provide these secrets during Resource Manager template deployment.

## <a name="troubleshooting-git-integration"></a>Troubleshooting Git integration

### <a name="stale-publish-branch"></a>Stale publish branch

If the publish branch is out of sync with the master branch and contains out-of-date resources despite a recent publish, try following these steps:

1. Remove your current Git repository
1. Reconfigure Git with the same settings, but make sure **Import existing Data Factory resources to repository** is selected and choose **New branch**
1. Delete all resources from your collaboration branch
1. Create a pull request to merge the changes to the collaboration branch 

## <a name="provide-feedback"></a>Invia commenti e suggerimenti
Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
* To implement continuous integration and deployment, see [Continuous integration and delivery (CI/CD) in Azure Data Factory](continuous-integration-deployment.md).
