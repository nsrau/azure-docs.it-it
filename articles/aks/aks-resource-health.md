---
title: Verificare la presenza di eventi di Integrità risorse che incidono sul cluster AKS (anteprima)
description: Verificare lo stato di integrità del cluster AKS usando Integrità risorse di Azure.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: d14ed7e714eac175dc7a17bbccd2150f9bb01ef9
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752735"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Verificare la presenza di eventi di Integrità risorse che incidono sul cluster AKS (anteprima)


Quando si eseguono i carichi di lavoro del contenitore su AKS, è necessario assicurarsi di poter risolvere i problemi e risolvere i problemi non appena si verificano per ridurre al minimo l'effetto sulla disponibilità dei carichi di lavoro. [Integrità risorse di Azure](https://docs.microsoft.com/azure/service-health/resource-health-overview) offre visibilità sui diversi eventi di integrità che possono causare l'indisponibilità del cluster AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Apri Integrità risorse

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Per accedere Integrità risorse per il cluster AKS:

- Passare al cluster AKS nel [portale di Azure](https://portal.azure.com).
- Selezionare **integrità risorse** nel percorso di spostamento a sinistra.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Per accedere Integrità risorse per tutti i cluster nella sottoscrizione:

- Cercare **integrità del servizio** nel [portale di Azure](https://portal.azure.com) e passare a tale stato.
- Selezionare **integrità risorsa** nel percorso di spostamento a sinistra.
- Selezionare la sottoscrizione e impostare il tipo di risorsa su Azure Kubernetes Service (AKS).

![risorsa-controllo integrità](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Verificare lo stato di integrità

Integrità risorse di Azure consente di diagnosticare e ottenere supporto per i problemi del servizio che interessano le risorse di Azure. Integrità risorse report sull'integrità corrente e passata delle risorse e consente di determinare se il problema è causato da un'azione avviata dall'utente o da un evento Platform.

Integrità risorse riceve i segnali per il cluster gestito per determinare lo stato di integrità del cluster. Esamina lo stato di integrità del cluster AKS e segnala le azioni necessarie per ogni segnale di integrità. Questi segnali variano da problemi di risoluzione automatica, aggiornamenti pianificati, eventi di integrità non pianificati e indisponibilità causati da azioni avviate dall'utente. Questi segnali sono classificati con lo stato di integrità del Integrità risorse di Azure: *disponibile*, non *disponibile*, *sconosciuto*e *danneggiato*.

- **Disponibile**: quando non sono presenti problemi noti che interessano l'integrità del cluster, integrità risorse segnala il cluster come *disponibile*.

- Non **disponibile**: quando esiste una piattaforma o un evento non di piattaforma che influisce sull'integrità del cluster, integrità risorse segnala il cluster come non *disponibile*.

- **Sconosciuto**: quando si verifica una perdita temporanea della connessione alle metriche di integrità del cluster, integrità risorse segnala il cluster come *sconosciuto*.

- **Danneggiato**: quando si verifica un problema di integrità che richiede l'intervento dell'utente, integrità risorse segnala il cluster come *danneggiato*.

Per ulteriori informazioni su ciò che indica lo stato di integrità, visitare [integrità risorse Panoramica](https://docs.microsoft.com/azure/service-health/resource-health-overview#health-status).

### <a name="view-historical-data"></a>Visualizzare i dati cronologici

È anche possibile visualizzare gli ultimi 30 giorni di informazioni cronologiche Integrità risorse nella sezione **cronologia integrità** .

## <a name="next-steps"></a>Passaggi successivi

Eseguire i controlli sul cluster per risolvere ulteriormente i problemi del cluster usando la [diagnostica AKS](https://docs.microsoft.com/azure/aks/concepts-diagnostics).
