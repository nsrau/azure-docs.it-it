---
title: Panoramica del portale di Azure | Microsoft Docs
description: Scopri come esplorare il portale di Azure e usarlo per gestire i servizi
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000349"
---
# <a name="azure-portal-overview"></a>Panoramica del portale di Azure

Questo articolo introduce la portale di Azure, identifica gli elementi della pagina del portale e consente di acquisire familiarità con l'esperienza di gestione portale di Azure.

## <a name="what-is-the-azure-portal"></a>Qual è la portale di Azure?

Il portale di Azure è una console unificata basata sul Web che fornisce un'alternativa agli strumenti da riga di comando. Con la portale di Azure è possibile gestire la sottoscrizione di Azure usando un'interfaccia utente grafica. Puoi creare, gestire e monitorare tutti gli elementi, dalle semplici app Web alle distribuzioni cloud complesse, creare dashboard personalizzati per una visualizzazione organizzata delle risorse e configurare le opzioni di accessibilità per un'esperienza ottimale.

Il portale di Azure è progettato per la resilienza e la disponibilità continua. Dispone di una presenza in ogni data center di Azure, rendendola resiliente agli errori dei singoli data center e, inoltre, evita il rallentamento della rete in quanto si avvicina agli utenti. Il portale di Azure viene aggiornato continuamente e non richiede alcun tempo di inattività per le attività di manutenzione.

## <a name="azure-home"></a>Home page di Azure

Un nuovo abbonato ai servizi di Azure, la prima cosa che viene visualizzato dopo [l'accesso al portale](https://portal.azure.com) è **Azure Home**. Questa pagina consente di compilare risorse che consentono di ottenere il massimo dalla sottoscrizione di Azure. Sono stati inclusi collegamenti a corsi online gratuiti, documentazione, servizi di base e siti utili per rimanere aggiornati e gestire le modifiche per la propria organizzazione. Per accedere in modo semplice e rapido al lavoro in corso, viene visualizzato anche un elenco delle risorse visitate più di recente. Non è possibile personalizzare questa pagina, ma è possibile scegliere se visualizzare la **Home page di Azure** o il **dashboard di Azure** come visualizzazione predefinita. La prima volta che si esegue l'accesso, nella parte superiore della pagina è disponibile un prompt in cui è possibile salvare le preferenze.

![Screenshot che mostra il selettore di visualizzazione predefinito](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Dashboard di Azure

I dashboard offrono una visualizzazione mirata delle risorse della sottoscrizione più importanti per l'utente. È stato fornito un dashboard predefinito per iniziare. È possibile personalizzare questo dashboard per riportare le risorse più utilizzate in un'unica visualizzazione. Qualsiasi modifica apportata alla visualizzazione predefinita influisce solo sull'esperienza utente. Tuttavia, è possibile creare dashboard aggiuntivi per uso personale o pubblicare i dashboard personalizzati e condividerli con altri utenti nell'organizzazione. Per ulteriori informazioni, vedere [la pagina relativa alla creazione e alla condivisione di dashboard nel portale di Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Come spostarsi nel portale

È utile comprendere il layout del portale di base e come interagire con esso. Qui verranno introdotti i componenti dell'interfaccia utente e alcuni dei termini usati per fornire istruzioni. Per una panoramica più dettagliata del portale, vedere la lezione sul corso [esplorare il portale](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Il portale di Azure Sidebar e l'intestazione di pagina sono elementi globali sempre presenti. Queste funzionalità permanenti sono la "Shell" per l'interfaccia utente associata a ogni singolo servizio o funzionalità e l'intestazione fornisce l'accesso ai controlli globali. La pagina di configurazione (talvolta definita "pannello") per una risorsa può avere anche un riquadro sinistro che consente di spostarsi tra le funzionalità.

Nella figura seguente vengono etichettati gli elementi di base dell'portale di Azure, ciascuno dei quali è descritto nella tabella seguente.

![Screenshot che mostra la visualizzazione e la chiave del portale a schermo intero per gli elementi dell'interfaccia utente](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Chiave|Descrizione
|:---:|---|
|1|Intestazione di pagina. Viene visualizzato nella parte superiore di ogni pagina del portale e include gli elementi globali.|
|2| Ricerca globale. Usare la barra di ricerca per trovare rapidamente una risorsa specifica, un servizio o una documentazione.|
|3|Controlli globali. Come tutti gli elementi globali, queste funzionalità vengono mantenute nel portale e includono: Cloud Shell, filtro delle sottoscrizioni, notifiche, impostazioni del portale, guida e supporto tecnico e inviare commenti e suggerimenti.|
|4|L'account. Visualizzare le informazioni sull'account, cambiare directory, disconnettersi o accedere con un account diverso.|
|5|Sidebar. Il Sidebar è un elemento globale che consente di spostarsi tra i servizi. È possibile comprimere l'intestazione laterale per fornire maggiore attenzione al riquadro di lavoro.|
|6|Controllo master per la creazione di una nuova risorsa nella sottoscrizione corrente. Cercare o esplorare Azure Marketplace per il tipo di risorsa che si vuole creare.|
|7|Elenco Preferiti. Aggiungere o rimuovere preferiti dalla pagina **tutti i servizi** .|
|8|Riquadro sinistro. Molti servizi includono un menu del riquadro sinistro che consente di gestire il servizio.|
|9|Barra degli ordini. I controlli sulla barra dei comandi sono contestuale allo stato attivo corrente.|
|10|Breadcrumb. È possibile utilizzare i collegamenti di navigazione per spostare di nuovo un livello nel flusso di lavoro.|
|11|Riquadro di lavoro.  Visualizza i dettagli relativi alla risorsa attualmente attiva.|

## <a name="get-started-with-services"></a>Inizia a usare i servizi

Se si è un nuovo abbonato, sarà necessario creare una risorsa prima di poter gestire qualsiasi elemento. Selezionare **+ Crea una risorsa** per visualizzare i servizi disponibili in Azure Marketplace. Qui sono disponibili applicazioni e servizi di centinaia di provider, tutti certificati per l'esecuzione in Azure.

I preferiti sono stati precompilati nella sidebar con i collegamenti ai servizi usati di frequente.  Per visualizzare tutti i servizi disponibili, selezionare **tutti i servizi** dalla barra laterale.

> [!TIP]
> Il modo più rapido per trovare una risorsa, un servizio o una documentazione consiste nell'usare la *ricerca* nell'intestazione globale. Usare i collegamenti di navigazione per tornare alle pagine precedenti.
>
Guardare questo video per una demo su come usare la ricerca globale nella portale di Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Come usare la ricerca globale nella portale di Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più su dove eseguire portale di Azure nei [browser e nei dispositivi supportati](../azure-portal/azure-portal-supported-browsers-devices.md)

* Rimanere connessi con [app per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
