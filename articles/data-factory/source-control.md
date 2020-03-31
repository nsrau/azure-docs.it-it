---
title: Controllo del codice sorgente
description: Informazioni su come configurare il controllo del codice sorgente in Azure Data FactoryLearn how to configure source control in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 6645c2672e15c562216b4347f779ef3634a2f124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130861"
---
# <a name="source-control-in-azure-data-factory"></a>Controllo del codice sorgente in Azure Data FactorySource control in Azure Data Factory

L'esperienza dell'interfaccia utente di Azure Data Factory (UX) ha due esperienze disponibili per la creazione visiva:The Azure Data Factory user interface experience (UX) has two experiences available for visual authoring:

- Creare direttamente con il servizio Data Factory
- Creare con l'integrazione di Azure Repos Git o GitHub

> [!NOTE]
> Solo la creazione diretta con il servizio Data Factory è supportata nel cloud di Azure per enti pubblici.

## <a name="author-directly-with-the-data-factory-service"></a>Creare direttamente con il servizio Data Factory

Durante la creazione diretta con il servizio Data Factory, l'unico modo per salvare le modifiche è tramite il pulsante **Pubblica tutto.** Dopo aver fatto clic, tutte le modifiche apportate vengono pubblicate direttamente nel servizio Data Factory. 

![Modalità Pubblica](media/author-visually/data-factory-publish.png)

La creazione diretta con il servizio Data Factory presenta le limitazioni seguenti:Authoring directly with the Data Factory service has the following limitations:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche.
- Il servizio Data Factory non è ottimizzato per la collaborazione o il controllo della versione.

> [!NOTE]
> La creazione diretta con il servizio Data Factory è disabilitata nell'esperienza utente di Azure Data Factory quando viene configurato un repository Git.Authoring directly with the Data Factory service is disabled in the Azure Data Factory UX when a Git repository is configured. Le modifiche possono essere apportate direttamente al servizio tramite PowerShell o un SDK.

## <a name="author-with-azure-repos-git-integration"></a>Creare con l'integrazione di GIT Azure Repos

La soluzione che prevede l'uso dell'integrazione di GIT Azure Repos supporta funzionalità per il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. È possibile associare una data factory a un repository di organizzazione GIT Azure Repos per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Una singola organizzazione GIT Azure Repos può avere più repository, ma un repository GIT Azure Repos può essere associato a una sola data factory. Se non si ha un'organizzazione o un repository Azure Repos, seguire [queste istruzioni](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare le risorse.

> [!NOTE]
> È possibile archiviare file di script e di dati in un repository GIT Azure Repos. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di dati o di script archiviati in un repository GIT Azure Repos.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurare un repository GIT Azure Repos con Azure Data Factory

È possibile configurare un repository GIT Azure Repos con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metodo di configurazione 1: home page di Azure Data FactoryConfiguration method 1: Azure Data Factory home page

Nella home page di Azure Data Factory selezionare **Configura repository di**codice .

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente
Nel canvas di creazione dell'esperienza utente di Azure Data Factory selezionare il menu a discesa **Data Factory** e quindi **Selezionare Configura repository di**codice.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Entrambi i metodi aprono il riquadro di configurazione delle impostazioni del repository.

![Configurare le impostazioni del repository di codice](media/author-visually/repo-settings.png)

Il riquadro di configurazione mostra le seguenti impostazioni del repository del codice di Azure Repos:The configuration pane shows the following Azure Repos code repository settings:

| Impostazione | Descrizione | valore |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps Git or GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Organizzazione Azure Repos** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Projectname** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome Repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. Per impostazione predefinita, è `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT Azure Repos. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

> [!NOTE]
> Se si usa Microsoft Edge e non viene visualizzato alcun valore nell'elenco a discesa Account DevOps di Azure, aggiungere https://.visualstudio.com all'elenco dei siti attendibili.

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

La soluzione che prevede l'uso dell'integrazione di GitHub supporta funzionalità per il controllo del codice sorgente e la collaborazione per le attività sulle pipeline di data factory. È possibile associare una data factory a un repository di account GitHub per il controllo del codice sorgente, la collaborazione e il controllo delle versioni. Un singolo account GitHub può avere più repository, ma un repository GitHub può essere associato a una sola data factory. Se non si dispone di un account GitHub o di un repository, seguire [queste istruzioni](https://github.com/join) per creare le risorse.

L'integrazione di GitHub con Data Factory supporta [https://github.com](https://github.com)sia GitHub pubblico (ovvero) che GitHub Enterprise. È possibile usare i repository GitHub pubblici e privati con Data Factory purché siano state ottenute le autorizzazioni di lettura e scrittura nel repository in GitHub.

Per configurare un repository GitHub, è necessario disporre delle autorizzazioni di amministratore per la sottoscrizione di Azure in uso.

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurare un repository GitHub con Azure Data FactoryConfigure a GitHub repository with Azure Data Factory

È possibile configurare un repository GitHub con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metodo di configurazione 1: home page di Azure Data FactoryConfiguration method 1: Azure Data Factory home page

Nella home page di Azure Data Factory selezionare **Configura repository di**codice .

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente

Nel canvas di creazione dell'esperienza utente di Azure Data Factory selezionare il menu a discesa **Data Factory** e quindi **Selezionare Configura repository di**codice.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Entrambi i metodi aprono il riquadro di configurazione delle impostazioni del repository.

![Impostazioni del repository GitHub](media/author-visually/github-integration-image2.png)

Il riquadro di configurazione mostra le seguenti impostazioni del repository GitHub:

| **Impostazione** | **Descrizione**  | **Valore**  |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos. | GitHub |
| **Use GitHub Enterprise** (Usa GitHub Enterprise) | Casella di controllo per selezionare GitHub Enterprise | non selezionato (impostazione predefinita) |
| **GitHub Enterprise URL** (URL GitHub Enterprise) | L'URL radice gitHub Enterprise (deve essere HTTPS per il server GitHub Enterprise locale). Ad esempio https://github.mydomain.com. Obbligatorio solo se è selezionata l'opzione **Usa GitHub EnterpriseRequired** only if Use GitHub Enterprise is selected | `<your GitHub enterprise url>` |                                                           
| **Account GitHub** | Nome dell'account GitHub. Questo nome è reperibile da https:\//github.com/ . Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub. | `<your GitHub account name>` |
| **Nome repository**  | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account. | `<your repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, è master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di data factory esistenti dal canvas di creazione dell'esperienza utente in un repository GitHub. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

### <a name="known-github-limitations"></a>Limitazioni note di GitHub

- È possibile archiviare file di script e di dati in un repository GitHub. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di script o di dati archiviati in un repository GitHub.

- GitHub Enterprise in una versione precedente alla 2.14.0 non funziona nel browser Microsoft Edge.

- GitHub integration with the Data Factory visual authoring tools only works in the generally available version of Data Factory.

- Un massimo di 1.000 entità per tipo di risorsa (ad esempio pipeline e set di dati) può essere recuperato da un singolo ramo GitHub. Se viene raggiunto questo limite, si consiglia di dividere le risorse in fabbriche separate. Azure DevOps Git non ha questa limitazione.

## <a name="switch-to-a-different-git-repo"></a>Cambiare repository Git

Per passare a un repository Git diverso, fare clic sull'icona **Impostazioni repository Git** nell'angolo superiore destro della pagina Panoramica di Data Factory. Se l'icona non è visibile, cancellare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

![Icona GIT](media/author-visually/remove-repo.png)

Quando viene visualizzato il riquadro Impostazioni repository, selezionare **Rimuovi Git**. Immettere il nome della data factory e fare clic su **Conferma** per rimuovere il repository Git associato alla data factory.

![Rimuovere l'associazione al repository Git corrente](media/author-visually/remove-repo2.png)

Dopo aver rimosso l'associazione con il repository corrente, è possibile configurare le impostazioni Git per l'utilizzo di un repository diverso e quindi importare le risorse di Data Factory esistenti nel nuovo repository. 

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

### <a name="creating-feature-branches"></a>Creazione di rami di entità geografiche

Ogni repository GIT Azure Repos associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Gli utenti possono anche creare rami di entità geografiche facendo clic su **Nuovo ramo** nell'elenco a discesa del ramo. Quando viene visualizzato il nuovo riquadro di diramazione, immettere il nome del ramo della funzionalità.

![Creare un nuovo ramoCreate a new branch](media/author-visually/new-branch.png)

Quando si è pronti per unire le modifiche dal ramo di funzionalità al ramo di collaborazione, fare clic sull'elenco a discesa del ramo e selezionare **Crea richiesta pull**. Viene visualizzata la pagina GIT Azure Repos, in cui è possibile generare richieste di pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per configurare il ramo di pubblicazione, vale a dire, il ramo in cui vengono salvati i modelli di Resource Manager, aggiungere un file `publish_config.json` nella cartella radice nel ramo di collaborazione. Data Factory legge questo file, cerca il campo `publishBranch` e crea un nuovo ramo (se non esiste già) con il valore specificato. Quindi salva tutti i modelli di Resource Manager nel percorso specificato. Ad esempio:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando si specifica un nuovo ramo di pubblicazione, Data Factory non elimina il ramo di pubblicazione precedente. Se si desidera rimuovere il ramo di pubblicazione precedente, eliminarlo manualmente.

> [!NOTE]
> Data Factory legge il file `publish_config.json` solo al momento del caricamento della factory. Se la factory è già caricata nel portale, aggiornare il browser per rendere effettive le modifiche.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

Dopo aver unito le modifiche`master` al ramo di collaborazione (impostazione predefinita), fare clic su **Pubblica** per pubblicare manualmente le modifiche al codice nel ramo master nel servizio Data Factory.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

Verrà aperto un riquadro laterale in cui si conferma che il ramo di pubblicazione e le modifiche in sospeso siano corrette. Dopo aver verificato le modifiche, fare clic **su OK** per confermare la pubblicazione.

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
-   **Prestazioni migliori**. Una fabbrica media carica dieci volte più velocemente in modalità Git rispetto alla normale modalità LIVE, perché le risorse vengono scaricate tramite Git.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

### <a name="permissions"></a>Autorizzazioni

In genere non si desidera che ogni membro del team disponga delle autorizzazioni per aggiornare la factory. Si consigliano le seguenti impostazioni di autorizzazione:

*   Tutti i membri del team devono avere le autorizzazioni di accesso in lettura alla data factory.
*   Solo un gruppo selezionato di persone dovrebbe essere autorizzato a pubblicare in fabbrica. A tale scopo, devono disporre del ruolo di **collaboratore** di Data Factory nella factory. Per altre informazioni sulle autorizzazioni, vedere Ruoli e autorizzazioni per Azure Data Factory.For more information on permissions, see [Roles and permissions for Azure Data Factory.](concepts-roles-permissions.md)
   
Si consiglia di non consentire le archiviazioni dirette nel ramo di collaborazione. Questa restrizione consente di evitare bug man mano che ogni archiviazione passerà attraverso un processo di revisione delle richieste pull descritto in [Creazione di rami di funzionalità](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Uso delle password dall'insieme di credenziali delle chiavi di AzureUsing passwords from Azure Key Vault

È consigliabile usare l'insieme di credenziali delle chiavi di Azure per archiviare le stringhe di connessione o le password per i servizi collegati di Data Factory.It's recommended to use Azure Key Vault to store any connection strings or passwords for Data Factory Linked Services. Per motivi di sicurezza, non vengono archiviate tali informazioni segrete in Git, pertanto tutte le modifiche ai servizi collegati vengono pubblicate immediatamente nel servizio Azure Data Factory.For security reasons, we don't store such such secret information in Git, so any changes to Linked Services are published immediately to the Azure Data Factory service.

L'utilizzo dell'insieme di credenziali delle chiavi semplifica inoltre l'integrazione e la distribuzione continue, in quanto non sarà necessario fornire questi segreti durante la distribuzione del modello di Resource Manager.Using Key Vault also makes continuous integration and deployment easier as you will not have to provide these secrets during Resource Manager template deployment.

## <a name="troubleshooting-git-integration"></a>Risoluzione dei problemi di integrazione GitTroubleshooting Git integration

### <a name="stale-publish-branch"></a>Branchdia di pubblicazione non aggiornata

Se il ramo di pubblicazione non è sincronizzato con il ramo master e contiene risorse non aggiornate nonostante una pubblicazione recente, provare a eseguire la procedura seguente:

1. Rimuovere il repository Git corrente
1. Riconfigurare Git con le stesse impostazioni, ma assicurarsi che **l'opzione Importa risorse Data Factory esistenti** nel repository sia selezionata e scegliere Nuovo **ramo**
1. Creare una richiesta pull per unire le modifiche al ramo di collaborazioneCreate a pull request to merge the changes to the collaboration branch 

Di seguito sono riportati alcuni esempi di situazioni che possono causare un ramo di pubblicazione non aggiornato:Below are some examples of situations that can cause a stale publish branch:
- Un utente dispone di più rami. In un ramo di funzionalità, hanno eliminato un servizio collegato che non è associato a AKV (i servizi collegati non AKV vengono pubblicati immediatamente indipendentemente dal fatto che si trovino in Git o meno) e non hanno mai fuso il ramo di funzionalità nella relazione di collaborazione.
- Un utente ha modificato la data factory usando l'SDK o PowerShell
- Un utente ha spostato tutte le risorse in una nuova succursale e ha tentato di pubblicare per la prima volta. I servizi collegati devono essere creati manualmente durante l'importazione delle risorse.
- Un utente carica manualmente un servizio collegato non AKV o un JSON di Runtime di integrazione. Fanno riferimento a tale risorsa da un'altra risorsa, ad esempio un set di dati, un servizio collegato o una pipeline. Un servizio collegato non AKV creato tramite l'esperienza utente viene pubblicato immediatamente perché le credenziali devono essere crittografate. Se si carica un set di dati che fa riferimento a tale servizio collegato e si tenta di eseguire la pubblicazione, l'esperienza utente lo consentirà perché esiste nell'ambiente git. Verrà rifiutato in fase di pubblicazione poiché non esiste nel servizio data factory.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti e suggerimenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
* Per implementare l'integrazione e la distribuzione continue, vedere [Integrazione e recapito continuo (CI/CD) in Azure Data Factory.](continuous-integration-deployment.md)
