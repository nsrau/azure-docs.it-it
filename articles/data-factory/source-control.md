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
ms.openlocfilehash: f327844be57d7f8e177f3bf72b1e3b56c5147e00
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629338"
---
# <a name="source-control-in-azure-data-factory"></a>Controllo del codice sorgente in Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Per impostazione predefinita, il Azure Data Factory gli autori dell'esperienza dell'interfaccia utente direttamente nel servizio di data factory. Questa esperienza presenta le limitazioni seguenti:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche. L'unico modo per salvare le modifiche è tramite il pulsante **pubblica tutto** e tutte le modifiche vengono pubblicate direttamente nel servizio Data Factory.
- Il servizio Data Factory non è ottimizzato per la collaborazione e il controllo della versione.

Per offrire un'esperienza di creazione migliore, Azure Data Factory consente di configurare un repository git con Azure Repos o GitHub. Git è un sistema di controllo della versione che consente di semplificare il rilevamento e la collaborazione delle modifiche. Questa esercitazione descrive come configurare e lavorare in un repository git insieme alle procedure consigliate e a una guida alla risoluzione dei problemi.

> [!NOTE]
> L'integrazione di Azure data factory Git non è disponibile nel cloud di Azure per enti pubblici.

## <a name="advantages-of-git-integration"></a>Vantaggi dell'integrazione con GIT

Di seguito è riportato un elenco di alcuni dei vantaggi offerti dall'integrazione di git all'esperienza di creazione:

-   **Controllo del codice sorgente:** Man mano che i carichi di lavoro di data factory diventano cruciali, è necessario integrare la factory con git per sfruttare diversi vantaggi del controllo del codice sorgente come i seguenti:
    -   Possibilità di controllare le modifiche e tenerne traccia.
    -   Possibilità di annullare le modifiche che hanno introdotto bug.
-   **Salvataggi parziali:** Quando si esegue la creazione con il servizio data factory, non è possibile salvare le modifiche come bozza e tutte le pubblicazioni devono superare data factory convalida. Se le pipeline non sono state completate o semplicemente non si vuole perdere le modifiche in caso di arresto anomalo del computer, l'integrazione Git consente di apportare modifiche incrementali alle risorse data factory indipendentemente dallo stato in cui si trovano. La configurazione di un repository Git consente di salvare le modifiche, in modo da consentire la pubblicazione solo dopo aver testato le modifiche apportate alla propria soddisfazione.
-   **Collaborazione e controllo:** Se si hanno più membri del team che contribuiscono alla stessa Factory, è consigliabile consentire ai colleghi di collaborare tra loro tramite un processo di revisione del codice. È anche possibile configurare la factory in modo che non tutti i collaboratori dispongano delle stesse autorizzazioni. Alcuni membri del team possono solo essere autorizzati a apportare modifiche tramite git e solo a determinati utenti del team possono pubblicare le modifiche alla Factory.
-   **Ci/CD migliore:**  Se si esegue la distribuzione in più ambienti con un [processo di recapito continuo](continuous-integration-deployment.md), l'integrazione di git semplifica le operazioni. Di seguito sono riportate alcune di queste azioni.
    -   Configurare la pipeline di versione in modo che venga attivata automaticamente non appena sono state apportate modifiche alla Factory di "dev".
    -   Personalizzare le proprietà nella Factory disponibili come parametri nel modello di Gestione risorse. Ciò può essere utile per mantenere come parametri solo il set di proprietà richiesto e impostare tutto il resto come hardcoded.
-   **Prestazioni migliori:** Una factory media con integrazione git carica 10 volte più velocemente rispetto a una creazione in base al servizio data factory. Questo miglioramento delle prestazioni è dovuto al fatto che le risorse vengono scaricate tramite git.

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

| Impostazione | Descrizione | Valore |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps git o GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Organizzazione Azure Repos** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **ProjectName** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. Per impostazione predefinita, `master`il relativo oggetto. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT Azure Repos. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

> [!NOTE]
> Se si usa Microsoft Edge e non vengono visualizzati valori nell'elenco a discesa dell'account Azure DevOps, aggiungere https://*. VisualStudio. com all'elenco siti attendibili.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usare un tenant di Azure Active Directory diverso

Il repository git Azure Repos può trovarsi in un tenant Azure Active Directory diverso. Per specificare un tenant di Azure AD diverso, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata.

### <a name="use-your-personal-microsoft-account"></a>Usare l'account Microsoft personale

Per usare un account Microsoft personale per l'integrazione con Git, è possibile collegare il repository di Azure personale con l'istanza di Active Directory dell'organizzazione.

1. Aggiungere come guest l'account Microsoft personale all'istanza di Active Directory dell'organizzazione. Per altre informazioni, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../active-directory/b2b/add-users-administrator.md).

2. Accedere al portale di Azure con l'account Microsoft personale. Passare quindi all'istanza di Active Directory dell'organizzazione.

3. Passare alla sezione Azure DevOps, in cui è possibile visualizzare il repository personale. Selezionare il repository e connettersi ad Active Directory.

Dopo questi passaggi di configurazione, il repository personale è disponibile quando si configura l'integrazione di Git nell'interfaccia utente di Data Factory.

Per altre informazioni sulla connessione di Azure Repos all'istanza di Active Directory dell'organizzazione, vedere [Connect your Azure DevOps organization to Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad) (Connettere un'organizzazione di Azure DevOps ad Azure Active Directory).

## <a name="author-with-github-integration"></a>Creazione tramite l'integrazione di GitHub

La soluzione che prevede l'uso dell'integrazione di GitHub supporta funzionalità per il controllo del codice sorgente e la collaborazione per le attività sulle pipeline di data factory. È possibile associare una data factory a un repository di account GitHub per il controllo del codice sorgente, la collaborazione e il controllo delle versioni. Un singolo account GitHub può avere più repository, ma un repository GitHub può essere associato a una sola data factory. Se non si ha un account github o un repository, seguire [queste istruzioni](https://github.com/join) per creare le risorse.

L'integrazione di GitHub con Data Factory supporta sia GitHub pubblico (ovvero [https://github.com](https://github.com)) che GitHub Enterprise. È possibile usare i repository GitHub pubblici e privati con Data Factory purché siano state ottenute le autorizzazioni di lettura e scrittura nel repository in GitHub.

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
| **GitHub Enterprise URL** (URL GitHub Enterprise) | URL radice di GitHub Enterprise (deve essere HTTPS per il server GitHub Enterprise locale). Ad esempio: `https://github.mydomain.com`. Obbligatorio solo se è selezionata l'opzione **usa github Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Account GitHub** | Nome dell'account GitHub. Questo nome può essere trovato da https:\//github.com/{account nome}/{repository}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub. | `<your GitHub account name>` |
| **Nome del repository**  | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account. | `<your repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, il relativo master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di data factory esistenti dall'area di disegno di creazione dell'esperienza utente in un repository GitHub. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse) | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente |  |

### <a name="known-github-limitations"></a>Limitazioni note di GitHub

- È possibile archiviare file di script e di dati in un repository GitHub. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di script o di dati archiviati in un repository GitHub.

- GitHub Enterprise in una versione precedente alla 2.14.0 non funziona nel browser Microsoft Edge.

- L'integrazione di GitHub con gli strumenti di creazione visiva Data Factory funziona solo nella versione disponibile a livello generale di Data Factory.

- È possibile recuperare un massimo di 1.000 entità per tipo di risorsa, ad esempio pipeline e set di impostazioni, da un singolo ramo GitHub. Se viene raggiunto questo limite, è consigliabile suddividere le risorse in stabilimenti distinti. Azure DevOps Git non presenta questa limitazione.

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

### <a name="creating-feature-branches"></a>Creazione di rami di funzionalità

Ogni repository GIT Azure Repos associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Gli utenti possono anche creare rami di funzionalità facendo clic su **+ nuovo ramo** nell'elenco a discesa del ramo. Quando viene visualizzato il riquadro nuovo ramo, immettere il nome del ramo della funzionalità.

![Creare un nuovo ramo](media/author-visually/new-branch.png)

Quando si è pronti per eseguire il merge delle modifiche dal ramo funzionalità al ramo di collaborazione, fare clic sull'elenco a discesa Branch e selezionare **Crea richiesta pull**. Viene visualizzata la pagina GIT Azure Repos, in cui è possibile generare richieste di pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per impostazione predefinita, data factory genera i modelli di Gestione risorse della Factory pubblicata e li salva in un ramo `adf_public`denominato. Per configurare un ramo di pubblicazione personalizzato, aggiungere `publish_config.json` un file alla cartella radice nel ramo collaborazione. Quando si pubblica, ADF legge questo file, Cerca il campo `publishBranch`e Salva tutti i modelli di gestione risorse nel percorso specificato. Se il ramo non esiste, data factory lo creerà automaticamente. Di seguito è riportato un esempio di come appare questo file:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory possibile avere un solo ramo di pubblicazione alla volta. Quando si specifica un nuovo ramo di pubblicazione, Data Factory non elimina il ramo di pubblicazione precedente. Se si vuole rimuovere il ramo di pubblicazione precedente, eliminarlo manualmente.

> [!NOTE]
> Data Factory legge il file `publish_config.json` solo al momento del caricamento della factory. Se la factory è già caricata nel portale, aggiornare il browser per rendere effettive le modifiche.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

Dopo aver unito le modifiche al ramo collaborazione (`master` impostazione predefinita), fare clic su **pubblica** per pubblicare manualmente le modifiche del codice nel ramo master nel servizio Data Factory.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

Verrà aperto un riquadro laterale in cui si conferma che il ramo di pubblicazione e le modifiche in sospeso sono corretti. Una volta verificate le modifiche, fare clic su **OK** per confermare la pubblicazione.

![Confermare il ramo di pubblicazione corretto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Il ramo principale non è rappresentativo di ciò che viene distribuito nel servizio Data Factory. Il ramo master *deve* essere pubblicato manualmente nel servizio Data Factory.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

### <a name="permissions"></a>Autorizzazioni

In genere, non è necessario che ogni membro del team disponga delle autorizzazioni per aggiornare la factory. Sono consigliate le seguenti impostazioni di autorizzazione:

*   Tutti i membri del team devono avere le autorizzazioni di accesso in lettura alla data factory.
*   Solo un set selezionato di persone dovrebbe essere autorizzato a pubblicare nella Factory. A tale scopo, devono avere il ruolo di **collaboratore Data Factory** per il gruppo di risorse in cui si trova la factory. Per ulteriori informazioni sulle autorizzazioni, vedere [ruoli e autorizzazioni per Azure Data Factory](concepts-roles-permissions.md).
   
Si consiglia di non consentire le archiviazioni dirette al ramo collaborazione. Questa restrizione può aiutare a prevenire i bug quando ogni archiviazione passa attraverso un processo di revisione delle richieste pull descritto in [creazione di rami di funzionalità](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Utilizzo di password da Azure Key Vault

Si consiglia di usare Azure Key Vault per archiviare le stringhe di connessione o le password o l'autenticazione dell'identità gestita per Data Factory servizi collegati. Per motivi di sicurezza, data factory non archivia i segreti in git. Tutte le modifiche apportate ai servizi collegati contenenti segreti, ad esempio le password, vengono pubblicate immediatamente nel servizio Azure Data Factory.

L'uso dell'autenticazione Key Vault o MSI rende inoltre più semplice l'integrazione e la distribuzione continue, perché non è necessario fornire questi segreti durante la distribuzione del modello Gestione risorse.

## <a name="troubleshooting-git-integration"></a>Risoluzione dei problemi di integrazione con git

### <a name="stale-publish-branch"></a>Ramo di pubblicazione non aggiornato

Se il ramo di pubblicazione non è sincronizzato con il ramo master e contiene risorse non aggiornate nonostante una pubblicazione recente, provare a seguire questa procedura:

1. Rimuovere il repository git corrente
1. Riconfigurare git con le stesse impostazioni, ma assicurarsi che **Importa le risorse di data factory esistenti nel repository** sia selezionato e scegliere **nuovo ramo**
1. Creare una richiesta pull per unire le modifiche al ramo di collaborazione 

Di seguito sono riportati alcuni esempi di situazioni che possono causare un ramo di pubblicazione non aggiornato:
- Un utente dispone di più rami. In un ramo della funzionalità è stato eliminato un servizio collegato che non è associato a AKV (i servizi collegati non AKV vengono pubblicati immediatamente indipendentemente dal fatto che si trovino in Git o meno) e non uniscono mai il ramo della funzionalità nel ramo collaborazione.
- Un utente ha modificato il data factory usando l'SDK o PowerShell
- Un utente ha spostato tutte le risorse in un nuovo ramo e ha tentato di eseguire la pubblicazione per la prima volta. I servizi collegati devono essere creati manualmente durante l'importazione delle risorse.
- Un utente carica manualmente un servizio collegato non AKV o un Integration Runtime JSON. Fanno riferimento a tale risorsa da un'altra risorsa, ad esempio un set di dati, un servizio collegato o una pipeline. Un servizio collegato non AKV creato tramite la UX viene pubblicato immediatamente perché le credenziali devono essere crittografate. Se si carica un set di dati che fa riferimento a tale servizio collegato e si tenta di eseguire la pubblicazione, l'esperienza utente lo consentirà perché esiste nell'ambiente git. Verrà rifiutato in fase di pubblicazione perché non esiste nel servizio data factory.

## <a name="switch-to-a-different-git-repository"></a>Passa a un repository git diverso

Per passare a un repository git diverso, fare clic sull'icona **delle impostazioni** del repository git nell'angolo superiore destro della pagina Panoramica data factory. Se l'icona non è visibile, deselezionare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

![Icona GIT](media/author-visually/remove-repo.png)

Quando viene visualizzato il riquadro Impostazioni repository, selezionare **Rimuovi git**. Immettere il nome del data factory e fare clic su **conferma** per rimuovere il repository git associato al data factory.

![Rimuovere l'associazione al repository Git corrente](media/author-visually/remove-repo2.png)

Dopo la rimozione dell'associazione con il repository corrente, è possibile configurare le impostazioni git per l'uso di un repository diverso e quindi importare le risorse di Data Factory esistenti nel nuovo repository.

> [!IMPORTANT]
> La rimozione della configurazione git da un data factory non comporta l'eliminazione di alcun elemento dal repository. La Factory conterrà tutte le risorse pubblicate. È possibile continuare a modificare la Factory direttamente nel servizio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
* Per implementare l'integrazione e la distribuzione continue, vedere [integrazione continua e recapito continuo (ci/CD) in Azure Data Factory](continuous-integration-deployment.md).
