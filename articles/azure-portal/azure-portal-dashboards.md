---
title: Creare e condividere i dashboard del portale di Azure | Microsoft Docs
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
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537228"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Creare e condividere dashboard nel portale di Azure

I dashboard offrono un modo per creare una visualizzazione con lo stato attivo e organizzata nel portale di Azure delle risorse cloud. Usare i dashboard come area di lavoro in cui è possibile rapidamente l'avvio di attività per le operazioni quotidiane e monitorare le risorse.  Creare dashboard personalizzati basati su progetti, attività o i ruoli utente, ad esempio.  Il portale di Azure offre un dashboard predefinito come punto di partenza. È possibile modificare il dashboard predefinito, creare e personalizzare i dashboard aggiuntivi e pubblicare e condividere i dashboard per renderli disponibili ad altri utenti. Questo articolo descrive come creare un nuovo dashboard, personalizzare l'interfaccia e pubblicare e condividere i dashboard.

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard

In questo esempio verrà creato un nuovo dashboard privato e assegnare un nome. Seguire questi passaggi per iniziare:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Dashboard** nella parte superiore della barra laterale sinistra. La visualizzazione predefinita potrebbe già essere impostata al dashboard.
1. Selezionare **+ nuovo dashboard**.

    ![Screenshot del dashboard predefinito](./media/azure-portal-dashboards/dashboard-new.png)

4. Questa azione apre la **raccolta riquadri**, da cui si selezionano i riquadri e una griglia vuota in cui si verranno disporre i riquadri.

    ![Schermata di griglia vuota e raccolta dei riquadri](./media/azure-portal-dashboards/dashboard-name.png)

5. Selezionare il **My Dashboard** testo nel dashboard di etichetta e immettere un nome che consenta di identificare facilmente i dashboard personalizzati.
1. Selezionare **fine personalizzazione** nell'intestazione di pagina per uscire dalla modalità di modifica.

La visualizzazione dashboard Mostra ora il dashboard vuoto. Selezionare l'elenco a discesa accanto al nome del dashboard per visualizzare i dashboard disponibili all'utente: l'elenco potrebbe includere dashboard ad altri utenti hanno creato e condiviso.

## <a name="edit-a-dashboard"></a>Modificare un dashboard

A questo punto, è possibile modificare il dashboard per aggiungere, ridimensionare e disporre i riquadri che rappresentano le risorse di Azure.

### <a name="add-tiles"></a>Aggiunta di riquadri

Per aggiungere riquadri a un dashboard, seguire questa procedura:
1. Selezionare ![icona Modifica](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modificare** dall'intestazione di pagina.

    ![Screenshot del dashboard l'evidenziazione della modifica](./media/azure-portal-dashboards/dashboard-edit.png)

2. Sfoglia il **raccolta riquadri** oppure utilizzare il campo di ricerca per individuare il riquadro da.
1. Selezionare **Add** per aggiungere automaticamente il riquadro al dashboard con una dimensione predefinita e il percorso. In alternativa, trascinare il riquadro nella griglia e lo posiziona in cui si desidera.

Numero di pagine di risorse (noto anche come "pannelli") comprendono un'icona della puntina da disegno nella barra dei comandi. Se si seleziona l'icona, un riquadro che rappresenta la pagina di origine è aggiunto al dashboard attualmente attivo. Questo metodo è un modo alternativo per aggiungere riquadri al dashboard.

![Screenshot della barra dei comandi di pagina con l'icona della puntina](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Se si lavora con più di un'organizzazione, aggiungere il **identità dell'organizzazione** riquadro al dashboard per visualizzare chiaramente quale organizzazione le risorse appartengono a.
>
>
### <a name="resize-or-rearrange-tiles"></a>Ridimensionare o ridisporre i riquadri
Per modificare le dimensioni di un riquadro o ridisporre i riquadri in un dashboard, seguire questi passaggi:

1. Selezionare ![icona Modifica](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modificare** dall'intestazione di pagina.
1. Selezionare il menu di scelta rapida nell'angolo superiore destro di un riquadro. Quindi, scegliere una dimensione della sezione. I riquadri che supportano qualsiasi dimensione includono anche un "handle" nell'angolo inferiore destro che consente di trascinare il riquadro per le dimensioni desiderate.

   ![Screenshot del dashboard con menu di scelta delle dimensioni riquadro aperto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Selezionare un riquadro e trascinarlo in una nuova posizione della griglia per organizzare il dashboard.

### <a name="additional-tile-configuration"></a>Configurazione di riquadro aggiuntive

Alcuni riquadri potrebbe essere necessario ulteriore configurazione per visualizzare le informazioni desiderate. Ad esempio, il **grafico con le metriche** riquadro deve essere configurato per la visualizzazione di una metrica dal **monitoraggio di Azure**. È anche possibile personalizzare i dati del riquadro per eseguire l'override delle impostazioni di tempo predefiniti del dashboard.

Qualsiasi riquadro che deve essere configurato consente di visualizzare una **riquadro Configura** banner fino a quando non si personalizza il riquadro. Selezionare il banner, quindi eseguire la configurazione necessaria.

![Screenshot del riquadro che richiede la configurazione](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Un riquadro markdown consente di visualizzare il contenuto statico, personalizzato nel dashboard. Ciò potrebbe essere istruzioni di base, un'immagine, un set di collegamenti ipertestuali o anche le informazioni di contatto. Per altre informazioni sull'uso di un riquadro markdown, vedere [utilizzare un riquadro markdown personalizzata](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personalizza dati riquadro

I dati nel dashboard mostrano automaticamente l'attività per le ultime 24 ore. Per visualizzare un intervallo di tempo diverso per solo questo riquadro, seguire questa procedura:

1. Selezionare il ![icona di filtro](./media/azure-portal-dashboards/dashboard-filter.png) icona del filtro nell'angolo in alto a sinistra del riquadro o select **i dati del riquadro Personalizza** dal menu di scelta rapida.

   ![Screenshot del menu di scelta rapida riquadro](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Selezionare la casella di controllo **sostituiscono le impostazioni di ora dashboard livello del riquadro**.

   ![Screenshot della finestra di dialogo per configurare le impostazioni ora del riquadro](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Scegliere l'intervallo di tempo da visualizzare per questo riquadro. È possibile scegliere tra i 30 minuti precedenti per gli ultimi 30 giorni o definire un intervallo personalizzato.
1. Scegliere la granularità temporale da visualizzare. È possibile visualizzare in qualsiasi punto da incrementi di un minuto per un mese.
1. Selezionare **Applica**.

## <a name="delete-a-tile"></a>Eliminare un riquadro

Per rimuovere un riquadro da un dashboard, seguire questa procedura:

* Selezionare il menu di scelta rapida in alto a destra del riquadro, quindi selezionare **Rimuovi dal dashboard**. Oppure
* Selezionare ![icona Modifica](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modificare** per attivare la modalità di personalizzazione. Passare il puntatore in alto a destra del riquadro, quindi selezionare il ![icona di eliminazione](./media/azure-portal-dashboards/dashboard-delete-icon.png) delete sull'icona per la rimozione del riquadro dal dashboard.

   ![Screenshot che mostra come rimuovere un riquadro dal dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonare un dashboard

Per usare un dashboard esistente come modello per un nuovo dashboard, seguire questa procedura:

1. Assicurarsi che la visualizzazione dashboard viene visualizzato il dashboard che si desidera copiare.
1. Nell'intestazione di pagina, selezionare ![icona di clonazione](./media/azure-portal-dashboards/dashboard-clone.png) **Clone**.
1. Una copia del dashboard, denominata "di clonare *il nome del dashboard*" viene aperto in modalità di modifica. Usare la procedura precedente in questo articolo per rinominare e personalizzare il dashboard.

## <a name="publish-and-share-a-dashboard"></a>Pubblicare e condividere un dashboard

Quando si crea un dashboard, è privata per impostazione predefinita, questo significa che si è l'unico utente può visualizzarlo. Per rendere disponibile ad altri dashboard, è possibile condividerli con altri utenti. In primo luogo, è necessario pubblicare il dashboard come risorsa di Azure. Per pubblicare e condividere un dashboard personalizzato, seguire questa procedura:

1. Selezionare ![icona Condividi](./media/azure-portal-dashboards/dashboard-share-icon.png) **condividere** dall'intestazione di pagina. Il **condivisione e controllo di accesso** viene visualizzato il form.
1. Verificare che venga visualizzato il nome corretto del dashboard.
1. Selezionare una **nome della sottoscrizione**. Gli utenti con accesso alla sottoscrizione possono usare il dashboard condiviso. Accesso alle risorse rappresentate da singoli riquadri è determinato dal controllo degli accessi in base al ruolo di Azure.
1. Selezionare la casella di controllo per pubblicare il dashboard per il gruppo di risorse 'dashboard' per la sottoscrizione selezionata. In alternativa, deselezionare la casella di controllo e scegliere per la pubblicazione in un gruppo di risorse invece.
1. Scegliere un percorso per la risorsa del dashboard. È consigliabile che avere individuato il dashboard con altre risorse. Nota: se si sceglie di gruppi di risorse esistenti, il dashboard è automaticamente disponibile con tale gruppo di risorse.
1. Selezionare **Pubblica**.

   ![Screenshot della finestra di dialogo di pubblicazione del dashboard](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Configurare il controllo di accesso in un dashboard condiviso

Dopo aver pubblicato il dashboard, gestire chi ha accesso al dashboard, seguire questi passaggi:

1. Nel **condivisione e controllo di accesso** riquadro, selezionare **Gestisci utenti**.

   ![Finestra di dialogo di controllo schermata di accesso + condivisione del dashboard](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. Il **controllo di accesso** verrà visualizzata la pagina. In questa pagina, è possibile controllare il livello di accesso per un utente o aggiungere una nuova assegnazione di ruolo. Quando si aggiunge un'assegnazione di ruolo in questo caso, si concedono le autorizzazioni per il dashboard.

> [!NOTE]
> I riquadri sono viste rappresentative delle risorse all'interno dell'organizzazione. Accesso alle risorse viene gestito tramite l'assegnazione di controllo di accesso basato sui ruoli e le autorizzazioni vengono ereditate dalla sottoscrizione fino alla risorsa. Concedere l'accesso a un dashboard non assegnate automaticamente le autorizzazioni per le risorse visualizzate nel dashboard. Per altre informazioni sulle autorizzazioni per i dashboard condivisi e controllo degli accessi in base al ruolo per le risorse, vedere [condividere i dashboard con controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Aprire un dashboard condiviso

Per trovare e aprire un dashboard condiviso, seguire questa procedura:

1. Selezionare l'elenco a discesa accanto al nome del dashboard.
1. Selezionare nell'elenco visualizzato di dashboard o **esplorare tutti i dashboard** se non è elencato il dashboard da aprire.

   ![Screenshot del menu di selezione del dashboard](./media/azure-portal-dashboards/dashboard-browse.png)

3. Nel **tipo** campi, selezionare **dashboard condivisi**.
1. Selezionare una o più sottoscrizioni. È anche possibile immettere testo per filtrare i dashboard in base al nome.
1. Selezionare un dashboard dall'elenco dei dashboard condivisi.

## <a name="delete-a-dashboard"></a>Eliminare un dashboard

Per eliminare definitivamente un dashboard privato o condiviso, seguire questa procedura:

1. Selezionare il dashboard a cui che si vuole eliminare dall'elenco a discesa accanto al nome del dashboard.
1. Selezionare ![icona di eliminazione](./media/azure-portal-dashboards/dashboard-delete-icon.png) **eliminare** dall'intestazione di pagina.
1. Per un dashboard privato, selezionare **OK** nella finestra di dialogo di conferma per rimuovere il dashboard. Per un dashboard condiviso, nella finestra di conferma, selezionare la casella di controllo per confermare che il dashboard pubblicato non potrà più visualizzabile da altri utenti. Quindi selezionare **OK**.

   ![Schermata di conferma delete](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passaggi successivi

* [Condividere i dashboard con controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md)
* [Creare a livello di programmazione i dashboard di Azure](azure-portal-dashboards-create-programmatically.md)
