---
title: Creare e condividere dashboard nel portale di Azure
description: Questo articolo descrive come creare, personalizzare, pubblicare e condividere i dashboard nel portale di Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901105"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Creare e condividere dashboard nel portale di Azure

I dashboard sono una visualizzazione mirata e organizzata delle risorse cloud nella portale di Azure. Usare i dashboard come area di lavoro in cui è possibile avviare rapidamente le attività per le operazioni quotidiane e monitorare le risorse. Creare dashboard personalizzati basati su progetti, attività o ruoli utente, ad esempio.

Il portale di Azure fornisce un dashboard predefinito come punto di partenza. È possibile modificare il dashboard predefinito. Creazione e personalizzazione di dashboard aggiuntivi e pubblicazione e condivisione di dashboard per renderli disponibili ad altri utenti. Questo articolo descrive come creare un nuovo dashboard, personalizzare l'interfaccia e pubblicare e condividere i dashboard.

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard

In questo esempio si crea un nuovo dashboard privato e si assegna un nome. Per iniziare, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Scegliere **Dashboard**dal menu portale di Azure. La visualizzazione predefinita potrebbe essere già impostata sul dashboard.

    ![Aprire il dashboard](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selezionare **nuovo dashboard**.

    ![Screenshot del dashboard predefinito](./media/azure-portal-dashboards/create-new-dashboard.png)

    Questa azione consente di aprire la **raccolta di sezioni**, da cui è possibile selezionare i riquadri e una griglia vuota in cui verranno organizzati i riquadri.

    ![Screenshot della raccolta dei riquadri e della griglia vuota](./media/azure-portal-dashboards/dashboard-name.png)

1. Selezionare il testo del **Dashboard** nell'etichetta del dashboard e immettere un nome che consentirà di identificare facilmente il dashboard personalizzato.

1. Selezionare **fine personalizzazione** nell'intestazione di pagina per uscire dalla modalità di modifica.

La visualizzazione Dashboard Mostra ora il dashboard vuoto. Selezionare la freccia accanto al nome del dashboard per visualizzare i dashboard disponibili. L'elenco potrebbe includere i dashboard creati e condivisi da altri utenti.

## <a name="edit-a-dashboard"></a>Modificare un dashboard

A questo punto, modificare il dashboard per aggiungere, ridimensionare e disporre i riquadri che rappresentano le risorse di Azure.

### <a name="add-tiles"></a>Aggiunta di riquadri

Per aggiungere riquadri a un dashboard, attenersi alla procedura seguente:

1. Selezionare ![icona modifica](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modifica** dall'intestazione di pagina.

    ![Screenshot dell'evidenziazione della modifica del dashboard](./media/azure-portal-dashboards/dashboard-edit.png)

1. Esplorare la **raccolta di riquadri** o usare il campo di ricerca per trovare il riquadro desiderato.

1. Selezionare **Aggiungi** per aggiungere il riquadro al dashboard con le dimensioni e il percorso predefiniti. In alternativa, trascinare il riquadro nella griglia e posizionarlo nel percorso desiderato.

Molte pagine delle risorse includono un'icona a puntina da disegno sulla barra dei comandi. Se si seleziona l'icona, un riquadro che rappresenta la pagina di origine viene aggiunto al dashboard attualmente attivo. Questo metodo rappresenta un modo alternativo per aggiungere riquadri al dashboard.

![Screenshot della barra dei comandi della pagina con icona del PIN](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Se si lavora con più di un'organizzazione, aggiungere il riquadro **identità organizzazione** al dashboard per visualizzare chiaramente l'organizzazione a cui appartengono le risorse.
>
>

### <a name="resize-or-rearrange-tiles"></a>Ridimensionare o ridisporre i riquadri

Per modificare le dimensioni di un riquadro o per ridisporre i riquadri in un dashboard, attenersi alla procedura seguente:

1. Selezionare ![icona modifica](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modifica** dall'intestazione di pagina.

1. Selezionare il menu di scelta rapida nell'angolo superiore destro di un riquadro. Quindi, scegliere le dimensioni del riquadro. I riquadri che supportano qualsiasi dimensione includono anche un "handle" nell'angolo in basso a destra che consente di trascinare il riquadro fino alle dimensioni desiderate.

    ![Screenshot del dashboard con il menu delle dimensioni del riquadro aperto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selezionare un riquadro e trascinarlo in una nuova posizione sulla griglia per disporre il dashboard.

### <a name="additional-tile-configuration"></a>Configurazione aggiuntiva del riquadro

Per alcuni riquadri potrebbe essere necessaria una configurazione maggiore per visualizzare le informazioni desiderate. Ad esempio, è necessario configurare il riquadro **grafico metriche** per visualizzare una metrica da **monitoraggio di Azure**. È anche possibile personalizzare i dati del riquadro per sostituire le impostazioni di ora predefinite del dashboard.

Tutti i riquadri che devono essere impostati visualizzano un banner di **configurazione del riquadro** fino a quando non si Personalizza il riquadro. Selezionare il banner, quindi eseguire la configurazione richiesta.

![Screenshot del riquadro che richiede la configurazione](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Un riquadro Markdown consente di visualizzare contenuto statico personalizzato nel dashboard. Potrebbe trattarsi di istruzioni di base, un'immagine, un set di collegamenti ipertestuali o persino informazioni di contatto. Per altre informazioni sull'uso di un riquadro Markdown, vedere [usare un riquadro Markdown nei dashboard di Azure per visualizzare il contenuto personalizzato](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personalizzare i dati del riquadro

I dati nel dashboard mostrano automaticamente le attività per le ultime 24 ore. Per visualizzare un intervallo di tempo diverso solo per questo riquadro, attenersi alla seguente procedura:

1. Selezionare **personalizzare i dati del riquadro** dal menu di scelta rapida o l'icona del filtro ![](./media/azure-portal-dashboards/dashboard-filter.png) filtro dall'angolo superiore sinistro del riquadro.

    ![Screenshot del menu di scelta rapida del riquadro](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Selezionare la casella di controllo per **sostituire le impostazioni dell'ora del dashboard a livello di riquadro**.

    ![Screenshot della finestra di dialogo per configurare le impostazioni temporali dei riquadri](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Consente di scegliere l'intervallo di tempo da visualizzare per questo riquadro. È possibile scegliere tra 30 minuti negli ultimi 30 giorni o definire un intervallo personalizzato.

1. Scegliere la granularità temporale da visualizzare. È possibile visualizzare gli incrementi di un minuto da un mese all'altro.

1. Selezionare **Applica**.

## <a name="delete-a-tile"></a>Eliminare un riquadro

Per rimuovere un riquadro da un dashboard, attenersi alla procedura seguente:

* Selezionare il menu di scelta rapida nell'angolo superiore destro del riquadro, quindi selezionare **Rimuovi dal dashboard**. Oppure

* Selezionare ![icona modifica](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modifica** per attivare la modalità di personalizzazione. Passare il puntatore del mouse nell'angolo superiore destro del riquadro, quindi selezionare l'icona Elimina ![](./media/azure-portal-dashboards/dashboard-delete-icon.png) icona Elimina per rimuovere il riquadro dal dashboard.

   ![Screenshot che illustra come rimuovere il riquadro dal dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonare un dashboard

Per usare un dashboard esistente come modello per un nuovo dashboard, seguire questa procedura:

1. Assicurarsi che la visualizzazione Dashboard mostri il dashboard che si vuole copiare.

1. Nell'intestazione di pagina selezionare ![icona clona](./media/azure-portal-dashboards/dashboard-clone.png) **clona**.

1. Una copia del dashboard denominata **clone del nome del** *Dashboard* viene aperta in modalità di modifica. Usare i passaggi precedenti di questo articolo per rinominare e personalizzare il dashboard.

## <a name="publish-and-share-a-dashboard"></a>Pubblicare e condividere un dashboard

Quando si crea un dashboard, è privato per impostazione predefinita, il che significa che l'unico utente può visualizzarlo. Per rendere disponibili i dashboard ad altri utenti, è possibile condividerli con altri utenti. Prima di tutto, è necessario pubblicare il dashboard come risorsa di Azure. Per pubblicare e condividere un dashboard personalizzato, attenersi alla procedura seguente:

1. Selezionare ![icona Condividi](./media/azure-portal-dashboards/dashboard-share-icon.png) **Condividi** dall'intestazione di pagina. Viene aperto **condivisione e controllo di accesso** .

1. Verificare che sia visualizzato il nome corretto del dashboard.

1. Selezionare un **nome di sottoscrizione**. Gli utenti con accesso alla sottoscrizione possono usare il dashboard condiviso. L'accesso alle risorse rappresentate dai singoli riquadri è determinato dal controllo degli accessi in base al ruolo di Azure.

1. Selezionare la casella di controllo per pubblicare il dashboard nel gruppo di risorse "Dashboard" per la sottoscrizione selezionata. In alternativa, deselezionare la casella di controllo e scegliere di pubblicare in un gruppo di risorse esistente.

1. Scegliere un percorso per la risorsa dashboard. Si consiglia di individuare il dashboard con altre risorse. Se si sceglie da gruppi di risorse esistenti, il dashboard viene automaticamente individuato con tale gruppo di risorse.

1. Selezionare **Pubblica**.

    ![Screenshot della finestra di dialogo di pubblicazione del dashboard](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Impostare il controllo di accesso in un dashboard condiviso

Dopo la pubblicazione del dashboard, gestire chi ha accesso al dashboard attenendosi alla procedura seguente:

1. Nel riquadro **condivisione e controllo di accesso** selezionare **Gestisci utenti**.

    ![Screenshot della finestra di dialogo condivisione dashboard e controllo di accesso](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. Verrà visualizzata la pagina **controllo di accesso** . È possibile esaminare il livello di accesso per un utente o aggiungere una nuova assegnazione di ruolo. Quando si aggiunge un'assegnazione di ruolo, si stanno concedendo autorizzazioni al dashboard.

> [!NOTE]
> I riquadri sono viste rappresentative delle risorse dell'organizzazione. L'accesso alle risorse viene gestito tramite l'assegnazione di controllo degli accessi in base al ruolo e le autorizzazioni vengono ereditate dalla sottoscrizione fino alla risorsa. Fornire l'accesso a un dashboard non assegna automaticamente le autorizzazioni per le risorse visualizzate nel dashboard. Per altre informazioni sulle autorizzazioni per i dashboard condivisi e il controllo degli accessi in base al ruolo per le risorse, vedere [condividere i dashboard di Azure tramite il controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Aprire un dashboard condiviso

Per trovare e aprire un dashboard condiviso, seguire questa procedura:

1. Selezionare la freccia accanto al nome del dashboard.

1. Selezionare dall'elenco di dashboard visualizzato o **sfogliare tutti i dashboard** se il dashboard che si vuole aprire non è elencato.

    ![Screenshot del menu di selezione del dashboard](./media/azure-portal-dashboards/dashboard-browse.png)

1. Nel campo **tipo** selezionare **Dashboard condivisi**.

1. Selezionare una o più sottoscrizioni. È anche possibile immettere il testo per filtrare i dashboard in base al nome.

1. Selezionare un dashboard dall'elenco dei dashboard condivisi.

## <a name="delete-a-dashboard"></a>Eliminare un dashboard

Per eliminare definitivamente un dashboard privato o condiviso, attenersi alla procedura seguente:

1. Selezionare il dashboard che si vuole eliminare dall'elenco accanto al nome del dashboard.

1. Selezionare ![icona Elimina](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Elimina** dall'intestazione di pagina.

1. Per un dashboard privato, fare clic su **OK** nella finestra di dialogo di conferma per rimuovere il dashboard. Per un dashboard condiviso, nella finestra di dialogo di conferma selezionare la casella di controllo per confermare che il dashboard pubblicato non sarà più visualizzabile da altri. Quindi selezionare **OK**.

    ![Screenshot della conferma dell'eliminazione](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passaggi successivi

* [Condividere i dashboard di Azure tramite il controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md)
* [Creare dashboard di Azure a livello di codice](azure-portal-dashboards-create-programmatically.md)
