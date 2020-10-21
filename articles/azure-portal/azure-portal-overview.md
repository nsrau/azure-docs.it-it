---
title: Panoramica del portale di Azure | Microsoft Docs
description: Il portale di Azure è un'interfaccia utente grafica che si può usare per gestire i servizi di Azure. Informazioni su come esplorare e trovare le risorse nel portale di Azure.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: overview
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 24b249d1098788e518f5eda2d1b0d421d4e5e6bb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072391"
---
# <a name="azure-portal-overview"></a>Panoramica del portale di Azure

Questo articolo introduce il portale di Azure, identifica gli elementi della pagina del portale e consente di acquisire familiarità con l'esperienza di gestione del portale di Azure.

## <a name="what-is-the-azure-portal"></a>Che cos'è il portale di Azure?

Il portale di Azure è una console unificata basata sul Web che offre un'alternativa agli strumenti da riga di comando. Con il portale di Azure è possibile gestire la sottoscrizione di Azure usando un'interfaccia utente grafica. È possibile creare, gestire e monitorare praticamente tutto, da semplici app Web fino a complesse distribuzioni cloud. Creare dashboard personalizzati per una visualizzazione organizzata delle risorse. Configurare le opzioni di accessibilità per un'esperienza ottimale.

Il portale di Azure è progettato per la resilienza e la disponibilità continua. È presente in ogni data center di Azure. Questa configurazione rende il portale di Azure resiliente agli errori dei singoli data center ed evita i rallentamenti della rete essendo vicina agli utenti. Il portale di Azure viene aggiornato continuamente e non richiede alcun tempo di inattività per le attività di manutenzione.

## <a name="azure-portal-menu"></a>Menu del portale di Azure

È possibile scegliere la modalità predefinita per il menu del portale. Può essere ancorato o essere usato come pannello a comparsa.

Quando il menu del portale è in modalità a comparsa, è nascosto fino a quando non è necessario. Selezionare l'icona del menu per aprire o chiudere il menu.

![Menu del portale di Azure in modalità a comparsa](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se si sceglie la modalità ancorata, il menu del portale sarà sempre visibile. È possibile comprimere il menu per fornire maggiore spazio di lavoro.

![Menu del portale di Azure in modalità ancorata](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Home page di Azure

Come nuovo abbonato ai servizi di Azure, la prima cosa che viene visualizzata dopo [aver effettuato l'accesso al portale](https://portal.azure.com) è la **Home page di Azure**. Questa pagina raccoglie le risorse che consentono di ottenere il massimo dalla sottoscrizione di Azure. Sono stati inclusi collegamenti a corsi online gratuiti, documentazione, servizi di base e siti utili per rimanere aggiornati e gestire le modifiche per la propria organizzazione. Per accedere in modo semplice e rapido ai lavori in corso, viene visualizzato anche un elenco delle risorse visitate più di recente. Non è possibile personalizzare questa pagina, ma è possibile scegliere se visualizzare la **Home page di Azure** o il **Dashboard di Azure** come visualizzazione predefinita. La prima volta che si esegue l'accesso, nella parte superiore della pagina è disponibile un prompt in cui è possibile salvare le preferenze.

![Screenshot che mostra il selettore della visualizzazione predefinita](./media/azure-portal-overview/azure-portal-default-view.png)

Sia il menu del portale di Azure che la visualizzazione predefinita di Azure possono essere modificati in **Impostazioni del portale**. Se si modifica la selezione, la modifica viene applicata immediatamente.

![Screenshot che mostra il selettore della visualizzazione predefinita](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Dashboard di Azure

I dashboard offrono una visualizzazione mirata delle risorse della sottoscrizione più importanti per l'utente. È stato fornito un dashboard predefinito per iniziare. È possibile personalizzare questo dashboard per avere le risorse usate di frequente in un'unica visualizzazione. Qualsiasi modifica apportata alla visualizzazione predefinita influisce solo sull'esperienza utente. Tuttavia, è possibile creare dashboard aggiuntivi per uso personale o pubblicare i dashboard personalizzati e condividerli con altri utenti dell'organizzazione. Per altre informazioni, vedere [Creare e condividere dashboard nel portale di Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Spostarsi nel portale

È utile comprendere il layout del portale di base e come interagirvi. Qui verranno introdotti i componenti dell'interfaccia utente e alcuni dei termini usati per fornire istruzioni. Per una panoramica più dettagliata del portale, vedere la lezione del corso [Esplorare il portale](/learn/modules/tour-azure-portal/3-navigate-the-portal).

Il menu del portale di Azure e l'intestazione di pagina sono elementi globali sempre presenti. Queste funzionalità permanenti rappresentano la "shell" dell'interfaccia utente associata a ogni singolo servizio o funzionalità e l'intestazione fornisce l'accesso ai controlli globali. La pagina di configurazione (talvolta definita "pannello") di una risorsa può includere anche un menu delle risorse che consente di spostarsi tra le funzionalità.

La figura seguente riporta gli elementi di base del portale di Azure, ciascuno dei quali è descritto nella tabella seguente.

![Screenshot che mostra la visualizzazione del portale a schermo intero con gli elementi dell'interfaccia utente contrassegnati](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Screenshot che mostra il menu del portale espanso](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chiave|Descrizione
|:---:|---|
|1|Intestazione della pagina. Viene visualizzata nella parte superiore di ogni pagina del portale e include gli elementi globali.|
|2| Ricerca globale. Usare la barra di ricerca per trovare rapidamente una risorsa, un servizio o una documentazione specifici.|
|3|Controlli globali. Come tutti gli elementi globali, queste funzionalità vengono mantenute nel portale e includono: Cloud Shell, filtro della sottoscrizione, notifiche, impostazioni del portale, guida e supporto tecnico e invio di commenti e suggerimenti.|
|4|Il proprio account. Consente di visualizzare le informazioni sull'account, cambiare directory, disconnettersi o accedere con un account diverso.|
|5|Menu del portale. Il menu del portale è un elemento globale che consente di spostarsi tra i servizi. A volte indicato come barra laterale, la modalità del menu del portale può essere modificata in **Impostazioni del portale**.|
|6|Menu delle risorse. Molti servizi includono un menu delle risorse che consente di gestire il servizio. Questo elemento può essere indicato come riquadro sinistro.|
|7|Barra dei comandi. I controlli sulla barra dei comandi sono contestuali allo stato attivo corrente.|
|8|Riquadro di lavoro.  Visualizza i dettagli relativi alla risorsa attualmente attiva.|
|9|Percorso di navigazione. È possibile usare i collegamenti di navigazione per tornare indietro di un livello nel flusso di lavoro.|
|10|Controllo principale per creare una nuova risorsa nella sottoscrizione corrente. Espandere o aprire il menu del portale per trovare **+ Crea una risorsa**. Cercare o esplorare Azure Marketplace per il tipo di risorsa che si vuole creare.|
|11|Elenco dei preferiti. Vedere [Aggiungere, rimuovere e ordinare i preferiti](../azure-portal/azure-portal-add-remove-sort-favorites.md) per informazioni su come personalizzare l'elenco.|

## <a name="get-started-with-services"></a>Introduzione ai servizi

Se si è un nuovo abbonato, sarà necessario creare una risorsa prima di poter gestire qualsiasi elemento. Selezionare **+ Crea una risorsa** per visualizzare i servizi disponibili in Azure Marketplace. Qui sono disponibili applicazioni e servizi di centinaia di provider, tutti certificati per l'esecuzione in Azure.

Nell'elenco dei preferiti nella barra laterale sono stati inseriti i collegamenti ai servizi di uso comune.  Per visualizzare tutti i servizi disponibili, selezionare **Tutti i servizi** nella barra laterale.

> [!TIP]
> Il modo più rapido per trovare una risorsa, un servizio o una documentazione consiste nell'usare la funzionalità di *ricerca* nell'intestazione globale. Usare i collegamenti di navigazione per tornare alle pagine precedenti.
>
Guardare questo video per una dimostrazione su come usare la ricerca globale nel portale di Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Come usare la ricerca globale nel portale di Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su dove eseguire il portale di Azure nei [browser e dispositivi supportati](../azure-portal/azure-portal-supported-browsers-devices.md)
* Rimanere sempre connessi con l'[app per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Eseguire l'onboarding e configurare l'ambiente cloud con il [Centro di avvio rapido di Azure](../azure-portal/azure-portal-quickstart-center.md)