---
title: Creare e condividere i dashboard del portale di Azure | Microsoft Docs
description: Questo articolo illustra come creare e modificare i dashboard nel portale di Azure.
services: azure-portal
documentationcenter: 
author: sewatson
manager: timlt
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 7f90c882285170bac34bfe020831d3ac04010a11
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Creare e condividere dashboard nel portale di Azure
È possibile creare più dashboard e condividerli con altri utenti che hanno accesso alle sottoscrizioni di Azure.  Questo articolo illustra le nozioni di base della creazione, della modifica, della pubblicazione e della gestione dell'accesso dei dashboard.

## <a name="create-a-dashboard"></a>Creare un dashboard
Per creare un dashboard, selezionare il pulsante **Nuovo dashboard** accanto al nome del dashboard corrente.  

![Creare un dashboard](./media/azure-portal-dashboards/new-dashboard.png)

Questa azione crea un nuovo dashboard privato vuoto e attiva la modalità di personalizzazione, in cui è possibile assegnare un nome al dashboard e aggiungere o ridisporre i riquadri.  In questa modalità, nell'area del menu di navigazione a sinistra viene visualizzata la raccolta di riquadri comprimibile.  La raccolta di riquadri consente di trovare i riquadri per le risorse di Azure in vari modi: è possibile eseguire la ricerca per [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups), per tipo di risorsa, per [tag](../azure-resource-manager/resource-group-using-tags.md) oppure in base al nome della risorsa.  

![Personalizzare il dashboard](./media/azure-portal-dashboards/customize-dashboard.png)

Per aggiungere i riquadri, trascinarli nel punto desiderato della superficie del dashboard.

Per i riquadri non associati a una particolare risorsa è disponibile una nuova categoria denominata **Generale**.  In questo esempio viene aggiunto il riquadro Markdown,  che viene usato per aggiungere contenuto personalizzato al dashboard.  Il riquadro supporta testo normale, la [sintassi markdown](https://daringfireball.net/projects/markdown/syntax) e un set limitato di HTML.  Per motivi di sicurezza, non è possibile eseguire operazioni come inserire tag `<script>` o usare determinati elementi di stile CSS che potrebbero interferire con il portale. 

![Aggiungere Markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Modificare un dashboard
Dopo aver creato il dashboard, è possibile aggiungere riquadri dalla raccolta di riquadri oppure la rappresentazione in forma di riquadro dei pannelli. Verrà ora aggiunta la rappresentazione del gruppo di risorse. È possibile eseguire l'aggiunta cercando l'elemento oppure dal pannello dei gruppi di risorse. Con entrambi gli approcci si ottiene l'aggiunta della rappresentazione in forma di riquadro del gruppo di risorse.

![Aggiungi al dashboard](./media/azure-portal-dashboards/pin-to-dashboard.png)

Al termine dell'aggiunta, l'elemento viene visualizzato nel dashboard.

![Visualizzare il dashboard](./media/azure-portal-dashboards/view-dashboard.png)

Dopo aver aggiunto un riquadro Markdown e un gruppo di risorse al dashboard, è possibile ridimensionare e ridisporre i riquadri in un layout idoneo.

Passando il puntatore e selezionando "…" o facendo clic con il pulsante destro del mouse su un riquadro è possibile visualizzare tutti i relativi comandi contestuali. Per impostazione predefinita, sono disponibili due voci:

1. **Rimuovi dal dashboard** consente di rimuovere il riquadro dal dashboard
2. **Personalizza** consente di passare alla modalità di personalizzazione

![Personalizzare un riquadro](./media/azure-portal-dashboards/customize-tile.png)

Selezionando Personalizza è possibile ridimensionare e riordinare i riquadri. Per ridimensionare un riquadro, selezionare le nuove dimensioni nel menu contestuale come illustrato nell'immagine seguente.

![Ridimensionare il riquadro](./media/azure-portal-dashboards/resize-tile.png)

In alternativa, se il riquadro supporta qualsiasi dimensione è possibile trascinare l'angolo inferiore destro fino a ottenere le dimensioni desiderate.

![Ridimensionare il riquadro](./media/azure-portal-dashboards/resize-corner.png)

Dopo aver ridimensionato i riquadri, visualizzare il dashboard.

![Visualizzare il riquadro](./media/azure-portal-dashboards/view-tile.png)

Al termine della personalizzazione di un dashboard, per uscire dalla modalità di personalizzazione, è sufficiente selezionare **Fine personalizzazione** oppure fare clic con il pulsante destro del mouse e scegliere **Fine personalizzazione** dal menu contestuale.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Pubblicare un dashboard e gestire il controllo di accesso
Quando si crea un dashboard, per impostazione predefinita il dashboard è privato e può quindi essere visualizzato solo dall'utente da cui è stato creato.  Per renderlo visibile ad altri utenti, usare il pulsante **Condividi** visualizzato insieme agli altri comandi relativi ai dashboard.

![Condividere il dashboard](./media/azure-portal-dashboards/share-dashboard.png)

Viene chiesto di scegliere una sottoscrizione e un gruppo di risorse in cui pubblicare il dashboard. Per facilitare l'integrazione dei dashboard nell'ecosistema, i dashboard condivisi sono stati implementati come risorse di Azure. Non è quindi possibile eseguire la condivisione digitando un indirizzo di posta elettronica.  L'accesso alle informazioni visualizzate nella maggior parte dei riquadri del portale è regolato dal [controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md). Dal punto di vista del controllo di accesso, i dashboard condivisi non sono diversi da una macchina virtuale o da un account di archiviazione.  

Si supponga di avere una sottoscrizione di Azure e che ai membri del team siano stati assegnati i ruoli di **proprietario**, **collaboratore** o **lettore** della sottoscrizione.  Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione.  Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli.  Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard condiviso, ma non pubblicarle nel server.  Possono tuttavia creare una copia privata del dashboard per uso personale.  Come sempre, i singoli riquadri del dashboard applicano proprie regole di controllo di accesso in base alle risorse corrispondenti.  

Per praticità, l'esperienza di pubblicazione del portale propone un modello che prevede l'inserimento dei dashboard in un gruppo di risorse denominato **dashboards**(dashboard).  

![Pubblicare il dashboard](./media/azure-portal-dashboards/publish-dashboard.png)

È anche possibile scegliere di pubblicare un dashboard in uno specifico gruppo di risorse.  Il controllo di accesso del dashboard corrisponderà al controllo di accesso del gruppo di risorse.  Gli utenti che possono gestire le risorse nel gruppo di risorse avranno accesso anche ai dashboard.

![Pubblicare il dashboard in un gruppo di risorse](./media/azure-portal-dashboards/publish-to-resource-group.png)

Al termine della pubblicazione del dashboard, il riquadro **Condivisione e controllo di accesso** verrà aggiornato e visualizzerà informazioni sulla pubblicazione del dashboard e un collegamento per gestire l'accesso degli utenti.  Il collegamento consente di aprire il pannello relativo al controllo degli accessi in base al ruolo standard che viene usato per gestire l'accesso per qualsiasi risorsa di Azure.  È possibile tornare a questa visualizzazione in qualsiasi momento selezionando **Condividi**.

![Gestire il controllo di accesso](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Passaggi successivi
* Per gestire le risorse, vedere [Gestire le risorse di Azure mediante il portale](../azure-resource-manager/resource-group-portal.md).
* Per distribuire le risorse, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

