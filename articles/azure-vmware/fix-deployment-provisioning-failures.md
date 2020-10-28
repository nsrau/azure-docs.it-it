---
title: Ottenere assistenza per la distribuzione di soluzioni VMware di Azure o gli errori di provisioning
description: Come ottenere le informazioni necessarie dal cloud privato della soluzione VMware di Azure per archiviare una richiesta di servizio per la distribuzione della soluzione VMware di Azure o gli errori di provisioning.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779493"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Ottenere assistenza per la distribuzione di soluzioni VMware di Azure o gli errori di provisioning

Questo articolo illustra gli errori di provisioning e distribuzione della soluzione VMware di Azure. Quando si verificano errori nel cloud privato, è necessario aprire una richiesta di [supporto](https://rc.portal.azure.com/#create/Microsoft.Support) (SR) nell'portale di Azure. 

Prima di tutto, tuttavia, è necessario raccogliere alcune informazioni chiave nella portale di Azure:

- ID correlazione
- ID circuito ExpressRoute

## <a name="collect-the-correlation-id"></a>Raccogli l'ID di correlazione
 
Un ID di correlazione viene generato quando si crea un cloud privato o una risorsa in Azure. Ogni distribuzione di Azure Resource Manager genera anche un ID correlazione. Questo ID consente la creazione e la risoluzione di SR più veloci. 
 
Di seguito è riportato un esempio dell'output di una distribuzione di cloud privato non riuscita, con l'ID di correlazione evidenziato.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::

Copiare e salvare l'ID correlazione da includere nella richiesta di servizio. Per informazioni dettagliate, vedere [creare la richiesta di supporto](#create-your-support-request) alla fine di questo articolo.

Se l'errore si verifica nelle fasi di pre-convalida, non viene generato alcun ID di correlazione. In questo caso, è possibile fornire le informazioni usate durante la creazione del cloud privato della soluzione VMware di Azure, tra cui:

- Location
- Resource group
- Nome risorsa
 
### <a name="collect-a-summary-of-errors"></a>Raccogli un riepilogo degli errori

I dettagli degli errori possono essere utili anche per risolvere il problema. Dalla schermata precedente, selezionare il messaggio di avviso per visualizzare un riepilogo degli errori.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::

Anche in questo caso, copiare e salvare il riepilogo da includere in SR.
 
### <a name="retrieve-past-deployments"></a>Recuperare le distribuzioni precedenti

È possibile recuperare le distribuzioni precedenti, incluse quelle non riuscite, eseguendo una ricerca nel log attività di distribuzione a cui si accede selezionando l'icona notifiche.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::

In notifiche selezionare **altri eventi nel log attività** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::

Cercare quindi il nome della risorsa o altre informazioni utilizzate per creare la risorsa per individuare la distribuzione non riuscita e l'ID di correlazione. Nell'esempio seguente vengono illustrati i risultati della ricerca in una risorsa cloud privata (PC03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::
 
Se si seleziona il nome dell'operazione di distribuzione non riuscita, viene visualizzata una finestra con i dettagli. Selezionare la scheda JSON e cercare correlationId. Copiare e includere in SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Raccogli l'ID ExpressRoute (URI)
 
Forse si sta provando a ridimensionare o eseguire il peering di un cloud privato esistente con il circuito ExpressRoute del cloud privato e l'operazione ha esito negativo. In tal caso, è necessario l'ID ExpressRoute. 

Nella portale di Azure selezionare **Connectivity > ExpressRoute** e copiare l' **ID ExpressRoute** negli Appunti.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione."::: 
 
> [!NOTE]
> In alcuni casi, i controlli di pre-convalida potrebbero avere esito negativo prima della distribuzione e le uniche informazioni disponibili saranno i messaggi di errore e di errore. Questi possono essere utili in molti errori, ad esempio per problemi relativi alla quota, ed è importante includere questi messaggi nella richiesta di supporto. Per raccogliere questi dati, vedere la sezione precedente [raccogliere un riepilogo degli errori](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Creare la richiesta di supporto

Per indicazioni generali sulla creazione della richiesta di supporto, vedere [come creare una richiesta di supporto tecnico di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Ecco alcune indicazioni specifiche per la creazione di un SR per la distribuzione della soluzione VMware di Azure o per gli errori di provisioning.

1. Selezionare l'icona della **Guida** e quindi **+ nuova richiesta di supporto** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::

2. Compilare tutti i campi obbligatori e nella scheda **nozioni di base** :

    - Per **tipo di problema** , selezionare **problemi di configurazione e configurazione** .

    - Per **sottotipo di problema** , selezionare **provisioning di un cloud privato** .

3. Nella scheda **Dettagli** :

    - Compilare tutti i campi obbligatori.

    - Incollare l'ID di correlazione o l'ID ExpressRoute nei campi specifici forniti. Se non viene visualizzato un campo specifico, è possibile incollarli nella casella di testo in **fornire dettagli sul problema.**

    - Incollare tutti i dettagli dell'errore, incluso il riepilogo degli errori copiati, nella casella di testo in **fornire dettagli sul problema.**

4. Esaminare e selezionare **Crea** per creare la SR.
