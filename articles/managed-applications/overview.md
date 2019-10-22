---
title: Panoramica delle applicazioni gestite di Azure | Microsoft Docs
description: Descrive i concetti per le applicazioni gestite di Azure, che offrono soluzioni cloud semplici da distribuire e gestire per i consumer.
author: tfitzmac
ms.service: managed-applications
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: fa37536b800203e981a841d049dc385640733f92
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332679"
---
# <a name="azure-managed-applications-overview"></a>Panoramica delle applicazioni gestite di Azure

Le applicazioni gestite di Azure consentono di offrire soluzioni cloud semplici da distribuire e gestire per i consumer. È sufficiente implementare l'infrastruttura e garantire un supporto continuativo. Per rendere un'applicazione gestita disponibile a tutti i clienti, pubblicarla in Azure Marketplace. Per renderla disponibile ai soli utenti dell'organizzazione, pubblicarla in un catalogo interno. 

Un'applicazione gestita è simile a un modello di soluzione del Marketplace, con una differenza essenziale. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione.

## <a name="advantages-of-managed-applications"></a>Vantaggi delle applicazioni gestite

Le applicazioni gestite riducono le barriere per i consumer, tramite soluzioni utilizzabili senza alcuna esperienza di infrastruttura cloud. I consumer hanno accesso limitato alle risorse critiche e non devono temere di commettere errori di gestione. 

Le applicazioni gestite consentono di stabilire un rapporto continuativo con i consumer. È sufficiente definire le condizioni di gestione dell'applicazione e tutti gli addebiti vengono gestiti con la fatturazione di Azure.

Sebbene i clienti distribuiscano le applicazioni gestite nelle proprie sottoscrizioni, non devono occuparsi della gestione, degli aggiornamenti o della manutenzione. È possibile fare in modo che tutti i clienti usino versioni approvate. I clienti non devono sviluppare una conoscenza approfondita delle caratteristiche delle applicazioni per gestirle. Inoltre, possono acquisire automaticamente gli aggiornamenti delle applicazioni senza doversi preoccupare di individuare, diagnosticare e risolvere eventuali problemi. 

Per i team IT, le applicazioni gestite consentono di offrire soluzioni approvate agli utenti dell'organizzazione. Si ha la certezza che tali soluzioni siano conformi agli standard aziendali.

Le applicazioni gestite supportano le [identità gestite per le risorse di Azure](./publish-managed-identity.md).

## <a name="types-of-managed-applications"></a>Tipi di applicazioni gestite

È possibile pubblicare l'applicazione gestita internamente o esternamente.

![Pubblicare internamente o esternamente](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Catalogo di servizi

Il catalogo di servizi è un catalogo interno di soluzioni approvate per gli utenti in un'organizzazione. Il catalogo può essere usato per garantire la conformità agli standard aziendali e offrire al contempo soluzioni ottimali per l'organizzazione. I dipendenti lo usano per trovare facilmente le applicazioni consigliate e approvate dai reparti IT o per visualizzare le applicazioni gestite che altri utenti dell'organizzazione hanno condiviso con loro.

Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare un'applicazione del catalogo di servizi](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Per la fatturazione dei servizi, i fornitori possono rendere disponibile un'applicazione gestita tramite Azure Marketplace. L'applicazione pubblicata dal fornitore è disponibile per gli utenti esterni all'organizzazione. Con questo approccio, i fornitori di servizi gestiti (MSP), i fornitori di software indipendenti (ISV) e gli integratori di sistemi (SI) possono offrire soluzioni a tutti i clienti Azure.

Per informazioni sulla pubblicazione di applicazioni gestite in Azure Marketplace, vedere [Create marketplace application](publish-marketplace-app.md) (Creare un'applicazione del Marketplace).

## <a name="resource-groups-for-managed-applications"></a>Gruppi di risorse per le applicazioni gestite

Le risorse per un'applicazione gestita si trovano in genere in due gruppi di risorse. Il consumer gestisce un gruppo di risorse, mentre l'entità di pubblicazione ne gestisce un altro. Quando si definisce l'applicazione gestita, l'entità di pubblicazione specifica i livelli di accesso. L'entità di pubblicazione può richiedere l'assegnazione di un ruolo permanente oppure l'[accesso JIT](request-just-in-time-access.md) per un'assegnazione vincolata a un periodo di tempo.

La limitazione dell'accesso per [operazioni sui dati](../role-based-access-control/role-definitions.md) non è attualmente supportata per tutti i provider di dati in Azure.

La figura seguente illustra uno scenario in cui l'entità di pubblicazione richiede il ruolo di proprietario per il gruppo di risorse gestite. L'entità di pubblicazione ha inserito un blocco di sola lettura in questo gruppo di risorse per il consumer. Le identità dell'entità di pubblicazione a cui è concesso l'accesso al gruppo di risorse gestite sono esenti dal blocco.

![Accesso al gruppo di risorse](./media/overview/access.png)

### <a name="application-resource-group"></a>Gruppo di risorse dell'applicazione

Il gruppo di risorse contiene l'istanza dell'applicazione gestita. Questo gruppo di risorse può contenere solo una risorsa. Il tipo di risorsa dell'applicazione gestita è **Microsoft.Solutions/applications**.

Il consumer ha accesso completo al gruppo di risorse e lo usa per gestire il ciclo di vita dell'applicazione gestita.

### <a name="managed-resource-group"></a>Gruppo di risorse gestite

Questo gruppo di risorse contiene tutte le risorse richieste dall'applicazione gestita. Ad esempio, le macchine virtuali, gli account di archiviazione e le reti virtuali per la soluzione. Il consumer ha accesso limitato al gruppo di risorse perché non gestisce le singole risorse per l'applicazione gestita. L'accesso al gruppo di risorse dell'entità di pubblicazione corrisponde al ruolo specificato nella definizione dell'applicazione gestita. Ad esempio, l'entità di pubblicazione potrebbe richiedere il ruolo di proprietario o di collaboratore per il gruppo di risorse. L'accesso è permanente o limitato a un periodo di tempo.

Quando pubblica l'[applicazione gestita nel Marketplace](publish-marketplace-app.md), l'autore può concedere agli utenti la possibilità di eseguire azioni specifiche sulle risorse nel gruppo di risorse gestite. L'autore può ad esempio specificare che gli utenti possono riavviare le macchine virtuali. Tutte le altre azioni oltre alle azioni di lettura sono comunque negate.

Quando il consumer elimina l'applicazione gestita, viene eliminato anche il gruppo di risorse gestite.

## <a name="azure-policy"></a>Criteri di Azure

È possibile applicare [criteri di Azure](../governance/policy/overview.md) all'applicazione gestita. L'applicazione di criteri consente di garantire che le istanze distribuite dell'applicazione gestita siano conformi ai requisiti per i dati e la sicurezza. Se l'applicazione interagisce con dati sensibili, assicurarsi di avere valutato come devono essere protetti. Ad esempio, se l'applicazione interagisce con dati di Office 365, applicare criteri per verificare che sia abilitata la crittografia dei dati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si sono visti i vantaggi dell'uso di applicazioni gestite. Nel prossimo articolo si vedrà come creare una definizione di applicazione gestita.

> [!div class="nextstepaction"]
> [Guida introduttiva: Pubblicare una definizione di applicazione gestita di Azure](publish-managed-app-definition-quickstart.md)
