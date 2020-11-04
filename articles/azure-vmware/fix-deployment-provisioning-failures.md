---
title: Supporto per la distribuzione della soluzione VMware di Azure o il provisioning non riuscito
description: Ottenere informazioni dal cloud privato della soluzione VMware di Azure per archiviare una richiesta di servizio per una distribuzione della soluzione VMware di Azure o un errore di provisioning.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 6d609774b0d3a2de7809d04e4fa0c4e3e6593590
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349121"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Aprire una richiesta di supporto per la distribuzione di una soluzione VMware di Azure o un errore di provisioning

Questo articolo illustra come aprire una richiesta di [supporto](https://rc.portal.azure.com/#create/Microsoft.Support) e fornire informazioni chiave per la distribuzione di una soluzione VMware di Azure o un errore di provisioning. 

Quando si verifica un errore nel cloud privato, è necessario aprire una richiesta di supporto nella portale di Azure. Per aprire una richiesta di supporto, ottenere prima alcune informazioni chiave nella portale di Azure:

- ID correlazione
- ID circuito ExpressRoute di Azure
- Messaggi di errore

## <a name="get-the-correlation-id"></a>Ottenere l'ID di correlazione
 
Quando si crea un cloud privato o una risorsa in Azure, viene generato automaticamente un ID di correlazione per la risorsa. Includere l'ID di correlazione del cloud privato nella richiesta di supporto per aprire e risolvere più rapidamente la richiesta.

Nella portale di Azure è possibile ottenere l'ID di correlazione per una risorsa in due modi:

* Riquadro **Panoramica**
* Log di distribuzione
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Ottenere l'ID di correlazione dalla panoramica delle risorse

Di seguito è riportato un esempio dei dettagli dell'operazione di una distribuzione del cloud privato non riuscita, con l'ID di correlazione selezionato:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Screenshot che mostra una distribuzione del cloud privato non riuscita con l'ID di correlazione selezionato.":::

Per accedere ai risultati della distribuzione in un riquadro di **Panoramica** del cloud privato:

1. Nella portale di Azure selezionare il cloud privato.

1. Nel menu a sinistra selezionare **Overview (panoramica** ).

Dopo l'avvio di una distribuzione, i risultati della distribuzione vengono visualizzati nel riquadro di **Panoramica** del cloud privato.

Copiare e salvare l'ID di correlazione della distribuzione del cloud privato da includere nella richiesta di servizio.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Ottenere l'ID di correlazione dal log di distribuzione

È possibile ottenere l'ID di correlazione per una distribuzione non riuscita eseguendo una ricerca nel log attività di distribuzione nel portale di Azure.

Per accedere al log di distribuzione:

1. Nella portale di Azure selezionare il cloud privato, quindi selezionare l'icona notifiche.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Screenshot che mostra l'icona delle notifiche nella portale di Azure.":::

1. Nel riquadro **notifiche** selezionare **altri eventi nel log attività** :

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Screenshot che Mostra più eventi nel collegamento log attività selezionato nel riquadro notifiche.":::

1. Per individuare la distribuzione non riuscita e l'ID di correlazione, cercare il nome della risorsa o altre informazioni utilizzate per creare la risorsa. 

    Nell'esempio seguente vengono illustrati i risultati della ricerca per una risorsa cloud privata denominata PC03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Screenshot che mostra i risultati della ricerca per una risorsa cloud privata di esempio e il riquadro crea o aggiorna un PrivateCloud.":::
 
1. Nei risultati della ricerca nel riquadro **log attività** selezionare il nome dell'operazione di distribuzione non riuscita.

1. Nel riquadro **Crea o aggiorna un PrivateCloud** selezionare la scheda **JSON** e quindi cercare `correlationId` nel log visualizzato. Copiare il `correlationId` valore per includerlo nella richiesta di supporto. 
 
## <a name="copy-error-messages"></a>Copia messaggi di errore

Per risolvere il problema di distribuzione, includere tutti i messaggi di errore visualizzati nel portale di Azure. Selezionare un messaggio di avviso per visualizzare un riepilogo degli errori:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Screenshot che mostra i dettagli dell'errore nella scheda Riepilogo del riquadro errori, con l'icona copia selezionata.":::

Per copiare il messaggio di errore, selezionare l'icona di copia. Salvare il messaggio copiato da includere nella richiesta di supporto.
 
## <a name="get-the-expressroute-id-uri"></a>Ottenere l'ID ExpressRoute (URI)
 
Forse si sta provando a ridimensionare o eseguire il peering di un cloud privato esistente con il circuito ExpressRoute del cloud privato e l'operazione ha esito negativo. In questo scenario, è necessario l'ID ExpressRoute da includere nella richiesta di supporto.

Per copiare l'ID ExpressRoute:

1. Nella portale di Azure selezionare il cloud privato.
1. Nel menu a sinistra, in **Gestisci** , selezionare **connettività**. 
1. Nel riquadro di destra selezionare la scheda **ExpressRoute** .
1. Selezionare l'icona di copia per **EXPRESSROUTE ID** e salvare il valore da usare nella richiesta di supporto.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Copiare l'ID ExpressRoute negli Appunti."::: 
 
## <a name="pre-validation-failures"></a>Errori di pre-convalida

Se il controllo di pre-convalida del cloud privato non è riuscito (prima della distribuzione), non verrà generato alcun ID di correlazione. In questo scenario è possibile specificare le informazioni seguenti nella richiesta di supporto:

- Messaggi di errore e di errore. Questi messaggi possono essere utili in molti errori, ad esempio per i problemi relativi alla quota. È importante copiare questi messaggi e includerli nella richiesta di supporto, come descritto in questo articolo.
- Informazioni usate per creare il cloud privato della soluzione VMware di Azure, tra cui:
  - Location
  - Resource group
  - Nome risorsa

## <a name="create-your-support-request"></a>Creare la richiesta di supporto

Per informazioni generali sulla creazione di una richiesta di supporto, vedere [come creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Per creare una richiesta di supporto per una distribuzione della soluzione VMware di Azure o un errore di provisioning:

1. Nella portale di Azure selezionare l'icona della **Guida** e quindi selezionare **nuova richiesta di supporto**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Screenshot del riquadro nuova richiesta di supporto nella portale di Azure.":::

1. Immettere o selezionare le informazioni necessarie:

   1. Passare alla scheda **Nozioni di base** :

      1. Per **tipo di problema** , selezionare **problemi di configurazione e configurazione**.

      1. Per **sottotipo di problema** , selezionare **provisioning di un cloud privato**.

   1. Nella scheda **Dettagli** :

      1. Immettere o selezionare le informazioni richieste.

      1. Incollare l'ID di correlazione o l'ID ExpressRoute in cui sono richieste queste informazioni. Se non viene visualizzata una casella di testo specifica per questi valori, incollarli nella casella di testo **specificare i dettagli relativi al problema** .

    1. Incollare tutti i dettagli dell'errore, inclusi i messaggi di errore o di errore copiati, nella casella di testo **specificare i dettagli relativi al problema** .

1. Esaminare le voci e quindi selezionare **Crea** per creare la richiesta di supporto.
