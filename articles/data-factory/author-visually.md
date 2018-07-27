---
title: Creazione di oggetti visivi in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare la creazione di oggetti visivi in Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214855"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creazione di oggetti visivi in Azure Data Factory
L'interfaccia utente di Azure Data Factory consente di creare e distribuire con strumenti visivi le risorse della data factory senza dover scrivere il codice. È possibile trascinare le attività in un'area di disegno della pipeline, eseguire test, eseguire il debug in modo iterativo, nonché distribuire e monitorare le esecuzioni della pipeline. Sono disponibili due approcci per usare l'esperienza utente per la creazione di oggetti visivi:

- Creare direttamente con il servizio Data Factory.
- Creare con l'integrazione di GIT in VSTS (Visual Studio Team Services) per la collaborazione, il controllo del codice sorgente o il controllo delle versioni.

## <a name="author-directly-with-the-data-factory-service"></a>Creare direttamente con il servizio Data Factory
La creazione di oggetti visivi con il servizio Data Factory presenta due differenze rispetto alla creazione di oggetti visivi con VSTS:

- Il servizio Data Factory non include un repository in cui archiviare le entità JSON per le modifiche.
- Il servizio Data Factory non è ottimizzato per la collaborazione o il controllo della versione.

![Configurare il servizio Data Factory ](media/author-visually/configure-data-factory.png)

Quando si usa l'**area di disegno di creazione** dell'esperienza utente per creare direttamente con il servizio Data Factory, è disponibile solo la modalità **Pubblica tutti**. Le modifiche apportate vengono pubblicate direttamente nel servizio Data Factory.

![Modalità Pubblica](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Creare con l'integrazione di GIT in VSTS
La soluzione che prevede l'uso dell'integrazione di GIT in VSTS supporta funzionalità per il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. È possibile associare una data factory a un repository di un account GIT di VSTS per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Un singolo account GIT di VSTS può avere più repository, ma un repository GIT di VSTS può essere associato a una sola data factory. Se non si ha un account VSTS o un repository, seguire [queste istruzioni](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) per creare le risorse.

> [!NOTE]
> È possibile archiviare i file di dati e di script in un repository VSTS GIT. È tuttavia necessario caricare manualmente i file in Archiviazione di Azure. Una pipeline di Data Factory non carica automaticamente in Archiviazione di Azure i file di dati o di script archiviati in un repository VSTS GIT.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Configurare un repository GIT di VSTS con Azure Data Factory
È possibile configurare un repository GIT di VSTS con una data factory seguendo due diversi metodi.

#### <a name="method1"></a> Metodo di configurazione 1: Pagina Attività iniziali

In Azure Data Factory andare alla pagina **Attività iniziali**. Selezionare **Configure Code Repository** (Configura repository di codice):

![Configurare un repository di codice VSTS](media/author-visually/configure-repo.png)

Viene visualizzato il riquadro di configurazione **Impostazioni repository**:

![Configurare le impostazioni del repository di codice](media/author-visually/repo-settings.png)

Il riquadro visualizza le impostazioni del repository di codice VSTS seguenti:

| Impostazione | DESCRIZIONE | Valore |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice VSTS.<br/>**Nota**: GitHub non è attualmente supportato. | Visual Studio Team Services Git |
| **Azure Active Directory** | Nome del tenant di Azure AD. | <your tenant name> |
| **Account di Visual Studio Team Services** | Nome dell'account VSTS. È possibile trovare il nome dell'account VSTS all'indirizzo `https://{account name}.visualstudio.com`. È possibile [accedere all'account di VSTS](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare il repository e i progetti. | <your account name> |
| **Nome progetto** | Nome del progetto VSTS. È possibile trovare il nome del progetto VSTS all'indirizzo `https://{account name}.visualstudio.com/{project name}`. | <your VSTS project name> |
| **Nome repository** | Nome del repository di codice VSTS. i progetti di VSTS contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | <your VSTS code repository name> |
| **Ramo di collaborazione** | Ramo di collaborazione di VSTS che verrà usato per la pubblicazione. L'impostazione predefinita è `master`. Modificare questa impostazione per pubblicare le risorse da un altro ramo. | <your collaboration branch name> |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di VSTS. | <your root folder name> |
| **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository) | Specifica se importare le risorse di Data Factory esistenti dall'**area di disegno di creazione** dell'esperienza utente in un repository GIT di VSTS. Selezionare la casella per importare le risorse di Data Factory nel repository GIT associato in formato JSON. Questa azione esporta ogni risorsa singolarmente, vale a dire che i servizi collegati e i set di dati vengono esportati in file JSON separati. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metodo di configurazione 2: Area di disegno di creazione dell'esperienza utente
Nell'**area di disegno di creazione** dell'esperienza utente di Azure Data Factory individuare la data factory. Selezionare il menu a discesa **Data Factory** e quindi scegliere **Configure Code Repository** (Configura repository di codice).

Viene visualizzato un riquadro di configurazione. Per informazioni dettagliate sulle impostazioni di configurazione, vedere le descrizioni in <a href="#method1">Metodo di configurazione 1</a>.

![Configurare le impostazioni del repository di codice per la creazione nell'esperienza utente](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>Cambiare repository Git

Per passare a un repository Git diverso, individuare l'icona nell'angolo superiore destro della pagina di panoramica di Data Factory, come illustrato nella schermata seguente. Se l'icona non è visibile, cancellare la cache del browser locale. Selezionare l'icona per rimuovere l'associazione al repository corrente.

Dopo aver rimosso l'associazione al repository corrente, è possibile configurare le impostazioni di Git per usare un repository diverso. È quindi possibile importare le risorse di Data Factory esistenti nel nuovo repository.

![Rimuovere l'associazione al repository Git corrente.](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Usare il controllo della versione
I sistemi di controllo della versione (o del _controllo del codice sorgente_) consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

Ogni repository GIT di VSTS associato a una data factory ha un ramo di collaborazione. (`master` è il ramo di collaborazione predefinito). Gli utenti possono anche creare rami funzionalità facendo clic su **Aggiungi nuovo ramo** e sviluppando i rami funzionalità.

![Modificare il codice tramite sincronizzazione o pubblicazione](media/author-visually/sync-publish.png)

Quando si è pronti con lo sviluppo di funzionalità nel ramo funzionalità, è possibile fare clic su **Crea richiesta pull**. Verrà visualizzata la pagina GIT di VSTS, in cui è possibile generare richieste pull, effettuare revisioni del codice e unire le modifiche nel ramo di collaborazione. (`master` è l'impostazione predefinita). Dal ramo di collaborazione è possibile solo eseguire la pubblicazione nel servizio Data Factory. 

![Creare una nuova richiesta pull](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice
Dopo aver unito le modifiche nel ramo di collaborazione (`master` è l'impostazione predefinita), selezionare **Pubblica** per pubblicare manualmente le modifiche al codice nel ramo master del servizio Data Factory.

![Pubblicare le modifiche nel servizio Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Il ramo principale non è rappresentativo di ciò che viene distribuito nel servizio Data Factory. Il ramo master *deve* essere pubblicato manualmente nel servizio Data Factory.

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
