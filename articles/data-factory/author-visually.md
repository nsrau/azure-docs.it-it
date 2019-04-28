---
title: Creazione di oggetti visivi in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare la creazione di oggetti visivi in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: d2e51551643e154714b9d2368e63d7af9ebfa204
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593100"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creazione di oggetti visivi in Azure Data Factory
L'interfaccia utente di Azure Data Factory consente di creare e distribuire con strumenti visivi le risorse della data factory senza dover scrivere il codice. È possibile trascinare le attività in un'area di disegno della pipeline, eseguire test, eseguire il debug in modo iterativo, nonché distribuire e monitorare le esecuzioni della pipeline. Sono disponibili due approcci per usare l'esperienza utente per la creazione di oggetti visivi:

- Creare direttamente con il servizio Data Factory.
- Creare direttamente con l'integrazione di Git di Azure Repos per la collaborazione, il controllo del codice sorgente e il controllo delle versioni.

## <a name="author-directly-with-the-data-factory-service"></a>Creare direttamente con il servizio Data Factory
La creazione di oggetti visivi con il servizio Data Factory presenta due differenze rispetto alla creazione di oggetti visivi con l'integrazione di Git:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche.
- Il servizio Data Factory non è ottimizzato per la collaborazione o il controllo della versione.

![Configurare il servizio Data Factory](media/author-visually/configure-data-factory.png)

Quando si usa l'**area di disegno di creazione** dell'esperienza utente per creare direttamente con il servizio Data Factory, è disponibile solo la modalità **Pubblica tutti**. Le modifiche apportate vengono pubblicate direttamente nel servizio Data Factory.

![Modalità Pubblica](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Creare con l'integrazione di GIT Azure Repos
La soluzione che prevede l'uso dell'integrazione di GIT Azure Repos supporta funzionalità per il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. È possibile associare una data factory a un repository di organizzazione GIT Azure Repos per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Una singola organizzazione GIT Azure Repos può avere più repository, ma un repository GIT Azure Repos può essere associato a una sola data factory. Se non si ha un'organizzazione o un repository Azure Repos, seguire [queste istruzioni](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare le risorse.

> [!NOTE]
> È possibile archiviare file di script e di dati in un repository GIT Azure Repos. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di dati o di script archiviati in un repository GIT Azure Repos.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurare un repository GIT Azure Repos con Azure Data Factory
È possibile configurare un repository GIT Azure Repos con una data factory attraverso due metodi diversi.

#### <a name="method1"></a> Metodo di configurazione 1 (repository GIT Azure Repos): Pagina Attività iniziali

In Azure Data Factory andare alla pagina **Attività iniziali**. Selezionare **Configure Code Repository** (Configura repository di codice):

![Configurare un repository di codice Azure Repos](media/author-visually/configure-repo.png)

Viene visualizzato il riquadro di configurazione **Impostazioni repository**:

![Configurare le impostazioni del repository di codice](media/author-visually/repo-settings.png)

Il riquadro visualizza le impostazioni del repository di codice Azure Repos seguenti:

| Impostazione | DESCRIZIONE | Value |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/>**Nota**: GitHub non è attualmente supportato. | GIT Azure Repos |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Organizzazione Azure Repos** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Nome progetto** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. L'impostazione predefinita è `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT Azure Repos. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Metodo di configurazione 2 (repository GIT Azure Repos): Area di disegno di creazione dell'esperienza utente
Nell'**area di disegno di creazione** dell'esperienza utente di Azure Data Factory individuare la data factory. Selezionare il menu a discesa **Data Factory** e quindi scegliere **Configure Code Repository** (Configura repository di codice).

Viene visualizzato un riquadro di configurazione. Per informazioni dettagliate sulle impostazioni di configurazione, vedere le descrizioni in <a href="#method1">Metodo di configurazione 1</a>.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>Usare un tenant di Azure Active Directory diverso

È possibile creare un repository GIT Azure Repos in un tenant di Azure Active Directory diverso. Per specificare un tenant di Azure AD diverso, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata.

### <a name="use-your-personal-microsoft-account"></a>Usare l'account Microsoft personale

Per usare un account Microsoft personale per l'integrazione con Git, è possibile collegare il repository di Azure personale con l'istanza di Active Directory dell'organizzazione.

1. Aggiungere come guest l'account Microsoft personale all'istanza di Active Directory dell'organizzazione. Per altre informazioni, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../active-directory/b2b/add-users-administrator.md).

2. Accedere al portale di Azure con l'account Microsoft personale. Passare quindi all'istanza di Active Directory dell'organizzazione.

3. Passare alla sezione Azure DevOps, in cui è possibile visualizzare il repository personale. Selezionare il repository e connettersi ad Active Directory.

Dopo questi passaggi di configurazione, il repository personale è disponibile quando si configura l'integrazione di Git nell'interfaccia utente di Data Factory.

Per altre informazioni sulla connessione di Azure Repos all'istanza di Active Directory dell'organizzazione, vedere [Connect your Azure DevOps organization to Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad) (Connettere un'organizzazione di Azure DevOps ad Azure Active Directory).

### <a name="switch-to-a-different-git-repo"></a>Cambiare repository Git

Per passare a un repository Git diverso, individuare l'icona nell'angolo superiore destro della pagina di panoramica di Data Factory, come illustrato nella schermata seguente. Se l'icona non è visibile, cancellare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

Dopo aver rimosso l'associazione al repository corrente, è possibile configurare le impostazioni di Git per usare un repository diverso. È quindi possibile importare le risorse di Data Factory esistenti nel nuovo repository.

![Rimuovere l'associazione al repository Git corrente](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Usare il controllo della versione
I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

Ogni repository GIT Azure Repos associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Gli utenti possono anche creare rami funzionalità facendo clic su **Aggiungi nuovo ramo** e sviluppando i rami funzionalità.

![Modificare il codice tramite sincronizzazione o pubblicazione](media/author-visually/sync-publish.png)

Quando si è pronti con lo sviluppo di funzionalità nel ramo funzionalità, è possibile fare clic su **Crea richiesta pull**. Viene visualizzata la pagina GIT Azure Repos, in cui è possibile generare richieste di pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per configurare il ramo di pubblicazione, vale a dire, il ramo in cui vengono salvati i modelli di Resource Manager, aggiungere un file `publish_config.json` nella cartella radice nel ramo di collaborazione. Data Factory legge questo file, cerca il campo `publishBranch` e crea un nuovo ramo (se non esiste già) con il valore specificato. Quindi salva tutti i modelli di Resource Manager nel percorso specificato. Ad esempio: 

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando si pubblica dalla modalità di Git, è possibile verificare che Data Factory usi il ramo di pubblicazione previsto come illustrato nello screenshot seguente:

![Confermare il ramo di pubblicazione corretto](media/author-visually/configure-publish-branch.png)

Quando si specifica un nuovo ramo di pubblicazione, Data Factory non elimina il ramo di pubblicazione precedente. Se si intende inviare ad ambiente remoto il ramo di pubblicazione precedente, eliminarlo manualmente.

Data Factory legge il file `publish_config.json` solo al momento del caricamento della factory. Se la factory è già caricata nel portale, aggiornare il browser per rendere effettive le modifiche.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice
Dopo aver unito le modifiche nel ramo di collaborazione (`master` è l'impostazione predefinita), selezionare **Pubblica** per pubblicare manualmente le modifiche del codice nel ramo master del servizio Data Factory.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Il ramo principale non è rappresentativo di ciò che viene distribuito nel servizio Data Factory. Il ramo master *deve* essere pubblicato manualmente nel servizio Data Factory.

### <a name="advantages-of-git-integration"></a>Vantaggi dell'integrazione con GIT

-   **Controllo del codice sorgente**. Man mano che i carichi di lavoro della data factory assumono un'importanza cruciale, può essere opportuno integrare la factory con GIT per sfruttare alcuni vantaggi del controllo del codice sorgente, come i seguenti:
    -   Possibilità di controllare le modifiche e tenerne traccia.
    -   Possibilità di annullare le modifiche che hanno introdotto bug.
-   **Salvataggi parziali**. Quando si apportano numerose modifiche nella factory, si noterà che nella normale modalità LIVE non è possibile salvare le modifiche come bozza perché non si è pronti o non si vogliono perdere le modifiche in caso di arresto anomalo del computer. Grazie all'integrazione con GIT, è possibile continuare a salvare le modifiche in modo incrementale ed eseguire la pubblicazione nella factory solo quando si è pronti. GIT funziona come un punto di gestione temporanea finché le modifiche non sono state testate e risultano soddisfacenti.
-   **Collaborazione e controllo**. Se più membri del team partecipano alla stessa factory, può essere opportuno offrire loro l'opportunità di collaborare tramite un processo di revisione del codice. È anche possibile configurare la factory in modo che non tutti i collaboratori abbiano le autorizzazioni per eseguire distribuzioni nella factory. I membri del team potrebbero essere autorizzati solo ad apportare modifiche tramite GIT, mentre solo determinati utenti potrebbero disporre dell'autorizzazione a "pubblicare" le modifiche nella factory.
-   **Visualizzazione delle differenze**. In modalità GIT è possibile visualizzare un diff del payload che sta per essere pubblicato nella factory. Il diff mostra tutte le risorse o le entità che sono state modificate, aggiunte o eliminate dall'ultima pubblicazione nella factory. In base a questo diff, è possibile proseguire con la pubblicazione o tornare indietro e controllare le modifiche prima di riprendere il processo.
-   **Integrazione continua/Distribuzione continua più efficaci**. Se si usa la modalità GIT, è possibile configurare la pipeline di versione in modo che venga attivata automaticamente non appena vengono apportate modifiche nella factory di sviluppo. È anche possibile personalizzare le proprietà nella factory che sono disponibili come parametri nel modello di Resource Manager. Ciò può essere utile per mantenere come parametri solo il set di proprietà richiesto e impostare tutto il resto come hardcoded.
-   **Prestazioni migliori**. In modalità GIT una factory media viene caricata a una velocità 10 maggiore rispetto alla normale modalità LIVE, perché le risorse vengono scaricate tramite GIT.

### <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

-   **Autorizzazioni**. In genere è preferibile che non tutti i membri del team abbiano le autorizzazioni per aggiornare la factory.
    -   Tutti i membri del team devono avere le autorizzazioni di accesso in lettura alla data factory.
    -   Solo un determinato set di utenti deve essere autorizzato a pubblicare nella factory e a tale scopo questi devono far parte del ruolo "Collaboratore Data factory" nella factory.
    -   Una delle procedure consigliate del controllo del codice sorgente è anche quella di impedire le archiviazioni dirette nel ramo di collaborazione. Questo requisito consente di evitare bug perché ogni archiviazione passa attraverso un processo di richiesta pull.
-   **Passaggio da una modalità all'altra**.
    -    Quando è attiva la modalità GIT, non è consigliabile alternare questa modalità con la modalità LIVE, principalmente perché le modifiche apportate in modalità LIVE non vengono visualizzate quando si passa alla modalità GIT. Cercare di apportare le modifiche in modalità GIT e quindi pubblicarle tramite l'interfaccia utente.
    -   Analogamente, non usare cmdlet di PowerShell per Data factory, perché raggiungono lo stesso effetto applicando le modifiche direttamente alla factory in modalità LIVE.
-   **Usare password da Azure Key Vault**.
    -   È consigliabile usare Azure Key Vault per archiviare eventuali password o stringhe di connessione in Servizi collegati di Data factory.
    -   Le informazioni segrete di questo tipo non vengono archiviate in GIT (per motivi di sicurezza) e pertanto le modifiche apportate a Servizi collegati vengono pubblicate direttamente nella factory in modalità LIVE. In alcuni casi questa pubblicazione immediata non è appropriata perché le modifiche potrebbero non essere state testate, vanificando così lo scopo di GIT.
    -   Tutti i segreti di questo tipo devono pertanto essere recuperati da Servizi collegati basati su Azure Key Vault.
    -   Tra i vantaggi offerti dall'uso di Key Vault è inclusa anche la semplificazione del processo di Integrazione continua/Distribuzione continua, poiché l'utente non deve specificare questi segreti durante la distribuzione del modello di Resource Manager.

## <a name="author-with-github-integration"></a>Creazione tramite l'integrazione di GitHub

La soluzione che prevede l'uso dell'integrazione di GitHub supporta funzionalità per il controllo del codice sorgente e la collaborazione per le attività sulle pipeline di data factory. È possibile associare una data factory a un repository di account GitHub per il controllo del codice sorgente, la collaborazione e il controllo delle versioni. Un singolo account GitHub può avere più repository, ma un repository GitHub può essere associato a una sola data factory. In assenza di un account o un repository GitHub seguire  [queste istruzioni](https://github.com/join)  per creare le risorse.

L'integrazione di GitHub con Data Factory supporta GitHub pubblico (ovvero [https://github.com](https://github.com)) e GitHub Enterprise. È possibile usare i repository GitHub pubblici e privati con Data Factory purché siano state ottenute le autorizzazioni di lettura e scrittura nel repository in GitHub.

Per configurare un repository GitHub, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata.

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Limitazioni

- È possibile archiviare file di script e di dati in un repository GitHub. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di script o di dati archiviati in un repository GitHub.

- GitHub Enterprise in una versione precedente alla 2.14.0 non funziona nel browser Microsoft Edge.

- L'integrazione di GitHub con gli strumenti di creazione di oggetti visivi di Data Factory funziona solo nella versione di Data Factory disponibile a livello generale.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Configurare un repository GitHub pubblico con Azure Data Factory

È possibile configurare un repository GitHub con una data factory attraverso due metodi diversi.

**Metodo di configurazione 1 (repository pubblico): Pagina Attività iniziali**

In Azure Data Factory andare alla pagina  **Attività iniziali** . Selezionare  **Configurare il repository di codice**:

![Pagina Attività iniziali di Data Factory](media/author-visually/github-integration-image1.png)

Viene visualizzato il riquadro di configurazione  **Impostazioni repository** :

![Impostazioni del repository GitHub](media/author-visually/github-integration-image2.png)

Il riquadro visualizza le impostazioni del repository di codice Azure Repos seguenti:

| **Impostazione**                                              | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valore**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Tipo di repository**                                      | Tipo del repository di codice Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Account GitHub**                                       | Nome dell'account GitHub. Questo nome è reperibile da https:\//github.com/{account nome} / {nome del repository}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub.                                                                                                                                                                                                                                               |                    |
| **Nome repository**                                       | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account.                                                                                                                                                                                                                              |                    |
| **Ramo di collaborazione**                                 | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, si tratta del ramo master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo.                                                                                                                                                                                                                                                               |                    |
| **Cartella radice**                                          | Cartella radice nel ramo di collaborazione di GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall' **area di disegno di creazione**  dell'esperienza utente in un repository GitHub. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse)                       | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Metodo di configurazione 2 (repository pubblico): Area di disegno di creazione dell'esperienza utente

Nell' **area di disegno di creazione** dell'esperienza utente di Azure Data Factory individuare la data factory. Selezionare il menu a discesa  **Data Factory**  e quindi scegliere  **Configure Code Repository** (Configura repository di codice).

Viene visualizzato un riquadro di configurazione. Per informazioni dettagliate sulle impostazioni di configurazione, vedere le descrizioni in  *Metodo di configurazione 1* riportate sopra.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Configurare un repository GitHub Enterprise con Azure Data Factory

È possibile configurare un repository GitHub Enterprise con una data factory attraverso due metodi diversi.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Metodo di configurazione 1 (repository Enterprise): Pagina Attività iniziali

In Azure Data Factory andare alla pagina  **Attività iniziali** . Selezionare  **Configurare il repository di codice**:

![Pagina Attività iniziali di Data Factory](media/author-visually/github-integration-image1.png)

Viene visualizzato il riquadro di configurazione  **Impostazioni repository** :

![Impostazioni del repository GitHub](media/author-visually/github-integration-image3.png)

Il riquadro visualizza le impostazioni del repository di codice Azure Repos seguenti:

| **Impostazione**                                              | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valore**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Tipo di repository**                                      | Tipo del repository di codice Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Use GitHub Enterprise** (Usa GitHub Enterprise)                                | Casella di controllo per selezionare GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise URL** (URL GitHub Enterprise)                                | URL radice di GitHub Enterprise. Ad esempio: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **Account GitHub**                                       | Nome dell'account GitHub. Questo nome è reperibile da https:\//github.com/{account nome} / {nome del repository}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub.                                                                                                                                                                                                                                               |                    |
| **Nome repository**                                       | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account.                                                                                                                                                                                                                              |                    |
| **Ramo di collaborazione**                                 | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, si tratta del ramo master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo.                                                                                                                                                                                                                                                               |                    |
| **Cartella radice**                                          | Cartella radice nel ramo di collaborazione di GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall' **area di disegno di creazione**  dell'esperienza utente in un repository GitHub. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Branch to import resource into** (Ramo in cui importare risorse)                       | Specifica il ramo in cui vengono importate le risorse di Data Factory (pipeline, set di dati, servizi collegati e così via). È possibile importare le risorse in uno dei rami seguenti: a. Collaborazione b. Crea nuovo c. Usa esistente                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Metodo di configurazione 2 (repository Enterprise): Area di disegno di creazione dell'esperienza utente

Nell' **area di disegno di creazione** dell'esperienza utente di Azure Data Factory individuare la data factory. Selezionare il menu a discesa  **Data Factory**  e quindi scegliere  **Configure Code Repository** (Configura repository di codice).

Viene visualizzato un riquadro di configurazione. Per informazioni dettagliate sulle impostazioni di configurazione, vedere le descrizioni in  *Metodo di configurazione 1* riportate sopra.

## <a name="use-the-expression-language"></a>Usare il linguaggio delle espressioni
È possibile specificare espressioni per i valori delle proprietà usando il linguaggio delle espressioni supportato da Azure Data Factory.

Specificare le espressioni per i valori delle proprietà selezionando **Aggiungere il contenuto dinamico**:

![Usare il linguaggio delle espressioni](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Usare funzioni e parametri

È possibile usare funzioni e parametri per pipeline e set di dati nella scheda **Generatore di espressioni** di Azure Data Factory:

Per informazioni sulle espressioni supportate, vedere [Espressioni e funzioni in Azure Data Factory](control-flow-expression-language-functions.md).

![Aggiungere il contenuto dinamico](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti e suggerimenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
