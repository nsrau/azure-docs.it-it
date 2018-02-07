---
title: Creare e modificare data factory di Azure con strumenti visivi | Microsoft Docs
description: Informazioni su come creare e modificare data factory di Azure usando strumenti visivi
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>Creare e modificare data factory con strumenti visivi
Con l'esperienza utente di Azure Data Factory, è possibile usare strumenti visivi per creare o modificare e distribuire risorse in una data factory senza scrivere nemmeno una riga di codice. Questa interfaccia senza codice consente di trascinare e rilasciare le attività in un'area di disegno della pipeline, eseguire test, eseguire il debug in modo iterativo, nonché distribuire e monitorare le esecuzioni della pipeline. È possibile scegliere di usare lo strumento ADF UX in due modi:

1. Eseguire le operazioni di creazione e modifica direttamente con il servizio Data Factory
2. Configurare l'integrazione di GIT in VSTS (Visual Studio Team Services) per la collaborazione, il controllo del codice sorgente o il controllo delle versioni

## <a name="authoring-with-data-factory"></a>Creazione e modifica con Data Factory
La prima soluzione consiste nell'usare direttamente Data Factory. Questo approccio è diverso rispetto all'uso del repository di codice di VSTS, poiché non è disponibile alcun repository in cui archiviare le entità JSON delle modifiche e inoltre la soluzione non è ottimizzata per la collaborazione o il controllo della versione.

![Configurare Data Factory](media/author-visually/configure-data-factory.png)

In Data Factory è disponibile solo la modalità di pubblicazione. Le modifiche apportate vengono pubblicate direttamente nel servizio Data Factory.

![Pubblicazione di Data Factory](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>Creazione e modifica con l'integrazione di GIT in VSTS
La soluzione che prevede l'uso dell'integrazione di GIT in VSTS offre funzionalità per il controllo del codice sorgente e la collaborazione durante la creazione e la modifica delle pipeline di data factory. Gli utenti hanno la possibilità di associare una data factory a un repository di un account GIT di VSTS per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Un account GIT di VSTS può avere più repository, ma un repository GIT di VSTS può essere associato a una sola data factory. Se non si sono ancora configurati un account e un repository di VSTS, crearli seguendo le istruzioni riportate in [questo articolo](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student).

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Configurare un repository GIT di VSTS con Azure Data Factory
Gli utenti possono configurare un repository GIT di VSTS con una data factory seguendo due diversi metodi.

#### <a name="method-1-lets-get-started-page"></a>Metodo 1: Pagina "Attività iniziali"

Passare alla pagina "Attività iniziali" e fare clic su "Configure Code Repository" (Configura repository di codice)

![Configurare il repository di codice](media/author-visually/configure-repo.png)

Verrà visualizzato un pannello laterale per configurare le impostazioni del repository.

![Configurare le impostazioni del repository](media/author-visually/repo-settings.png)
* **Tipo di repository**: Visual Studio Team Services Git (attualmente Github non è supportato).
* **Account di Visual Studio Team Services**: il nome dell'account può essere ricavato dall'indirizzo https://{nome account}.visualstudio.com. Accedere all'account di VSTS da [questa pagina](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio per visualizzare il repository e i progetti.
* **Nome progetto**: il nome del progetto può essere ricavato dall'indirizzo https://{nome account}.visualstudio.com/{nome progetto}.
* **Nome del repository**: i progetti di VSTS contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. Creare un nuovo repository o usare un repository già presente nel progetto.
* **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository): selezionando questa casella, è possibile importare le risorse correnti della data factory, modificate nell'area di disegno dell'esperienza utente, nel repository GIT di VSTS associato in formato JSON. Questa operazione consente di esportare ogni risorsa singolarmente. In altre parole, i servizi e i set di dati collegati vengono esportati in file JSON separati.    Se si deseleziona questa casella di controllo, le risorse esistenti non vengono importate nel repository GIT.

#### <a name="method-2-from-authoring-canvas"></a>Metodo 2: Area di disegno

Nell'area di disegno fare clic sul menu a discesa "Data Factory" sotto il nome della data factory. Fare quindi clic su "Configure Code Repository" (Configura repository di codice). Come per il **metodo 1**, verrà visualizzato un pannello laterale per configurare le impostazioni del repository. Per informazioni sulle impostazioni, vedere le sezioni precedenti.

![Configurare il repository di codice 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>Controllo della versione
I sistemi di controllo della versione, o del codice sorgente, consentono agli sviluppatori di collaborare alla creazione e alla modifica del codice e di tenere traccia delle modifiche apportate alla codebase. Il controllo del codice sorgente è uno strumento essenziale per i progetti in cui sono coinvolti più sviluppatori.

Una volta associato a una data factory, ogni repository GIT di VSTS ha un ramo master. Da questo ramo, ogni utente con diritti di accesso al repository ha a disposizione due opzioni per implementare le modifiche: la sincronizzazione e la pubblicazione.

![Sincronizzazione e pubblicazione](media/author-visually/sync-publish.png)

#### <a name="sync"></a>Sincronizzazione

Se si sceglie di eseguire la sincronizzazione, è possibile eseguire il pull delle modifiche dal ramo master a quello locale oppure eseguirne il push dal ramo locale a quello master.

![Sincronizzazione delle modifiche](media/author-visually/sync-change.png)

#### <a name="publish"></a>Pubblicazione
 Se si sceglie di eseguire la pubblicazione, le modifiche apportate al ramo master vengono pubblicate nel servizio Data Factory.

> [!NOTE]
> Il **ramo master non è rappresentativo dell'implementazione nel servizio Data Factory**. Il ramo master *deve* essere pubblicato manualmente nel servizio Data Factory.




## <a name="expression-language"></a>Linguaggio di espressione

Gli utenti possono specificare espressioni per definire i valori delle proprietà usando il linguaggio di espressione supportato da Azure Data Factory. Per altre informazioni sulle espressioni supportate, vedere [Espressioni e funzioni in Azure Data Factory](control-flow-expression-language-functions.md).

La schermata seguente illustra come specificare espressioni nei valori delle proprietà usando l'esperienza utente.

![Linguaggio di espressione](media/author-visually/expression-language.png)

## <a name="parameters"></a>Parametri
È possibile specificare i parametri per pipeline e set di dati nella scheda "Parametri". I parametri possono inoltre essere utilizzati facilmente nelle proprietà selezionando "Aggiungi contenuto dinamico".

![Contenuto dinamico](media/author-visually/dynamic-content.png)

Da questa area è possibile utilizzare un parametro esistente o specificare un nuovo parametro nel valore della proprietà.

![Parametri](media/author-visually/parameters.png)

## <a name="feedback"></a>Commenti e suggerimenti
Fare clic sull'icona "Commenti e suggerimenti" per inviare a Microsoft un parere sulle diverse funzionalità o su eventuali problemi riscontrati.

![Commenti e suggerimenti](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
