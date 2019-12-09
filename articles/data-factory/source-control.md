---
title: Controllo del codice sorgente
description: Informazioni su come configurare il controllo del codice sorgente in Azure Data Factory
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
ms.openlocfilehash: 1f31bb78fced6db2a26559cbd098407823161dfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928775"
---
# <a name="source-control-in-azure-data-factory"></a>Controllo del codice sorgente in Azure Data Factory

L'esperienza dell'interfaccia utente Azure Data Factory (UX) offre due esperienze per la creazione di oggetti visivi:

- Creare direttamente con il servizio Data Factory
- Crea con Azure Repos integrazione git o GitHub

> [!NOTE]
> Il cloud di Azure per enti pubblici supporta solo la creazione diretta con il servizio Data Factory.

## <a name="author-directly-with-the-data-factory-service"></a>Creare direttamente con il servizio Data Factory

Quando si crea direttamente con il servizio Data Factory, l'unico modo per salvare le modifiche è tramite il pulsante **pubblica tutti** . Una volta fatto clic, tutte le modifiche apportate vengono pubblicate direttamente nel servizio Data Factory. 

![Modalità Pubblica](media/author-visually/data-factory-publish.png)

La creazione diretta con il servizio Data Factory presenta le limitazioni seguenti:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche.
- Il servizio Data Factory non è ottimizzato per la collaborazione o il controllo della versione.

> [!NOTE]
> La creazione diretta con il servizio Data Factory è disabilitata nel Azure Data Factory UX quando viene configurato un repository git. Le modifiche possono essere apportate direttamente al servizio tramite PowerShell o un SDK.

## <a name="author-with-azure-repos-git-integration"></a>Creare con l'integrazione di GIT Azure Repos

La soluzione che prevede l'uso dell'integrazione di GIT Azure Repos supporta funzionalità per il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. È possibile associare una data factory a un repository di organizzazione GIT Azure Repos per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Una singola organizzazione GIT Azure Repos può avere più repository, ma un repository GIT Azure Repos può essere associato a una sola data factory. Se non si ha un'organizzazione o un repository Azure Repos, seguire [queste istruzioni](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare le risorse.

> [!NOTE]
> È possibile archiviare file di script e di dati in un repository GIT Azure Repos. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di dati o di script archiviati in un repository GIT Azure Repos.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurare un repository GIT Azure Repos con Azure Data Factory

È possibile configurare un repository GIT Azure Repos con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metodo di configurazione 1: Azure Data Factory home page

Nel home page Azure Data Factory selezionare **Configura repository di codice**.

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente
Nell'area di disegno di creazione dell'esperienza utente Azure Data Factory selezionare il menu a discesa **Data Factory** , quindi selezionare **Configura repository di codice**.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Entrambi i metodi aprono il riquadro di configurazione delle impostazioni del repository.

![Configurare le impostazioni del repository di codice](media/author-visually/repo-settings.png)

Il riquadro Configurazione Mostra le seguenti impostazioni del repository di codice Azure Repos:

| Impostazione | Description | Value |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps git o GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Organizzazione Azure Repos** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Nome progetto** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. Per impostazione predefinita, è `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT Azure Repos. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

> [!NOTE]
> Se si usa Microsoft Edge e non vengono visualizzati valori nell'elenco a discesa dell'account Azure DevOps, aggiungere https://*. VisualStudio. com all'elenco siti attendibili.

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

Per configurare un repository GitHub, è necessario disporre delle autorizzazioni di amministratore per la sottoscrizione di Azure in uso.

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurare un repository GitHub con Azure Data Factory

È possibile configurare un repository GitHub con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metodo di configurazione 1: Azure Data Factory home page

Nel home page Azure Data Factory selezionare **Configura repository di codice**.

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente

Nell'area di disegno di creazione dell'esperienza utente Azure Data Factory selezionare il menu a discesa **Data Factory** , quindi selezionare **Configura repository di codice**.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Entrambi i metodi aprono il riquadro di configurazione delle impostazioni del repository.

![Impostazioni del repository GitHub](media/author-visually/github-integration-image2.png)

Il riquadro Configurazione Mostra le impostazioni del repository GitHub seguenti:

| **Impostazione** | **Descrizione**  | **Valore**  |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos. | GitHub |
| **Use GitHub Enterprise** (Usa GitHub Enterprise) | Casella di controllo per selezionare GitHub Enterprise | non selezionato (impostazione predefinita) |
| **GitHub Enterprise URL** (URL GitHub Enterprise) | URL radice di GitHub Enterprise. Ad esempio: https://github.mydomain.com. Obbligatorio solo se è selezionata l'opzione **usa github Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Account GitHub** | Nome dell'account GitHub. Questo nome può essere trovato da https:\//github.com/{account nome}/{repository}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub. | `<your GitHub account name>` |
| **Nome del repository**  | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account. | `<your repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, è master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di data factory esistenti dall'area di disegno di creazione dell'esperienza utente in un repository GitHub. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

### <a name="known-github-limitations"></a>Limitazioni note di GitHub

- È possibile archiviare file di script e di dati in un repository GitHub. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di script o di dati archiviati in un repository GitHub.

- GitHub Enterprise in una versione precedente alla 2.14.0 non funziona nel browser Microsoft Edge.

- L'integrazione di GitHub con gli strumenti di creazione visiva Data Factory funziona solo nella versione disponibile a livello generale di Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Cambiare repository Git

Per passare a un repository git diverso, fare clic sull'icona **delle impostazioni del repository git** nell'angolo superiore destro della pagina Panoramica data factory. Se l'icona non è visibile, cancellare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

![Icona Git](media/author-visually/remove-repo.png)

Quando viene visualizzato il riquadro Impostazioni repository, selezionare **Rimuovi git**. Immettere il nome del data factory e fare clic su **conferma** per rimuovere il repository git associato al data factory.

![Rimuovere l'associazione al repository Git corrente](media/author-visually/remove-repo2.png)

Dopo la rimozione dell'associazione con il repository corrente, è possibile configurare le impostazioni git per l'uso di un repository diverso e quindi importare le risorse di Data Factory esistenti nel nuovo repository. 

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

### <a name="creating-feature-branches"></a>Creazione di rami di funzionalità

Ogni repository GIT Azure Repos associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Gli utenti possono anche creare rami di funzionalità facendo clic su **+ nuovo ramo** nell'elenco a discesa del ramo. Quando viene visualizzato il riquadro nuovo ramo, immettere il nome del ramo della funzionalità.

![Creare un nuovo ramo](media/author-visually/new-branch.png)

Quando si è pronti per eseguire il merge delle modifiche dal ramo funzionalità al ramo di collaborazione, fare clic sull'elenco a discesa Branch e selezionare **Crea richiesta pull**. Viene visualizzata la pagina GIT Azure Repos, in cui è possibile generare richieste di pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per configurare il ramo di pubblicazione, vale a dire, il ramo in cui vengono salvati i modelli di Resource Manager, aggiungere un file `publish_config.json` nella cartella radice nel ramo di collaborazione. Data Factory legge questo file, cerca il campo `publishBranch` e crea un nuovo ramo (se non esiste già) con il valore specificato. Quindi salva tutti i modelli di Resource Manager nel percorso specificato. ad esempio:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando si specifica un nuovo ramo di pubblicazione, Data Factory non elimina il ramo di pubblicazione precedente. Se si vuole rimuovere il ramo di pubblicazione precedente, eliminarlo manualmente.

> [!NOTE]
> Data Factory legge il file `publish_config.json` solo al momento del caricamento della factory. Se la factory è già caricata nel portale, aggiornare il browser per rendere effettive le modifiche.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

Dopo aver unito le modifiche al ramo collaborazione (`master` è l'impostazione predefinita), fare clic su **pubblica** per pubblicare manualmente le modifiche del codice nel ramo master nel servizio Data Factory.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

Verrà aperto un riquadro laterale in cui si conferma che il ramo di pubblicazione e le modifiche in sospeso sono corretti. Una volta verificate le modifiche, fare clic su **OK** per confermare la pubblicazione.

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
-   **Prestazioni migliori**. Una factory Media carica dieci volte più velocemente in modalità git rispetto alla modalità LIVE normale, perché le risorse vengono scaricate tramite git.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

### <a name="permissions"></a>autorizzazioni

In genere, non è necessario che ogni membro del team disponga delle autorizzazioni per aggiornare la factory. Sono consigliate le seguenti impostazioni di autorizzazione:

*   Tutti i membri del team devono avere le autorizzazioni di accesso in lettura alla data factory.
*   Solo un set selezionato di persone dovrebbe essere autorizzato a pubblicare nella Factory. A tale scopo, devono avere il ruolo di **collaboratore Data Factory** nella Factory. Per ulteriori informazioni sulle autorizzazioni, vedere [ruoli e autorizzazioni per Azure Data Factory](concepts-roles-permissions.md).
   
Si consiglia di non consentire le archiviazioni dirette al ramo collaborazione. Questa restrizione può aiutare a prevenire i bug quando ogni archiviazione passa attraverso un processo di revisione delle richieste pull descritto in [creazione di rami di funzionalità](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Utilizzo di password da Azure Key Vault

È consigliabile usare Azure Key Vault per archiviare le stringhe di connessione o le password per Data Factory servizi collegati. Per motivi di sicurezza, queste informazioni segrete non vengono archiviate in git, quindi le modifiche apportate ai servizi collegati vengono pubblicate immediatamente nel servizio Azure Data Factory.

L'uso di Key Vault rende inoltre più semplice l'integrazione e la distribuzione continue, perché non sarà necessario fornire questi segreti durante la distribuzione del modello Gestione risorse.

## <a name="troubleshooting-git-integration"></a>Risoluzione dei problemi di integrazione con git

### <a name="stale-publish-branch"></a>Ramo di pubblicazione non aggiornato

Se il ramo di pubblicazione non è sincronizzato con il ramo master e contiene risorse non aggiornate nonostante una pubblicazione recente, provare a seguire questa procedura:

1. Rimuovere il repository git corrente
1. Riconfigurare git con le stesse impostazioni, ma assicurarsi che **Importa le risorse di data factory esistenti nel repository** sia selezionato e scegliere **nuovo ramo**
1. Elimina tutte le risorse dal ramo di collaborazione
1. Creare una richiesta pull per unire le modifiche al ramo di collaborazione 

## <a name="provide-feedback"></a>Invia commenti e suggerimenti
Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
* Per implementare l'integrazione e la distribuzione continue, vedere [integrazione continua e recapito continuo (ci/CD) in Azure Data Factory](continuous-integration-deployment.md).
