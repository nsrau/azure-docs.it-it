---
title: Controllo del codice sorgente in sinapsi Studio
description: Informazioni su come configurare il controllo del codice sorgente in Azure sinapsi Studio
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: a5cb5831090987f1b620593843ddba817d8e68d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188123"
---
# <a name="source-control-in-azure-synapse-studio"></a>Controllo del codice sorgente in Azure sinapsi Studio

Per impostazione predefinita, Azure sinapsi Studio crea direttamente il servizio sinapsi. Tuttavia, questa esperienza presenta le limitazioni seguenti:

- Sinapsi Studio non include un'archiviazione temporanea per l'archiviazione delle modifiche. L'unico modo per salvare e condividere le modifiche è tramite la **pubblicazione** e tutte le modifiche vengono pubblicate direttamente nel servizio sinapsi.

- Sinapsi Studio non è ottimizzato per la collaborazione e il controllo della versione.

Per fornire funzionalità di controllo del codice sorgente, sinapsi Studio consente di associare l'area di lavoro a un repository git, Azure DevOps o GitHub. Questo articolo descrive come configurare e lavorare in un'area di lavoro sinapsi con il repository git abilitato. Vengono inoltre evidenziate alcune procedure consigliate e una guida alla risoluzione dei problemi.

> [!NOTE]
> L'integrazione di Git in Azure sinapsi Studio non è disponibile nel cloud di Azure per enti pubblici.

## <a name="configure-git-repository-in-synapse-studio"></a>Configurare il repository git in sinapsi Studio 

Dopo l'avvio di sinapsi studio, è possibile configurare il repository git nell'area di lavoro. Un'area di lavoro di sinapsi Studio può essere associata solo a un repository git alla volta. 

### <a name="configuration-method-1-authoring-canvas"></a>Metodo di configurazione 1: creazione dell'area di disegno

Nell'area di disegno della creazione di sinapsi Studio selezionare il menu a discesa **sinapsi Live** , quindi selezionare **Configura repository di codice**.

![Configurare le impostazioni del repository di codice dalla creazione](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Metodo di configurazione 2: gestire l'hub

Passare all'hub di gestione di sinapsi Studio. Selezionare **configurazione git** nella sezione **controllo del codice sorgente** . Se non si dispone di un repository connesso, fare clic su **Configura**.

![Configurare le impostazioni del repository di codice dall'hub di gestione](media/configure-repo-2.png)

> [!NOTE]
> Gli utenti concessi come ruoli collaboratore area di lavoro, proprietario o livello superiore possono configurare, modificare l'impostazione e disconnettere il repository git in Azure sinapsi Studio 

È possibile connettere il repository git di Azure DevOps o GitHub nell'area di lavoro.

## <a name="connect-with-azure-devops-git"></a>Connettersi con Azure DevOps git 

È possibile associare un'area di lavoro sinapsi a un repository Azure DevOps per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Se non si dispone di un repository Azure DevOps, seguire [queste istruzioni](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare prima le risorse del repository.

### <a name="azure-devops-git-repository-settings"></a>Impostazioni del repository git di Azure DevOps

Quando ci si connette al repository git, selezionare prima di tutto il tipo di repository come Azure DevOps git e quindi selezionare un Azure AD tenant dall'elenco a discesa e fare clic su **continue (continua**).

![Configurare le impostazioni del repository di codice](media/connect-with-azuredevops-repo-selected.png)

Il riquadro Configurazione Mostra le impostazioni git DevOps di Azure seguenti:

| Impostazione | Descrizione | valore |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps Git o GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Account Azure DevOps** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Nome progetto** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. L'impostazione predefinita è `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. È possibile selezionare rami esistenti o crearne di nuovi | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Importa le risorse esistenti nel repository** | Specifica se importare le risorse esistenti da sinapsi studio in un repository git Azure Repos. Selezionare la casella per importare le risorse dell'area di lavoro, ad eccezione dei pool, nel repository git associato in formato JSON. Questa azione esporta ogni risorsa singolarmente. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionato (impostazione predefinita) |
| **Importa risorsa in questo branch** | Selezionare il ramo in cui vengono importate le risorse (script SQL, notebook, definizione del processo Spark, set di dati, flusso di dati e così via). 

È anche possibile usare il collegamento repository per puntare rapidamente al repository git con cui si vuole connettersi. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Usare un tenant di Azure Active Directory diverso

Il repository Azure Repos Git può essere posizionato in un diverso tenant di Azure Active Directory. Per specificare un tenant di Azure AD diverso, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata. Per altre informazioni, vedere [modificare l'amministratore della sottoscrizione](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator) .

> [!IMPORTANT]
> Per connettersi a un'altra Azure Active Directory, l'utente che ha eseguito l'accesso deve appartenere a tale Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Usare l'account Microsoft personale

Per usare un account Microsoft personale per l'integrazione con Git, è possibile collegare il repository di Azure personale con l'istanza di Active Directory dell'organizzazione.

1. Aggiungere come guest l'account Microsoft personale all'istanza di Active Directory dell'organizzazione. Per altre informazioni, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/external-identities/add-users-administrator).

2. Accedere al portale di Azure con l'account Microsoft personale. Passare quindi all'istanza di Active Directory dell'organizzazione.

3. Passare alla sezione Azure DevOps, in cui è possibile visualizzare il repository personale. Selezionare il repository e connettersi ad Active Directory.

Dopo questi passaggi di configurazione, il repository personale è disponibile quando si configura l'integrazione git in sinapsi Studio.

Per altre informazioni sulla connessione di Azure Repos all'istanza di Active Directory dell'organizzazione, vedere [Connect your Azure DevOps organization to Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad) (Connettere un'organizzazione di Azure DevOps ad Azure Active Directory).

## <a name="connect-with-github"></a>Connettersi con GitHub 

 È possibile associare un'area di lavoro a un repository GitHub per il controllo del codice sorgente, la collaborazione, il controllo delle versioni. Se non si ha un account o un repository GitHub, seguire [queste istruzioni](https://github.com/join) per creare le risorse.

L'integrazione di GitHub con sinapsi Studio supporta sia GitHub pubblico (ovvero [https://github.com](https://github.com) ) che GitHub Enterprise. È possibile usare i repository GitHub sia pubblici che privati, purché si disponga delle autorizzazioni di lettura e scrittura per il repository in GitHub.

### <a name="github-settings"></a>Impostazioni di GitHub

Quando ci si connette al repository git, selezionare prima di tutto il tipo di repository come GitHub e quindi specificare l'account GitHub o l'URL di GitHub Enterprise Server se si usa GitHub Enterprise Server e fare clic su **continue (continua**).

![Impostazioni del repository GitHub](media/connect-with-github-repo-1.png)

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
| **Importa le risorse esistenti nel repository** | Specifica se importare le risorse esistenti da sinapsi studio in un repository git. Selezionare la casella per importare le risorse dell'area di lavoro, ad eccezione dei pool, nel repository git associato in formato JSON. Questa azione esporta ogni risorsa singolarmente. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Importa risorsa in questo branch** | Consente di selezionare il ramo in cui vengono importate le risorse (script SQL, notebook, definizione del processo Spark, set di dati, flusso di dati e così via). 

### <a name="github-organizations"></a>Organizzazioni GitHub

Per la connessione a un'organizzazione GitHub è necessario che l'organizzazione conceda l'autorizzazione a sinapsi Studio. Un utente con autorizzazioni di amministratore per l'organizzazione deve eseguire i passaggi seguenti.

#### <a name="connecting-to-github-for-the-first-time"></a>Connessione a GitHub per la prima volta

Se ci si connette a GitHub da sinapsi Studio per la prima volta, seguire questa procedura per connettersi a un'organizzazione GitHub.

1. Nel riquadro Configurazione git immettere il nome dell'organizzazione nel campo *account github* . Verrà visualizzata una richiesta di accesso a GitHub. 

1. Accedere usando le credenziali utente.

1. Verrà richiesto di autorizzare sinapsi come un'applicazione denominata *sinapsi di Azure*. In questa schermata verrà visualizzata un'opzione per concedere l'autorizzazione per l'accesso dell'organizzazione a sinapsi. Se non viene visualizzata l'opzione per concedere l'autorizzazione, chiedere a un amministratore di concedere manualmente l'autorizzazione tramite GitHub.

Una volta eseguita questa procedura, l'area di lavoro sarà in grado di connettersi ai repository pubblici e privati all'interno dell'organizzazione. Se non si riesce a connettersi, provare a cancellare la cache del browser e a riprovare.

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (noti anche come controllo del codice _sorgente_) consentono agli sviluppatori di collaborare al codice e di tenere traccia delle modifiche. Il controllo del codice sorgente è uno strumento essenziale per progetti multisviluppatore.

### <a name="creating-feature-branches"></a>Creazione di rami di funzionalità

Ogni repository git associato a una sinapsi studio ha un ramo di collaborazione. `main`o `master` è il ramo di collaborazione predefinito. Gli utenti possono creare rami di funzionalità facendo clic su **Aggiungi nuovo ramo** nel menu a discesa dei rami. Quando viene visualizzato il riquadro del nuovo ramo, immettere il nome del ramo di funzionalità.

![Creare un nuovo ramo](media/create-new-branch.png)

Quando si è pronti per unire le modifiche dal ramo di funzionalità al ramo di collaborazione, fare clic sull'elenco a discesa del ramo e selezionare **Crea richiesta pull**. Questa azione consente di passare al provider git in cui è possibile generare richieste pull, eseguire revisioni del codice e unire le modifiche nel ramo di collaborazione. È possibile eseguire la pubblicazione nel servizio sinapsi solo dal ramo di collaborazione. 

![Creare una nuova richiesta pull](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per impostazione predefinita, sinapsi Studio genera i modelli dell'area di lavoro e li salva in un branch denominato `workspace_publish` . Per configurare un ramo di pubblicazione personalizzato, aggiungere un file `publish_config.json` alla cartella radice nel ramo di collaborazione. Quando si pubblica, sinapsi studio legge questo file, Cerca il campo `publishBranch` e salva i file del modello dell'area di lavoro nel percorso specificato. Se il ramo non esiste, sinapsi studio lo creerà automaticamente. Un esempio dell'aspetto del file è il seguente:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure sinapsi Studio può avere un solo Branch di pubblicazione alla volta. Quando si specifica un nuovo ramo di pubblicazione, il ramo di pubblicazione precedente non è stato eliminato. Se si intende rimuovere il ramo di pubblicazione precedente, eliminarlo manualmente.

### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

Dopo l'Unione delle modifiche al ramo collaborazione, fare clic su **pubblica** per pubblicare manualmente le modifiche al codice nel ramo collaborazione nel servizio sinapsi.

![Pubblicare modifiche](media/gitmode-publish.png)

Si apre un riquadro laterale in cui l'utente conferma che il ramo di pubblicazione e le modifiche in sospeso sono corretti. Dopo aver verificato le modifiche, fare clic su **OK** per confermare la pubblicazione.

![Confermare il ramo di pubblicazione corretto](media/publish-change.png)

> [!IMPORTANT]
> Il ramo collaborazione non è rappresentativo di ciò che viene distribuito nel servizio. Le modifiche nel ramo collaborazione *devono* essere pubblicate manualmente.

## <a name="switch-to-a-different-git-repository"></a>Passare a un repository Git diverso

Per passare a un repository git diverso, passare alla pagina Configurazione git nell'hub di gestione sotto **controllo del codice sorgente**. Selezionare **Disconnetti**. 

![Icona Git](media/remove-repository.png)

Immettere il nome dell'area di lavoro e fare clic su **Disconnetti** per rimuovere il repository git associato all'area di lavoro.

Dopo la rimozione dell'associazione con il repository corrente, è possibile configurare le impostazioni git per l'uso di un repository diverso e quindi importare le risorse esistenti nel nuovo repository.

> [!IMPORTANT]
> La rimozione della configurazione git da un'area di lavoro non comporta l'eliminazione di alcun elemento dal repository. L'area di lavoro sinapsi conterrà tutte le risorse pubblicate. È possibile continuare a modificare l'area di lavoro direttamente nel servizio.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

-   **Autorizzazioni**. Quando si dispone di un repository git connesso all'area di lavoro, chiunque possa accedere al repository git con qualsiasi ruolo nell'area di lavoro sarà in grado di aggiornare gli elementi, ad esempio script SQL, notebook, definizione del processo Spark, set di dati, flusso di dati e pipeline in modalità git. In genere, non è necessario che ogni membro del team disponga delle autorizzazioni per aggiornare l'area di lavoro. Concedere l'autorizzazione per il repository git solo agli autori di elementi dell'area di lavoro sinapsi. 
-   **Collaborazione**. Si consiglia di non consentire le archiviazioni dirette nel ramo collaborazione. Questa limitazione può aiutare a prevenire i bug in quanto ogni archiviazione passa attraverso un processo di revisione delle richieste pull descritto in [Creazione di rami di funzionalità](source-control.md#creating-feature-branches).
-   **Modalità Live sinapsi**. Dopo la pubblicazione in modalità git, tutte le modifiche verranno riflesse in modalità Live sinapsi. In modalità Live sinapsi la pubblicazione è disabilitata. È possibile visualizzare ed eseguire gli artefatti in modalità Live se è stata concessa l'autorizzazione appropriata. 
-   **Modificare gli artefatti in studio**. Sinapsi studio è l'unica posizione in cui è possibile abilitare il controllo del codice sorgente dell'area di lavoro e sincronizzare automaticamente le modifiche in git. Qualsiasi modifica tramite SDK, PowerShell, non verrà sincronizzata con git. È consigliabile modificare sempre l'artefatto in studio quando Git è abilitato.

## <a name="troubleshooting-git-integration"></a>Risoluzione dei problemi di integrazione con git

### <a name="access-to-git-mode"></a>Accesso alla modalità git 

Se è stata concessa l'autorizzazione per il repository git GitHub collegato all'area di lavoro, ma non è possibile accedere alla modalità git: 

1. Cancellare la cache e aggiornare la pagina. 

1. Accedere all'account GitHub.

### <a name="stale-publish-branch"></a>Ramo di pubblicazione non aggiornato

Se il ramo di pubblicazione non è sincronizzato con il ramo collaborazione e contiene risorse non aggiornate nonostante una pubblicazione recente, provare a seguire questa procedura:

1. Rimuovere il repository Git corrente

1. Riconfigurare git con le stesse impostazioni, ma assicurarsi che **Importa risorse esistenti nel repository** sia selezionato e scegliere lo stesso ramo.  

1. Creare una richiesta pull per unire le modifiche al ramo di collaborazione 

## <a name="unsupported-features"></a>Funzionalità non supportate

- Sinapsi Studio non consente il cherry-picking di commit o la pubblicazione selettiva delle risorse. 
- Sinapsi Studio non supporta la personalizzazione del messaggio di commit.
- Per impostazione predefinita, l'azione di eliminazione in studio verrà sottoposta a commit direttamente in git

## <a name="next-steps"></a>Passaggi successivi

* Per implementare l'integrazione e la distribuzione continue, vedere [integrazione continua e recapito continuo (ci/CD)](continuous-integration-deployment.md).