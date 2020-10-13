---
title: Configurare Azure Sentinel per Defender per l'it (anteprima)
description: Viene illustrato come configurare Sentinel di Azure per ricevere i dati dalla soluzione Defender for Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940059"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Connetti i tuoi dati da Defender for Internet a Sentinel di Azure (anteprima)

Il Centro sicurezza di Azure per il connettore dati su Internet in Sentinel di Azure è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

In questa guida viene illustrato come connettere il Defender per i dati di Internet delle cose in Sentinel di Azure.

> [!div class="checklist"]
> * Prerequisiti
> * Impostazioni di connessione
> * Visualizzazione avvisi Log Analytics

Connetti gli avvisi da Defender per l'IT e trasmettili direttamente in Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** dell'area di lavoro.
- Il **Defender per** l'IT deve essere **abilitato** negli hub Internet pertinenti.
- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** per l' **Hub Azure** Internet che si vuole connettere.
- È necessario avere anche le autorizzazioni di **lettura** e **scrittura** per il **gruppo di risorse dell'hub Azure**.

> [!NOTE]
> È necessario avere a disposizione le licenze del livello standard del Centro sicurezza di Azure in esecuzione nella sottoscrizione per inviare avvisi generali sulle risorse di Azure. Con le licenze per il livello gratuito richieste per Defender per l'it, solo i Defender per gli avvisi correlati all'it verranno trasmessi ad Azure Sentinel.

## <a name="connect-to-defender-for-iot"></a>Connetti a Defender per le cose

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Defender for** Internet.
1. Nella parte inferiore del riquadro destro fare clic su **Apri pagina connettore**.
1. Fare clic su **Connetti**accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel.
    - Se Defender for Internet non è abilitato nell'hub, verrà visualizzato un messaggio di avviso di abilitazione. Fare clic sul collegamento **Enable (Abilita** ) per avviare e abilitare il servizio.
1. È possibile decidere se si desidera che gli avvisi da Defender per gli eventi per generare automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti**selezionare **Abilita** per abilitare la regola per la creazione automatica degli eventi imprevisti dagli avvisi generati.  Questa regola può essere modificata o modificata in **analisi**  >  regole**attive** .

> [!NOTE]
>Per aggiornare l'elenco di hub dopo aver apportato le modifiche alla connessione, può essere necessario più di 10 secondi.

## <a name="log-analytics-alert-display"></a>Visualizzazione Log Analytics avviso

Per utilizzare lo schema pertinente in Log Analytics per visualizzare il Defender per gli avvisi di Internet delle cose:

1. Aprire **logs**  >  **SecurityInsights**  >  **SecurityAlert**o cercare **SecurityAlert**.
1. Filtrare per visualizzare solo il Defender per gli avvisi generati da un sacco di elementi usando il filtro KQL seguente:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Note servizio

Dopo la connessione di un hub Internet delle cose, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Defender per le cose a sentinella di Azure. Per ulteriori informazioni sul rilevamento delle minacce e sull'accesso ai dati di sicurezza, vedere gli articoli seguenti:

- Informazioni su come usare Sentinel di Azure per [ottenere visibilità dei dati e potenziali minacce](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Informazioni su come [accedere ai dati sulla sicurezza](how-to-security-data-access.md)
