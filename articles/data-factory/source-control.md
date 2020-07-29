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
ms.date: 04/30/2020
ms.openlocfilehash: 2d6f667b6a49520dfe210fd797a828328899b634
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674585"
---
# <a name="source-control-in-azure-data-factory"></a>Controllo del codice sorgente in Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Per impostazione predefinita, l'esperienza dell'interfaccia utente di Azure Data Factory consente di creare direttamente nel servizio Data Factory. Questa esperienza presenta le limitazioni seguenti:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche. L'unico modo per salvare le modifiche è tramite il pulsante **Pubblica tutto**, che consente di pubblicare tutte le modifiche direttamente nel servizio Data Factory.
- Il servizio Data Factory non è ottimizzato per la collaborazione e il controllo della versione.

Per fornire una migliore esperienza di creazione, Azure Data Factory consente di configurare un repository Git con Azure Repos o GitHub. Git è un sistema di controllo della versione che consente di rilevare le modifiche e collaborare in modo più semplice. Questa esercitazione descrive come configurare e lavorare in un repository Git, inoltre indica le procedure consigliate e fornisce una guida per la risoluzione dei problemi.

> [!NOTE]
> L'integrazione con Git di Azure Data Factory non è disponibile nel cloud di Azure per enti pubblici.

## <a name="advantages-of-git-integration"></a>Vantaggi dell'integrazione con GIT

Di seguito è riportato un elenco di alcuni dei vantaggi offerti dall'integrazione con Git relativamente all'esperienza di creazione:

-   **Controllo del codice sorgente:** Man mano che i carichi di lavoro della data factory assumono un'importanza cruciale, può essere opportuno integrare la factory con GIT per sfruttare alcuni vantaggi del controllo del codice sorgente, come i seguenti:
    -   Possibilità di controllare le modifiche e tenerne traccia.
    -   Possibilità di annullare le modifiche che hanno introdotto bug.
-   **Salvataggi parziali:** quando si esegue la creazione con il servizio Data Factory, non è possibile salvare le modifiche come bozza e tutte le pubblicazioni devono superare la convalida di Data Factory. Se le pipeline non sono state completate o semplicemente non si vogliono perdere le modifiche in caso di arresto anomalo del sistema, l'integrazione con Git consente di apportare modifiche incrementali alle risorse di Data Factory indipendentemente dallo stato in cui si trovano. La configurazione di un repository Git consente di salvare le modifiche in modo da poter procedere alla pubblicazione solo dopo aver testato le modifiche apportate in base alle proprie esigenze.
-   **Collaborazione e controllo:** se più membri del team contribuiscono alla stessa factory, può essere opportuno consentirgli di collaborare tramite un processo di revisione del codice. È possibile configurare la factory in modo che non tutti i collaboratori dispongano delle stesse autorizzazioni. Alcuni membri del team potrebbero essere autorizzati solo ad apportare modifiche tramite Git, mentre solo alcuni del team potrebbero disporre dell'autorizzazione per pubblicare le modifiche nella factory.
-   **CI/CD più efficaci:**  se si esegue la distribuzione in più ambienti con un [processo di recapito continuo](continuous-integration-deployment.md), l'integrazione con Git semplifica determinate azioni. Di seguito sono riportate alcune di queste azioni.
    -   Configurare la pipeline di versione in modo che venga attivata automaticamente non appena si apportano modifiche alla factory di sviluppo.
    -   Personalizzare le proprietà nella factory che sono disponibili come parametri nel modello di Resource Manager. Ciò può essere utile per mantenere come parametri solo il set di proprietà richiesto e impostare tutto il resto come hardcoded.
-   **Prestazioni migliori:** una factory media integrata con Git carica 10 volte più velocemente rispetto a una creazione basata sul servizio Data Factory. Questo miglioramento delle prestazioni è dovuto al fatto che le risorse vengono scaricate tramite Git.

> [!NOTE]
> La creazione diretta con il servizio Data Factory è disabilitata nell'esperienza utente di Azure Data Factory quando viene configurato un repository Git. Le modifiche possono essere apportate direttamente al servizio tramite PowerShell o un SDK.

## <a name="author-with-azure-repos-git-integration"></a>Creare con l'integrazione di GIT Azure Repos

La soluzione che prevede l'uso dell'integrazione di GIT Azure Repos supporta funzionalità per il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. È possibile associare una data factory a un repository di organizzazione GIT Azure Repos per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Una singola organizzazione GIT Azure Repos può avere più repository, ma un repository GIT Azure Repos può essere associato a una sola data factory. Se non si ha un'organizzazione o un repository Azure Repos, seguire [queste istruzioni](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare le risorse.

> [!NOTE]
> È possibile archiviare file di script e di dati in un repository GIT Azure Repos. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di dati o di script archiviati in un repository GIT Azure Repos.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurare un repository GIT Azure Repos con Azure Data Factory

È possibile configurare un repository GIT Azure Repos con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metodo di configurazione 1: home page di Azure Data Factory

Nella home page di Azure Data Factory selezionare **Configura repository del codice**.

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente
Nell'area di disegno di creazione dell'esperienza utente di Azure Data Factory selezionare il menu a discesa **Data Factory**, quindi selezionare **Configura repository del codice**.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Entrambi i metodi consentono di visualizzare il riquadro di configurazione delle impostazioni del repository.

![Configurare le impostazioni del repository di codice](media/author-visually/repo-settings.png)

Il riquadro di configurazione visualizza le impostazioni del repository del codice degli Azure Repos seguenti:

| Impostazione | Descrizione | valore |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps Git o GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Organizzazione Azure Repos** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Nome progetto** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. L'impostazione predefinita è `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT Azure Repos. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

> [!NOTE]
> Se si usa Microsoft Edge e non vengono visualizzati valori nell'elenco a discesa dell'account Azure DevOps, aggiungere https://*.visualstudio.com all'elenco dei siti attendibili.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usare un tenant di Azure Active Directory diverso

Il repository Azure Repos Git può essere posizionato in un diverso tenant di Azure Active Directory. Per specificare un tenant di Azure AD diverso, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata.

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

Per configurare un repository GitHub, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata.

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurare un repository GitHub con Azure Data Factory

È possibile configurare un repository GitHub con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metodo di configurazione 1: home page di Azure Data Factory

Nella home page di Azure Data Factory selezionare **Configura repository del codice**.

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente

Nell'area di disegno di creazione dell'esperienza utente di Azure Data Factory selezionare il menu a discesa **Data Factory**, quindi selezionare **Configura repository del codice**.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

Entrambi i metodi consentono di visualizzare il riquadro di configurazione delle impostazioni del repository.

![Impostazioni del repository GitHub](media/author-visually/github-integration-image2.png)

Il riquadro di configurazione visualizza le impostazioni seguenti del repository GitHub:

| **Impostazione** | **Descrizione**  | **Valore**  |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos. | GitHub |
| **Use GitHub Enterprise** (Usa GitHub Enterprise) | Casella di controllo per selezionare GitHub Enterprise | non selezionato (impostazione predefinita) |
| **GitHub Enterprise URL** (URL GitHub Enterprise) | URL radice di GitHub Enterprise (deve essere HTTPS per il server GitHub Enterprise locale). Ad esempio: `https://github.mydomain.com`. Obbligatorio solo se si seleziona **Usa GitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Account GitHub** | Nome dell'account GitHub. Questo nome è indicato nella pagina https:\//github.com/{nome account}/{nome repository}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub. | `<your GitHub account name>` |
| **Nome repository**  | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account. | `<your repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, si tratta del ramo master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'area di disegno di creazione dell'esperienza utente in un repository GitHub. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

### <a name="known-github-limitations"></a>Limitazioni note di GitHub

- È possibile archiviare file di script e di dati in un repository GitHub. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di script o di dati archiviati in un repository GitHub.

- GitHub Enterprise in una versione precedente alla 2.14.0 non funziona nel browser Microsoft Edge.

- L'integrazione di GitHub con gli strumenti di creazione di oggetti visivi di Data Factory funziona solo nella versione di Data Factory disponibile a livello generale.

- È possibile recuperare un massimo di 1.000 entità per tipo di risorsa (ad esempio pipeline e set di dati) da un singolo ramo GitHub. Se questo limite viene raggiunto, è consigliabile suddividere le risorse in factory distinte. Azure DevOps Git non presenta questa limitazione.

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

### <a name="creating-feature-branches"></a>Creazione di rami di funzionalità

Ogni repository GIT Azure Repos associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Gli utenti possono creare rami di funzionalità facendo clic su **Aggiungi nuovo ramo** nel menu a discesa dei rami. Quando viene visualizzato il riquadro del nuovo ramo, immettere il nome del ramo di funzionalità.

![Creare un nuovo ramo](media/author-visually/new-branch.png)

Quando si è pronti per unire le modifiche dal ramo di funzionalità al ramo di collaborazione, fare clic sull'elenco a discesa del ramo e selezionare **Crea richiesta pull**. Viene visualizzata la pagina GIT Azure Repos, in cui è possibile generare richieste di pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per impostazione predefinita, Data Factory genera i modelli di Resource Manager della factory pubblicata e li salva in un ramo denominato `adf_publish`. Per configurare un ramo di pubblicazione personalizzato, aggiungere un file `publish_config.json` alla cartella radice nel ramo di collaborazione. Durante la pubblicazione ADF legge questo file, cerca il campo `publishBranch` e salva tutti i modelli di Resource Manager nella posizione specificata. Se il ramo non esiste, viene automaticamente creato da Data Factory. Un esempio dell'aspetto del file è il seguente:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory può avere un unico ramo di pubblicazione alla volta. Quando si specifica un nuovo ramo di pubblicazione, Data Factory non elimina il ramo di pubblicazione precedente. Se si intende rimuovere il ramo di pubblicazione precedente, eliminarlo manualmente.

> [!NOTE]
> Data Factory legge il file `publish_config.json` solo al momento del caricamento della factory. Se la factory è già caricata nel portale, aggiornare il browser per rendere effettive le modifiche.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

Dopo aver unito le modifiche nel ramo di collaborazione (`master` è l'impostazione predefinita), fare clic su **Pubblica** per pubblicare manualmente le modifiche al codice nel ramo master del servizio Data Factory.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

Si apre un riquadro laterale in cui l'utente conferma che il ramo di pubblicazione e le modifiche in sospeso sono corretti. Dopo aver verificato le modifiche, fare clic su **OK** per confermare la pubblicazione.

![Confermare il ramo di pubblicazione corretto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Il ramo principale non è rappresentativo di ciò che viene distribuito nel servizio Data Factory. Il ramo master *deve* essere pubblicato manualmente nel servizio Data Factory.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

### <a name="permissions"></a>Autorizzazioni

In genere è preferibile che non tutti i membri del team dispongano delle autorizzazioni per aggiornare la factory. Sono consigliate le impostazioni di autorizzazione seguenti:

*   Tutti i membri del team devono avere le autorizzazioni di accesso in lettura alla data factory.
*   Solo un gruppo selezionato di persone dovrebbe essere autorizzato a pubblicare nella factory. A tale scopo, è necessario che dispongano del ruolo di **collaboratore di Data Factory** nel gruppo di risorse in cui si trova la factory. Per altre informazioni sulle autorizzazioni, vedere [Ruoli e autorizzazioni per Azure Data Factory](concepts-roles-permissions.md).
   
Si consiglia di non consentire le archiviazioni dirette nel ramo collaborazione. Questa limitazione può aiutare a prevenire i bug in quanto ogni archiviazione passa attraverso un processo di revisione delle richieste pull descritto in [Creazione di rami di funzionalità](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Uso di password da Azure Key Vault

Si consiglia di usare Azure Key Vault per archiviare le stringhe di connessione, le password o l'autenticazione dell'identità gestita per i servizi collegati di Data Factory. Per motivi di sicurezza, Data Factory non archivia i segreti in Git. Tutte le modifiche apportate ai servizi collegati contenenti segreti, ad esempio le password, vengono pubblicate immediatamente nel servizio Azure Data Factory.

L'uso di Key Vault o dell'autenticazione MSI rende inoltre più semplice l'integrazione e la distribuzione continue, in quanto non è necessario fornire questi segreti durante la distribuzione del modello di Resource Manager.

## <a name="troubleshooting-git-integration"></a>Risoluzione dei problemi di integrazione con Git

### <a name="stale-publish-branch"></a>Ramo di pubblicazione non aggiornato

Se il ramo di pubblicazione non è sincronizzato con il ramo master e contiene risorse non aggiornate nonostante una pubblicazione recente, provare con i passaggi seguenti:

1. Rimuovere il repository Git corrente
1. Riconfigurare Git con le stesse impostazioni, ma verificando di aver selezionato **Importa risorse di Data Factory esistenti nel repository** e di scegliere **Nuovo ramo**
1. Creare una richiesta pull per unire le modifiche al ramo di collaborazione 

Di seguito sono riportati alcuni esempi di situazioni che possono creare il problema di un ramo di pubblicazione non aggiornato:
- Un utente presenta più rami. In un ramo di funzionalità ha eliminato un servizio collegato non associato ad AKV (i servizi associati non AKV vengono pubblicati immediatamente indipendentemente dal fatto che si trovino o meno in Git) e non ha mai unito il ramo di funzionalità al ramo di collaborazione.
- Un utente ha modificato il data factory usando l'SDK o PowerShell
- Un utente ha spostato tutte le risorse in un nuovo ramo e ha tentato di eseguire la pubblicazione per la prima volta. I servizi collegati devono essere creati manualmente durante l'importazione delle risorse.
- Un utente carica manualmente un servizio collegato non AKV o un JSON di Integration Runtime. Fa riferimento a quella risorsa da una risorsa diversa come un set di dati, un servizio collegato o una pipeline. Un servizio collegato non AKV creato tramite l'esperienza utente viene pubblicato immediatamente poiché le credenziali devono essere crittografate. Se si carica un set di dati che fa riferimento a tale servizio collegato e si tenta di pubblicarlo, l'esperienza utente lo consentirà perché esiste nell'ambiente git. Verrà rifiutato in fase di pubblicazione poiché non esiste nel servizio Data Factory.

## <a name="switch-to-a-different-git-repository"></a>Passare a un repository Git diverso

Per passare a un repository Git diverso, fare clic sull'icona **Impostazioni repository Git** nell'angolo superiore destro della pagina di panoramica di Data Factory. Se l'icona non è visibile, cancellare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

![Icona Git](media/author-visually/remove-repo.png)

Dopo aver visualizzato il riquadro Impostazioni repository, selezionare **Rimuovi Git**. Immettere il nome del data factory e fare clic su **Conferma** per rimuovere il repository Git associato al data factory.

![Rimuovere l'associazione al repository Git corrente](media/author-visually/remove-repo2.png)

Dopo aver rimosso l'associazione al repository corrente è possibile configurare le impostazioni di Git per usare un repository diverso e quindi importare le risorse di Data Factory esistenti nel nuovo repository.

> [!IMPORTANT]
> La rimozione della configurazione Git da un data factory non comporta l'eliminazione di elementi dal repository. La factory conterrà tutte le risorse pubblicate. È possibile continuare a modificare la factory direttamente nel servizio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
* Per implementare l'integrazione e la distribuzione continue, vedere [Integrazione e recapito continui (CI/CD) in Azure Data Factory](continuous-integration-deployment.md).
