---
title: Panoramica del portale di Azure | Microsoft Docs
description: Informazioni su come esplorare il portale di Azure e usarlo per gestire i servizi
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: de04b461d8e2d2dce9a88052a86fbe5d163fafec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244194"
---
# <a name="azure-portal-overview"></a>Panoramica del portale di Azure

Questo articolo introduce il portale di Azure, identifica gli elementi di pagina del portale e consente di acquisire familiarità con l'esperienza di gestione del portale di Azure.

## <a name="what-is-the-azure-portal"></a>Che cos'è il portale di Azure?

Il portale di Azure è una console basata sul web e unificata che offre un'alternativa agli strumenti da riga di comando. Nel portale di Azure, è possibile gestire la sottoscrizione di Azure usando un'interfaccia utente grafica. È possibile compilare, gestire e monitorare tutti i dati da semplici App web e le distribuzioni cloud complessi, creare dashboard personalizzati per una vista organizzata delle risorse e configurare le opzioni di accessibilità per risultati ottimali.

Il portale di Azure è progettato per la resilienza e disponibilità continua. Ha una presenza in ogni Data Center di Azure in modo da ottenere resilienti agli errori di singoli Data Center e consente anche di evitare rallentamenti rete deve trovarsi vicino agli utenti. Il portale di Azure vengono aggiornati continuamente e non richiede tempi di inattività per le attività di manutenzione.

## <a name="azure-home"></a>Azure Home

Come nuovo abbonato a servizi di Azure, il primo oggetto visualizzato dopo aver [Accedi al portale](https://portal.azure.com) viene **home page di Azure**. Questa compilazione di una pagina delle risorse che consentono di sfruttare al meglio la sottoscrizione di Azure. Abbiamo incluso collegamenti per liberare i corsi online, documentazione, servizi di base e modificare siti utili per rimanere correnti e la gestione dell'organizzazione. Per l'accesso rapido e semplice lavorare in corso, viene inoltre illustrato un elenco delle risorse di visitato più di recente. Non è possibile personalizzare questa pagina, ma è possibile scegliere se visualizzare **home page di Azure** oppure **Dashboard di Azure** come visualizzazione predefinita. La prima volta che si accede, viene richiesta nella parte superiore della pagina in cui è possibile salvare le preferenze.

![Seleziona visualizzazione predefinita di schermata che mostra](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Dashboard di Azure

I dashboard offrono una visualizzazione dettagliata delle risorse nella sottoscrizione che è importante più all'utente. Ti forniamo un dashboard predefinito per iniziare a usare. È possibile personalizzare il dashboard per visualizzare le risorse che usi più in una singola visualizzazione. Le modifiche apportate alla visualizzazione predefinita interessano solo l'esperienza. Tuttavia, possibile creare altri dashboard per uso personale o pubblicare i dashboard personalizzati e condividerli con altri utenti nell'organizzazione. Per altre informazioni, vedere [creare e condividere i dashboard nel portale di Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Aggirando il portale

È utile comprendere il layout di base del portale e come interagire con esso. In questo caso, verrà presentato i componenti dell'interfaccia utente e alcuni termini che viene usato per fornire istruzioni. Per una panoramica più dettagliata del portale, vedere la lezione course [nel portale passare](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

La barra laterale del portale Azure e l'intestazione di pagina sono gli elementi globali che sono sempre presenti. Queste funzionalità persistenti sono "shell" per l'interfaccia utente associata a ogni singolo servizio o funzionalità e l'intestazione fornisce l'accesso a controlli globali. La pagina di configurazione (talvolta detta "blade") per una risorsa può anche avere un riquadro a sinistra per passare tra le funzionalità.

La figura seguente vengono etichettati gli elementi di base del portale di Azure, ognuno dei quali è descritti nella tabella seguente.

![Screenshot della visualizzazione nel portale a schermo intero e la chiave per gli elementi dell'interfaccia utente](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Chiave|Descrizione
|:---:|---|
|1|Intestazione di pagina. Viene visualizzata nella parte superiore di ogni pagina del portale e contiene gli elementi globali.|
|2| Ricerca globale. Utilizzare la barra di ricerca per trovare rapidamente una risorsa specifica, un servizio o documentazione.|
|3|Controlli globali. Come tutti gli elementi globali, queste funzionalità persistono il portale e includono: Cloud Shell, filtro di sottoscrizione, le notifiche, impostazioni del portale, Guida e supporto e inviare commenti e suggerimenti.|
|4|L'account. Visualizzare le informazioni relative all'account, passare alla directory, disconnettersi o accedere con un account diverso.|
|5|Nella barra laterale. L'intestazione laterale è un elemento globale che consente di spostarsi tra i servizi. L'intestazione laterale può essere compresse per attivare altre nel riquadro di lavoro.|
|6|Controllo master per creare una nuova risorsa nella sottoscrizione corrente. Cercare o sfogliare il Marketplace di Azure per il tipo di risorsa da creare.|
|7|Nell'elenco dei Preferiti. Aggiungere o rimuovere Preferiti dal **tutti i servizi** pagina.|
|8|Riquadro a sinistra. Molti servizi includono un menu riquadro sinistro per semplificare la gestione del servizio.|
|9|Barra dei comandi. I controlli sulla barra dei comandi sono contestuali per lo stato attivo corrente.|
|10|Barra di navigazione. È possibile usare i collegamenti di navigazione per riportare un livello del flusso di lavoro.|
|11|Riquadro di lavoro.  Visualizza i dettagli sulla risorsa che è attualmente in stato attivo.|

## <a name="get-started-with-services"></a>Introduzione a servizi

Se sei un nuovo sottoscrittore, è possibile creare una risorsa, prima ci siano operazioni da gestire. Selezionare **+ crea una risorsa** per visualizzare i servizi disponibili in Azure Marketplace. Troverai applicazioni e servizi da centinaia di fornitori in questo caso, tutti i certificati per l'esecuzione in Azure.

Abbiamo pre-popolato i Preferiti nella barra laterale con collegamenti ai servizi più diffusi.  Per visualizzare tutti i servizi disponibili, selezionare **tutti i servizi** dalla barra laterale.

> [!TIP]
> Il modo più rapido per trovare una risorsa, servizio o documentazione è usare *ricerca* nell'intestazione globale. Usare i collegamenti di navigazione per tornare alle pagine precedenti.
>

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su dove eseguire il portale di Azure in [browser e dispositivi supportati](../azure-portal/azure-portal-supported-browsers-devices.md)

* Rimanere connessi ovunque con [app per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
