---
title: Panoramica del portale di Azure | Microsoft Docs
description: Il portale di Azure è un'interfaccia utente grafica che è possibile usare per gestire i servizi di Azure.The Azure portal is a graphical user interface that you can use to manage your Azure services. Informazioni su come esplorare e trovare risorse nel portale di Azure.Learn how to navigate and find resources in the Azure portal.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310731"
---
# <a name="azure-portal-overview"></a>Panoramica del portale di Azure

Questo articolo introduce il portale di Azure, identifica gli elementi della pagina del portale e consente di acquisire familiarità con l'esperienza di gestione del portale di Azure.This article introduces the Azure portal, identifies portal page elements, and helps you get familiar with the Azure portal management experience.

## <a name="what-is-the-azure-portal"></a>Che cos'è il portale di Azure?

Il portale di Azure è una console unificata basata sul Web che offre un'alternativa agli strumenti da riga di comando. Con il portale di Azure è possibile gestire la sottoscrizione di Azure usando un'interfaccia utente grafica. È possibile creare, gestire e monitorare tutto, dalle semplici app Web alle distribuzioni cloud complesse. Creare dashboard personalizzati per una visualizzazione organizzata delle risorse. Configurare le opzioni di accessibilità per un'esperienza ottimale.

Il portale di Azure è progettato per la resilienza e la disponibilità continua. Ha una presenza in ogni data center di Azure.It has a presence in every Azure datacenter. Questa configurazione rende il portale di Azure resiliente agli errori dei singoli data center ed evita i rallentamenti della rete essendo vicini agli utenti. Il portale di Azure viene aggiornato continuamente e non richiede tempi di inattività per le attività di manutenzione.

## <a name="azure-portal-menu"></a>Menu del portale di AzureAzure portal menu

È possibile scegliere la modalità predefinita per il menu del portale. Può essere ancorato o può agire come un pannello a comparsa.

Quando il menu del portale è in modalità riquadro a comparsa, viene nascosto fino a quando non è necessario. Selezionare l'icona del menu per aprire o chiudere il menu.

![Menu del portale di Azure in modalità riquadro a comparsaAzure portal menu in flyout mode](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se si sceglie la modalità ancorata per il menu del portale, sarà sempre visibile. È possibile comprimere il menu per fornire più spazio di lavoro.

![Menu del portale di Azure in modalità ancorataAzure portal menu in docked mode](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure Home

Come nuovo sottoscrittore dei servizi di Azure, la prima cosa che viene visualizzata dopo [l'accesso al portale](https://portal.azure.com) è **Home di Azure.** Questa pagina compila le risorse che consentono di ottenere il massimo dalla sottoscrizione di Azure.This page compiles resources that help you get the most from your Azure subscription. Abbiamo incluso collegamenti a corsi online gratuiti, documentazione, servizi di base e siti utili per rimanere aggiornati e gestire il cambiamento per la tua organizzazione. Per un accesso rapido e semplice ai lavori in corso, mostriamo anche un elenco delle risorse visitate più di recente. Non è possibile personalizzare questa pagina, ma è possibile scegliere se visualizzare **Home page** di Azure o Dashboard di **Azure** come visualizzazione predefinita. Al primo accesso, viene visualizzato un prompt nella parte superiore della pagina in cui è possibile salvare le preferenze.

![Schermata che mostra il selettore di visualizzazione predefinito](./media/azure-portal-overview/azure-portal-default-view.png)

Sia il menu del portale di Azure che la visualizzazione predefinita di Azure possono essere modificati in **Impostazioni portale.** Se si modifica la selezione, la modifica viene applicata immediatamente.

![Schermata che mostra il selettore di visualizzazione predefinito](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Dashboard di Azure

I dashboard offrono una visualizzazione mirata delle risorse della sottoscrizione più importanti per l'utente. Ti abbiamo fornito un dashboard predefinito per iniziare. È possibile personalizzare questo dashboard per portare le risorse utilizzate di frequente in un'unica visualizzazione. Le modifiche apportate alla visualizzazione predefinita influiscono solo sulla tua esperienza. Tuttavia, è possibile creare dashboard aggiuntivi per uso personale o pubblicare i dashboard personalizzati e condividerli con altri utenti dell'organizzazione. Per altre informazioni, vedere Creare e condividere dashboard nel portale di Azure.For more information, see [Create and share dashboards in the Azure portal.](../azure-portal/azure-portal-dashboards.md)

## <a name="getting-around-the-portal"></a>Come spostarsi nel portale

È utile comprendere il layout di base del portale e come interagire con esso. Qui, introdurremo i componenti dell'interfaccia utente e parte della terminologia che usiamo per dare istruzioni. Per una presentazione più dettagliata del portale, vedere la lezione del corso [Esplorare il portale](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Il menu del portale di Azure e l'intestazione di pagina sono elementi globali sempre presenti. Queste funzionalità permanenti sono la "shell" per l'interfaccia utente associata a ogni singolo servizio o funzionalità e l'intestazione fornisce l'accesso ai controlli globali. La pagina di configurazione (talvolta definita "blade") per una risorsa può anche disporre di un menu di risorse che consente di spostarsi tra le funzionalità.

La figura seguente etichetta gli elementi di base del portale di Azure, ognuno dei quali è descritto nella tabella seguente.

![Schermata della visualizzazione del portale a schermo intero e della chiave per gli elementi dell'interfaccia utente](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Schermata del menu del portale espanso](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chiave|Descrizione
|:---:|---|
|1|Intestazione della pagina. Viene visualizzato nella parte superiore di ogni pagina del portale e contiene elementi globali.|
|2| Ricerca globale. Utilizzare la barra di ricerca per trovare rapidamente una risorsa specifica, un servizio o una documentazione.|
|3|Controlli globali. Come tutti gli elementi globali, queste funzionalità persistono nel portale e includono: Cloud Shell, filtro sottoscrizioni, notifiche, impostazioni del portale, guida e supporto e inviaci commenti e suggerimenti.|
|4|Il tuo account. Visualizzare informazioni sull'account, passare da una directory all'altra, disconnettersi o accedere con un altro account.|
|5|dal menu Portale. Il menu del portale è un elemento globale che consente di spostarsi tra i servizi. Talvolta definita barra laterale, la modalità del menu del portale può essere modificata nelle **impostazioni del portale.**|
|6|dal menu Risorsa. Molti servizi includono un menu delle risorse per facilitare la gestione del servizio. È possibile vedere questo elemento indicato come riquadro sinistro.|
|7|Barra dei comandi. I controlli sulla barra dei comandi sono contestuali allo stato attivo corrente.|
|8|Riquadro di lavoro.  Visualizza i dettagli relativi alla risorsa attualmente attiva.|
|9|Breadcrumb. È possibile utilizzare i collegamenti di navigazione per spostarsi indietro di un livello nel flusso di lavoro.|
|10|Controllo master per creare una nuova risorsa nella sottoscrizione corrente. Espandere o aprire il menu del portale per trovare **: Creare una risorsa**. Cercare o esplorare il tipo di risorsa che si vuole creare in Azure Marketplace.|
|11|La tua lista dei preferiti. Per informazioni su come personalizzare l'elenco, vedere [Aggiungere, rimuovere e ordinare i preferiti.](../azure-portal/azure-portal-add-remove-sort-favorites.md)|

## <a name="get-started-with-services"></a>Introduzione ai servizi

Se sei un nuovo sottoscrittore, dovrai creare una risorsa prima che ci sia qualcosa da gestire. Selezionare **Crea una risorsa** per visualizzare i servizi disponibili in Azure Marketplace.Select : Create a resource to view the services available in the Azure Marketplace. Qui troverai applicazioni e servizi di centinaia di provider, tutti certificati per l'esecuzione in Azure.You'll find applications and services from hundreds of providers here, all certified to run on Azure.

Abbiamo pre-popolato i preferiti nella barra laterale con link ai servizi di uso comune.  Per visualizzare tutti i servizi disponibili, seleziona **Tutti i servizi** dalla barra laterale.

> [!TIP]
> Il modo più rapido per trovare una risorsa, un servizio o una documentazione consiste nell'usare *Cerca* nell'intestazione globale. Utilizzare i collegamenti di navigazione per tornare alle pagine precedenti.
>
Guardare questo video per una demo su come usare la ricerca globale nel portale di Azure.Watch this video for a demo on how to use global search in the Azure portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Come usare la ricerca globale nel portale di AzureHow to use global search in the Azure portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su dove eseguire il portale di Azure, vedere [Browser e dispositivi supportatiLearn](../azure-portal/azure-portal-supported-browsers-devices.md) more about where to run Azure portal in Supported browsers and devices
* Rimani connesso in viaggio con [l'app per dispositivi mobili](https://azure.microsoft.com/features/azure-portal/mobile-app/) di Azure
* Onboarding e configurazione dell'ambiente cloud con il [Centro guide introduttive di Azure](../azure-portal/azure-portal-quickstart-center.md)
