---
title: Panoramica delle applicazioni gestite di Azure | Microsoft Docs
description: Illustra i concetti relativi alle applicazioni gestite di Azure
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: a26cfc632dacb41435b3755409d3e91630f5f3eb
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="azure-managed-applications-overview"></a>Panoramica delle applicazioni gestite di Azure

Le applicazioni gestite di Azure consentono di offrire soluzioni cloud semplici da distribuire e gestire per i consumer. È sufficiente implementare l'infrastruttura e garantire un supporto continuativo. Per rendere un'applicazione gestita disponibile a tutti i clienti, pubblicarla in Azure Marketplace. Per renderla disponibile ai soli utenti dell'organizzazione, pubblicarla in un catalogo interno. 

Un'applicazione gestita è simile a un modello di soluzione del Marketplace, con una differenza essenziale. In un'applicazione gestita il provisioning delle risorse viene eseguito in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione.

## <a name="advantages-of-managed-applications"></a>Vantaggi delle applicazioni gestite

Le applicazioni gestite riducono le barriere per i consumer, tramite soluzioni utilizzabili senza alcuna esperienza di infrastruttura cloud. I consumer hanno accesso limitato alle risorse critiche e non devono temere di commettere errori di gestione. 

Le applicazioni gestite consentono di stabilire un rapporto continuativo con i consumer. È sufficiente definire le condizioni di gestione dell'applicazione e tutti gli addebiti vengono gestiti con la fatturazione di Azure.

Sebbene i clienti distribuiscano le applicazioni gestite nelle proprie sottoscrizioni, non devono occuparsi della gestione, degli aggiornamenti o della manutenzione. È possibile garantire che tutti i clienti usino versioni approvate. I clienti non devono sviluppare una conoscenza approfondita delle caratteristiche delle applicazioni per gestirle. Inoltre, possono acquisire automaticamente gli aggiornamenti delle applicazioni senza doversi preoccupare di individuare, diagnosticare e risolvere eventuali problemi. 

Per i team IT, le applicazioni gestite consentono di offrire soluzioni approvate agli utenti dell'organizzazione. Assicurarsi che tali soluzioni siano conformi agli standard aziendali.

## <a name="types-of-managed-applications"></a>Tipi di applicazioni gestite

È possibile pubblicare l'applicazione gestita internamente o esternamente.

![Pubblicare internamente o esternamente](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Catalogo di servizi

Il catalogo di servizi è un catalogo interno di soluzioni approvate per gli utenti in un'organizzazione. Il catalogo può essere usato per garantire la conformità a determinati standard aziendali e offrire al contempo soluzioni ottimali per l'organizzazione. I dipendenti lo usano per trovare facilmente la vasta gamma di applicazioni consigliate e approvate dai reparti IT o per visualizzare le applicazioni gestite che altri utenti dell'organizzazione hanno condiviso con loro.

Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare un'applicazione del catalogo di servizi](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Per la fatturazione dei servizi, i fornitori possono rendere disponibile un'applicazione gestita tramite Azure Marketplace. L'applicazione pubblicata dal fornitore è disponibile per gli utenti esterni all'organizzazione. Con questo approccio, i fornitori di servizi gestiti (MSP), i fornitori di software indipendenti (ISV) e gli integratori di sistemi (SI) possono offrire soluzioni a tutti i clienti Azure.

Per informazioni sulla pubblicazione di applicazioni gestite in Azure Marketplace, vedere [Create marketplace application](publish-marketplace-app.md) (Creare un'applicazione del Marketplace).

## <a name="resource-groups-for-managed-applications"></a>Gruppi di risorse per le applicazioni gestite

Le risorse per un'applicazione gestita si trovano in genere in due gruppi di risorse. Il consumer gestisce un gruppo di risorse, mentre l'entità di pubblicazione ne gestisce un altro. Quando si definisce l'applicazione gestita, l'entità di pubblicazione specifica i livelli di accesso. La figura seguente illustra uno scenario in cui l'entità di pubblicazione richiede il ruolo di proprietario per il gruppo di risorse gestite. L'entità di pubblicazione ha inserito un blocco di sola lettura in questo gruppo di risorse per il consumer.

![Accesso al gruppo di risorse](./media/overview/access.png)

### <a name="application-resource-group"></a>Gruppo di risorse dell'applicazione

Il gruppo di risorse contiene l'istanza dell'applicazione gestita. Questo gruppo di risorse può contenere solo una risorsa. Il tipo di risorsa dell'applicazione gestita è **Microsoft.Solutions/applications**.

Il consumer ha accesso completo al gruppo di risorse e lo usa per gestire il ciclo di vita dell'applicazione gestita.

### <a name="managed-resource-group"></a>Gruppo di risorse gestite

Questo gruppo di risorse contiene tutte le risorse richieste dall'applicazione gestita. Ad esempio, le macchine virtuali, gli account di archiviazione e le reti virtuali per la soluzione. Il consumer ha accesso limitato al gruppo di risorse perché non gestisce le singole risorse per l'applicazione gestita. L'accesso al gruppo di risorse dell'entità di pubblicazione corrisponde al ruolo specificato nella definizione dell'applicazione gestita. Ad esempio, l'entità di pubblicazione potrebbe richiedere il ruolo di proprietario o di collaboratore per il gruppo di risorse.

Quando il consumer elimina l'applicazione gestita, viene eliminato anche il gruppo di risorse gestite.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla definizione e alla distribuzione di un'applicazione gestita, vedere [Creare e distribuire un'applicazione gestita di Azure con l'interfaccia della riga di comando di Azure](managed-apps-quickstart-cli.md)
* Per informazioni sulla pubblicazione di un'applicazione interna, vedere [Creare un'applicazione del catalogo di servizi](publish-service-catalog-app.md).
* Per informazioni sulla pubblicazione di applicazioni gestite in Azure Marketplace, vedere [Create marketplace application](publish-marketplace-app.md) (Creare un'applicazione del Marketplace).
