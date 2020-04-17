---
title: Creare e condividere dashboard nel portale di Azure
description: Questo articolo descrive come creare, personalizzare, pubblicare e condividere dashboard nel portale di Azure.This article describes how to create, customize, publish, and share dashboards in the Azure portal.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: e6eda3b522a5b46cd82ef29ae493891a624d3272
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459288"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Creare e condividere dashboard nel portale di Azure

I dashboard sono una visualizzazione mirata e organizzata delle risorse cloud nel portale di Azure.Dashboards are a focused and organized view of your cloud resources in the Azure portal. Usare i dashboard come area di lavoro in cui è possibile avviare rapidamente le attività per le operazioni quotidiane e monitorare le risorse. Creare dashboard personalizzati basati su progetti, attività o ruoli utente, ad esempio.

Il portale di Azure offre un dashboard predefinito come punto di partenza. È possibile modificare il dashboard predefinito. Creare e personalizzare dashboard aggiuntivi e pubblicare e condividere dashboard per renderli disponibili ad altri utenti. In questo articolo viene descritto come creare un nuovo dashboard, personalizzare l'interfaccia e pubblicare e condividere dashboard.

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard

In questo esempio viene creato un nuovo dashboard privato e viene assegnato un nome. Per iniziare, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu del portale di Azure selezionare **Dashboard**. La visualizzazione predefinita potrebbe essere già impostata su dashboard.

    ![Aprire il dashboard](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selezionare **Nuovo dashboard**.

    ![Screenshot del nuovo dashboard](./media/azure-portal-dashboards/create-new-dashboard.png)

    Questa azione apre la **Raccolta riquadri,** da cui verranno selezionati i riquadri, e una griglia vuota in cui si difterrano i riquadri.

    ![Screenshot della raccolta di riquadri e griglia vuota](./media/azure-portal-dashboards/dashboard-name.png)

1. Selezionare il testo **Dashboard** personale nell'etichetta del dashboard e immettere un nome che consenta di identificare facilmente il dashboard personalizzato.

1. Selezionare **Fine personalizzazione** nell'intestazione di pagina per uscire dalla modalità di modifica.

La visualizzazione dashboard ora mostra il nuovo dashboard. Selezionare la freccia accanto al nome del dashboard per visualizzare i dashboard disponibili. L'elenco può includere dashboard creati e condivisi da altri utenti.

## <a name="edit-a-dashboard"></a>Modificare un dashboard

A questo punto, è possibile modificare il dashboard per aggiungere, ridimensionare e disporre i riquadri che rappresentano le risorse di Azure.Now, let's edit the dashboard to add, resize, and arrange tiles that represent your Azure resources.

### <a name="add-tiles-from-the-dashboard"></a>Aggiungere riquadri dal dashboard

Per aggiungere riquadri a un dashboard, attenersi alla seguente procedura:

1. Selezionare ![l'icona](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifica** dall'intestazione della pagina.

    ![Screenshot della modifica dell'evidenziazione del dashboard](./media/azure-portal-dashboards/dashboard-edit.png)

1. Sfogliare la **Raccolta riquadri** o usare il campo di ricerca per trovare il riquadro desiderato.

1. Selezionare **Aggiungi** per aggiungere il riquadro al dashboard con dimensioni e posizione predefinite. In alternativa, trascinare il riquadro nella griglia e posizionarlo nel punto desiderato.

> [!TIP]
> Se si lavora con più di un'organizzazione, aggiungere il riquadro **Identità organizzazione** al dashboard per mostrare chiaramente a quale organizzazione appartengono le risorse.

### <a name="add-tiles-from-a-resource-page"></a>Aggiungere riquadri da una pagina delle risorse

Esiste un modo alternativo per aggiungere riquadri al dashboard. Molte pagine delle risorse includono un'icona a forma di puntina da riga nella barra dei comandi. Se si seleziona l'icona, un riquadro che rappresenta la pagina di origine viene aggiunto al dashboard attualmente attivo. 

![Screenshot della barra dei comandi della pagina con l'icona a forma di puntina](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Ridimensionare o ridisporre i riquadri

Per modificare le dimensioni di un riquadro o per ridisporre i riquadri in un dashboard, attenersi alla seguente procedura:

1. Selezionare ![l'icona](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifica** dall'intestazione della pagina.

1. Selezionare il menu di scelta rapida nell'angolo superiore destro di un riquadro. Quindi, scegliere una dimensione del riquadro. I riquadri che supportano qualsiasi dimensione includono anche una "maniglia" nell'angolo in basso a destra che consente di trascinare il riquadro fino alle dimensioni desiderate.

    ![Screenshot del dashboard con il menu delle dimensioni del riquadro aperto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selezionare un riquadro e trascinarlo in una nuova posizione nella griglia per disporre il dashboard.

### <a name="additional-tile-configuration"></a>Configurazione aggiuntiva del riquadro

Alcuni riquadri potrebbero richiedere una configurazione maggiore per visualizzare le informazioni desiderate. Ad esempio, il riquadro **del grafico Metriche** deve essere impostato per visualizzare una metrica da **Monitoraggio di Azure.** È inoltre possibile personalizzare i dati del riquadro per ignorare le impostazioni temporali predefinite del dashboard.

Qualsiasi riquadro che deve essere impostato visualizza un banner **Configura riquadro** fino a quando non si personalizza il riquadro. Per personalizzare il riquadro:

1. Selezionare **Fine personalizzazione** nell'intestazione di pagina per uscire dalla modalità di modifica.

1. Seleziona il banner, quindi esegui la configurazione richiesta.

    ![Screenshot del riquadro che richiede la configurazione](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Un riquadro markdown consente di visualizzare contenuto statico personalizzato nel dashboard. Questo potrebbe essere istruzioni di base, un'immagine, un insieme di collegamenti ipertestuali, o anche informazioni di contatto. Per altre informazioni sull'uso di un riquadro markdown, vedere [Usare un riquadro markdown nei dashboard](azure-portal-markdown-tile.md)di Azure per visualizzare contenuto personalizzato.

### <a name="customize-tile-data"></a>Personalizzare i dati dei riquadri

I dati nel dashboard mostrano automaticamente l'attività delle ultime 24 ore. Per visualizzare un intervallo di tempo diverso solo per questo riquadro, attenersi alla seguente procedura:

1. Seleziona Personalizza i dati del **riquadro** ](./media/azure-portal-dashboards/dashboard-filter.png) dal menu di scelta rapida o il filtro icona del ![filtro nell'angolo in alto a sinistra del riquadro.

    ![Screenshot del menu di scelta rapida del riquadro](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Selezionare la casella di controllo **Ignora le impostazioni dell'ora del dashboard a livello di riquadro.**

    ![Screenshot della finestra di dialogo per configurare le impostazioni dell'ora del riquadro](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Scegliere l'intervallo di tempo da visualizzare per questo riquadro. È possibile scegliere tra gli ultimi 30 minuti agli ultimi 30 giorni o definire un intervallo personalizzato.

1. Scegliere la granularità temporale da visualizzare. È possibile visualizzare da incrementi di un minuto a un mese.

1. Selezionare **Applica**.

## <a name="delete-a-tile"></a>Eliminare un riquadro

Per rimuovere un riquadro da un dashboard, attenersi alla seguente procedura:

* Selezionare il menu di scelta rapida nell'angolo superiore destro del riquadro, quindi selezionare **Rimuovi dal dashboard**. Oppure

* Selezionare ![l'icona](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifica** per attivare la modalità di personalizzazione. Passare il mouse nell'angolo superiore destro ![del](./media/azure-portal-dashboards/dashboard-delete-icon.png) riquadro, quindi selezionare l'icona di eliminazione per rimuovere il riquadro dal dashboard.

   ![Screenshot che mostra come rimuovere il riquadro dal dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonare un dashboard

Per utilizzare un dashboard esistente come modello per un nuovo dashboard, attenersi alla seguente procedura:

1. Assicurarsi che la visualizzazione dashboard mostri il dashboard che si desidera copiare.

1. Nell'intestazione di ![pagina,](./media/azure-portal-dashboards/dashboard-clone.png) selezionare l'icona **Clone**.

1. Una copia del dashboard, denominata **Clona del** nome del *dashboard,* viene aperta in modalità di modifica. Usare i passaggi precedenti in questo articolo per rinominare e personalizzare il dashboard.

## <a name="publish-and-share-a-dashboard"></a>Pubblicare e condividere un dashboard

Quando crei un dashboard, è privato per impostazione predefinita, il che significa che sei l'unico che può vederlo. Per rendere i dashboard disponibili ad altri utenti, è possibile pubblicarli e condividerli. Per altre informazioni, vedere Condividere dashboard di Azure usando il controllo degli [accessi](azure-portal-dashboard-share-access.md)in base al ruolo.

### <a name="open-a-shared-dashboard"></a>Aprire un dashboard condiviso

Per trovare e aprire un dashboard condiviso, attenersi alla seguente procedura:

1. Selezionare la freccia accanto al nome del dashboard.

1. Selezionare dall'elenco visualizzato dei dashboard. Se il dashboard che si desidera aprire non è presente nell'elenco:

    1. Selezionare **Sfoglia tutti i dashboard**.

        ![Schermata del menu di selezione del dashboard](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Nel campo **Tipo** selezionare **Dashboard condivisi**.

        ![Screenshot del menu di selezione di tutti i dashboard](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selezionare una o più sottoscrizioni. È inoltre possibile immettere testo per filtrare i dashboard in base al nome.

    1. Selezionare un dashboard dall'elenco dei dashboard condivisi.

## <a name="delete-a-dashboard"></a>Eliminare un dashboard

Per eliminare definitivamente un dashboard privato o condiviso, attenersi alla seguente procedura:

1. Selezionare il dashboard da eliminare dall'elenco accanto al nome del dashboard.

1. Selezionare ![l'icona](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Elimina** dall'intestazione di pagina.

1. Per un dashboard privato, selezionare **OK** nella finestra di dialogo di conferma per rimuoverlo. Per un dashboard condiviso, nella finestra di dialogo di conferma selezionare la casella di controllo per confermare che il dashboard pubblicato non sarà più visibile da altri utenti. Selezionare quindi **OK**.

    ![Schermata della conferma di eliminazione](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passaggi successivi

* [Condividere i dashboard di Azure tramite il Controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md)
* [Creare dashboard di Azure a livello di codiceProgrammatically create Azure dashboards](azure-portal-dashboards-create-programmatically.md)
