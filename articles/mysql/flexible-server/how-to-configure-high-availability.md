---
title: Gestire la disponibilità elevata con ridondanza della zona-portale di Azure-server flessibile per database di Azure per MySQL
description: Questo articolo descrive come abilitare o disabilitare la disponibilità elevata con ridondanza della zona nel database di Azure per il server flessibile MySQL tramite il portale di Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 09cd7428519cbf84c785efa16b61b9507a3c0b94
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940506"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Gestire la disponibilità elevata con ridondanza della zona nel server flessibile database di Azure per MySQL (anteprima)

Questo articolo descrive come abilitare o disabilitare la configurazione della disponibilità elevata con ridondanza della zona nel server flessibile.

La funzionalità di disponibilità elevata effettua il provisioning della replica primaria e di standby separate fisicamente in zone diverse. Per ulteriori informazioni, vedere la [documentazione relativa ai concetti relativi alla disponibilità elevata](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> È possibile abilitare la disponibilità elevata con ridondanza della zona solo durante la creazione di server flessibili.

In questa pagina vengono fornite le linee guida su come abilitare o disabilitare la disponibilità elevata. Questa operazione non modifica le altre impostazioni, tra cui la configurazione VNET, le impostazioni del firewall e la conservazione dei backup. Analogamente, la disabilitazione della disponibilità elevata è un'operazione online e non influisca sulla connettività e sulle operazioni dell'applicazione.

> [!IMPORTANT]
> La disponibilità elevata con ridondanza della zona è disponibile in un set limitato di aree: Asia sudorientale, Westus 2, Europa occidentale e Stati Uniti orientali.  

## <a name="enable-high-availability-during-server-creation"></a>Abilitare la disponibilità elevata durante la creazione del server

In questa sezione vengono fornite informazioni dettagliate specifiche per i campi correlati a disponibilità elevata. È possibile seguire questa procedura per distribuire la disponibilità elevata durante la creazione del server flessibile.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere flessibile server e fare clic su **Crea**.  Per informazioni dettagliate su come compilare i dettagli, ad esempio **sottoscrizione**, **gruppo di risorse**, **nome del server**, **area**e altri campi, vedere la documentazione relativa alle procedure per la creazione del server.

2.  Fare clic sulla casella di controllo per la **disponibilità elevata con ridondanza della zona** nell'opzione disponibilità.

3.  Se si desidera modificare il calcolo e l'archiviazione predefiniti, fare clic su  **Configura server**.

4.  Se è selezionata l'opzione disponibilità elevata, il livello di espansione non sarà disponibile per la scelta. È possibile scegliere i livelli di calcolo per **utilizzo generico** o con ottimizzazione per la **memoria** .

    > [!IMPORTANT]
    > È supportata solo la disponibilità elevata con ridondanza della zona per il piano tariffario ***utilizzo generico*** e con ottimizzazione per la ***memoria*** .

5.  Selezionare le **dimensioni di calcolo** preferite nell'elenco a discesa.

6.  Selezionare **dimensioni di archiviazione** in Gib usando la barra di scorrimento e selezionare il **periodo di conservazione dei backup** tra 7 giorni e 35 giorni.   

## <a name="disable-high-availability"></a>Disabilitare la disponibilità elevata

Seguire questi passaggi per disabilitare la disponibilità elevata per il server flessibile già configurato con la ridondanza della zona.

1.  Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per MySQL server flessibile.

2.  Nella pagina server flessibile fare clic su **disponibilità elevata** nel pannello anteriore per aprire la pagina disponibilità elevata.

3.  Fare clic sulla casella di controllo **disponibilità elevata con ridondanza della zona** per disabilitare l'opzione e fare clic su **Salva**   per salvare la modifica.

4.  Verrà visualizzata una finestra di dialogo di conferma in cui è possibile confermare la disabilitazione della disponibilità elevata.

5.  Fare clic sul pulsante **Disabilita** disponibilità elevata per disabilitare la disponibilità elevata.

6.  Verrà visualizzata una notifica che indica che la distribuzione a disponibilità elevata è in corso.

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
