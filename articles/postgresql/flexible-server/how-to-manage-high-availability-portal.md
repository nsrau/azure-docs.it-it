---
title: Gestire la disponibilità elevata con ridondanza della zona-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come abilitare o disabilitare la disponibilità elevata con ridondanza della zona nel database di Azure per PostgreSQL-server flessibile tramite il portale di Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937003"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Gestire la disponibilità elevata con ridondanza della zona in server flessibili

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo descrive come abilitare o disabilitare la configurazione della disponibilità elevata con ridondanza della zona nel server flessibile.

La funzionalità di disponibilità elevata effettua il provisioning della replica primaria e di standby separate fisicamente in zone diverse. Per ulteriori informazioni, vedere la [documentazione relativa ai concetti relativi alla disponibilità elevata](./concepts-high-availability.md). È possibile scegliere di abilitare la disponibilità elevata al momento della creazione flessibile del server o dopo la creazione. In questa pagina vengono fornite le linee guida su come abilitare o disabilitare la disponibilità elevata. Questa operazione non modifica le altre impostazioni, tra cui la configurazione VNET, le impostazioni del firewall e la conservazione dei backup. Analogamente, l'abilitazione e la disabilitazione della disponibilità elevata è un'operazione online e non influisca sulla connettività e sulle operazioni dell'applicazione.

## <a name="pre-requisites"></a>Prerequisiti

La disponibilità elevata con ridondanza della zona è disponibile solo nelle aree in cui sono supportate più zone. 

## <a name="enable-high-availability-during-server-creation"></a>Abilitare la disponibilità elevata durante la creazione del server

In questa sezione vengono fornite informazioni dettagliate specifiche per i campi correlati a disponibilità elevata. È possibile seguire questa procedura per distribuire la disponibilità elevata durante la creazione del server flessibile.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere flessibile server e fare clic su Crea.  Per informazioni dettagliate su come compilare i dettagli, ad esempio **sottoscrizione**, **gruppo di risorse**, **nome del server**, **area**e altri campi, vedere la documentazione relativa alle procedure per la creazione del server.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Visualizza sottoscrizione e area":::

2.  Scegliere la **zona di disponibilità**. Questa opzione è utile se si desidera collocare l'applicazione nella stessa zona di disponibilità del database per ridurre la latenza. Scegliere **Nessuna preferenza** se si desidera che il server flessibile venga distribuito in qualsiasi zona di disponibilità.
    ![Selezione della ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="Visualizza sottoscrizione e area"::: AZ selection  

3.  Fare clic sulla casella di controllo per la **disponibilità elevata con ridondanza della zona** nell'opzione disponibilità.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Visualizza sottoscrizione e area":::

4.  Se si desidera modificare il calcolo e l'archiviazione predefiniti, fare clic su  **Configura server**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="Visualizza sottoscrizione e area":::  

5.  Se è selezionata l'opzione disponibilità elevata, il livello di espansione non sarà disponibile per la scelta. È possibile scegliere i livelli di calcolo per **utilizzo generico** o con ottimizzazione per la **memoria** . Sarà quindi possibile selezionare le **dimensioni di calcolo** scelte dall'elenco a discesa.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Visualizza sottoscrizione e area":::  


6.  Selezionare **dimensioni di archiviazione** in Gib usando la barra di scorrimento e selezionare il **periodo di conservazione dei backup** tra 7 giorni e 35 giorni.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Visualizza sottoscrizione e area"::: 

7. Fare clic su **Save**. 

## <a name="enable-high-availability-post-server-creation"></a>Abilitare la creazione del server Post a disponibilità elevata

Seguire questa procedura per abilitare la disponibilità elevata per il server flessibile esistente.

1.  Nella [portale di Azure](https://portal.azure.com/)selezionare il server PostgreSQL flessibile esistente.

2.  Nella pagina server flessibile fare clic su **disponibilità elevata** nel pannello sinistro per aprire la pagina disponibilità elevata.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Visualizza sottoscrizione e area"::: 

3.  Fare clic sulla casella di controllo **disponibilità elevata con ridondanza della zona** per **abilitare** l'opzione e fare clic su **Salva**   per salvare la modifica.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Visualizza sottoscrizione e area"::: 

4.  Viene visualizzata una finestra di dialogo di conferma che indica che, abilitando la disponibilità elevata, il costo aumenterà a causa della distribuzione di server e archiviazione aggiuntiva.

5.  Fare clic su **Abilita** pulsante a disponibilità elevata per abilitare la disponibilità elevata.

6.  Verrà visualizzata una notifica che informa che è in corso la distribuzione a disponibilità elevata.

## <a name="disable-high-availability"></a>Disabilitare la disponibilità elevata

Seguire questi passaggi per disabilitare la disponibilità elevata per il server flessibile già configurato con la ridondanza della zona.

1.  Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per PostgreSQL-server flessibile.

2.  Nella pagina server flessibile fare clic su **disponibilità elevata** nel pannello anteriore per aprire la pagina disponibilità elevata.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Visualizza sottoscrizione e area"::: 

3.  Fare clic sulla casella di controllo **disponibilità elevata con ridondanza della zona** per **disabilitare** l'opzione. Quindi fare clic su **Salva**   per salvare la modifica.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Visualizza sottoscrizione e area"::: 

4.  Verrà visualizzata una finestra di dialogo di conferma in cui è possibile confermare la disabilitazione della disponibilità elevata.

5.  Fare clic sul pulsante **Disabilita** disponibilità elevata per disabilitare la disponibilità elevata.

6.  Verrà visualizzata una notifica che indica che la distribuzione a disponibilità elevata è in corso.

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
