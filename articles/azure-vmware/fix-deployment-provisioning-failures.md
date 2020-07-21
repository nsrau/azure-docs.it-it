---
title: Ottenere assistenza per la distribuzione di soluzioni VMware di Azure o gli errori di provisioning
description: Come ottenere le informazioni necessarie dal cloud privato della soluzione VMware di Azure (AVS) per archiviare una richiesta di servizio per la distribuzione AVS o gli errori di provisioning.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 05c9dee088b37c37cdcdee7e745cdcd2222b63e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514578"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Ottenere assistenza per la distribuzione di soluzioni VMware di Azure o gli errori di provisioning

Questo articolo illustra come ottenere assistenza per la distribuzione di soluzioni VMware di Azure (AVS) o per il provisioning degli errori nel cloud privato aprendo una richiesta di servizio (SR) nell'portale di Azure. Prima di tutto, tuttavia, è necessario raccogliere alcune informazioni chiave nella portale di Azure. Nella maggior parte dei casi, è necessario:

- ID di correlazione (della distribuzione non riuscita)
- ID circuito ExpressRoute (quando si tenta di ridimensionare o eseguire il peering di un cloud privato esistente con il circuito ExpressRoute del cloud privato e l'errore)

## <a name="collect-the-correlation-id"></a>Raccogli l'ID di correlazione
 
Esaminiamo prima l'ID di correlazione. Quando si crea un cloud privato (o qualsiasi risorsa in Azure), viene generato un ID correlazione associato. Ogni distribuzione di Azure Resource Manager genera anche un ID correlazione univoco. Questo ID consente la creazione e la risoluzione di SR più veloci. 
 
Di seguito è riportato un esempio dell'output di una distribuzione di cloud privato non riuscita, con l'ID di correlazione evidenziato.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Distribuzione del cloud privato non riuscita con ID correlazione.":::

Copiare e salvare l'ID correlazione da includere nella richiesta di servizio. Per informazioni dettagliate, vedere [creare la richiesta di supporto](#create-your-support-request) alla fine di questo articolo.

Se l'errore si verifica nelle fasi di pre-convalida, prima della distribuzione di un cloud privato, non viene generato alcun ID di correlazione. In questo caso, è possibile fornire semplicemente le informazioni usate durante la creazione del cloud privato AVS, tra cui:

- Location
- Resource group
- Nome risorsa
 
### <a name="collect-a-summary-of-errors"></a>Raccogli un riepilogo degli errori

I dettagli di eventuali errori possono essere utili anche per la risoluzione del problema. Dalla schermata precedente selezionare **fare clic qui per i dettagli** (evidenziato) e viene visualizzato un riepilogo degli errori, come illustrato nello screenshot seguente.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Riepilogo degli errori.":::

Anche in questo caso, copiare e salvare il riepilogo da includere in SR.
 
### <a name="retrieve-past-deployments"></a>Recuperare le distribuzioni precedenti

È possibile recuperare le distribuzioni precedenti, incluse quelle non riuscite, eseguendo una ricerca nel log attività di distribuzione a cui si accede selezionando l'icona notifiche.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Notifiche aperte.":::

In notifiche selezionare **altri eventi nel log attività**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Collegamento: più eventi nel log attività.":::

Cercare quindi il nome della risorsa o un'altra informazione univoca usata per la creazione della risorsa, per individuare la distribuzione non riuscita e il relativo ID di correlazione. Nell'esempio seguente vengono illustrati i risultati della ricerca in una risorsa cloud privata (PC03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Trova le distribuzioni AVS precedenti non riuscite.":::
 
Se si seleziona il nome dell'operazione di distribuzione non riuscita, viene visualizzata una finestra con i dettagli. Selezionare la scheda JSON e cercare correlationId. Copiare e includere in SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Raccogli l'ID ExpressRoute (URI)
 
Probabilmente si ha già un cloud privato e si verifica un errore quando si prova a ridimensionarlo o a peer con il circuito ExpressRoute del cloud privato. In tal caso, è possibile usare l'ID ExpressRoute del cloud privato per identificarlo quando si crea un SR.

Quando si visualizza un cloud privato nel portale, selezionare **Connectivity > ExpressRoute** e copiare l' **ID ExpressRoute** negli Appunti.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Raccogliere un ID ExpressRoute."::: 
 
Incollare l'ID ExpressRoute nel campo appropriato nella nuova richiesta di supporto. Per altre informazioni, vedere la sezione seguente [creare la richiesta di supporto](#create-your-support-request).
 
> [!NOTE]
> In alcuni casi, i controlli di pre-convalida potrebbero avere esito negativo prima di una distribuzione e le uniche informazioni disponibili saranno i messaggi di errore e/o errore. Questi possono essere utili in diversi errori, ad esempio per problemi relativi alla quota, ed è importante includere questi messaggi nella richiesta di supporto. Per raccogliere questi dati, vedere la sezione precedente [raccogliere un riepilogo degli errori](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Creare la richiesta di supporto

Per indicazioni generali sulla creazione della richiesta di supporto, vedere [come creare una richiesta di supporto tecnico di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Di seguito sono riportate altre linee guida specifiche per la creazione di un SR per la distribuzione AVS o per errori di provisioning

1. Selezionare l'icona della **Guida** e quindi **+ nuova richiesta di supporto**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Raccogliere un ID ExpressRoute.":::

2. Compilare tutti i campi obbligatori e nella scheda **nozioni di base** :

    - Per **tipo di problema**, selezionare **problemi di configurazione e configurazione**.

    - Per **sottotipo di problema**, selezionare **provisioning di un cloud privato**.

3. Nella scheda **Dettagli** :

    - Compilare tutti i campi obbligatori.

    - Incollare l'ID di correlazione o l'ID ExpressRoute nei campi specifici forniti. Se non viene visualizzato un campo specifico, è possibile incollarli nella casella di testo in **fornire dettagli sul problema.**

    - Incollare tutti i dettagli dell'errore, incluso il riepilogo degli errori copiati, nella casella di testo in **fornire dettagli sul problema.**

4. Esaminare e selezionare **Crea** per creare la SR.
