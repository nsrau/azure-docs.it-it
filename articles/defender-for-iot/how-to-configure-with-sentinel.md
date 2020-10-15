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
ms.openlocfilehash: 12f65d0e7f9c380f77fe4189d26fdeafd426295b
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090795"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Connetti i tuoi dati da Defender for Internet a Sentinel di Azure (anteprima)

Il connettore Azure Defender per il Data Connector in Azure Sentinel è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

In questa guida viene illustrato come connettere il Defender per i dati di Internet delle cose in Sentinel di Azure.

> [!div class="checklist"]
> * Prerequisiti
> * Impostazioni di connessione
> * Visualizzazione avvisi Log Analytics

Connetti gli avvisi da Defender per l'IT e trasmettili direttamente in Azure Sentinel.

Grazie all'integrazione più rigorosa di Azure Defender con Azure Sentinel, il primo SIEM nativo per il cloud e il primo SIEM con le cose native e la sicurezza, Microsoft offre un approccio più semplice alla sicurezza unificata tra le reti IT e industriali. In combinazione con il machine learning di Azure Sentinel, questa integrazione consente alle organizzazioni di rilevare rapidamente gli attacchi multifase che spesso attraversano i limiti IT e OT. Azure Defender per l'integrazione con le funzionalità di orchestrazione, automazione e risposta (SOAR) di Azure Sentinel, inoltre, consente di abilitare la risposta e la prevenzione automatizzate grazie a PlayBook predefiniti per l'ottimizzazione dell'OT. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** dell'area di lavoro.
- Il **Defender per** l'IT deve essere **abilitato** negli hub Internet pertinenti.
- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** per l' **Hub Azure** Internet che si vuole connettere.
- È necessario avere anche le autorizzazioni di **lettura** e **scrittura** per il **gruppo di risorse dell'hub Azure**.


## <a name="connect-to-defender-for-iot"></a>Connetti a Defender per le cose

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Defender for** Internet.
1. Nella parte inferiore del riquadro destro fare clic su **Apri pagina connettore**.
1. Fare clic su **Connetti**accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel.
    - Se Defender for Internet non è abilitato nell'hub, verrà visualizzato un messaggio di avviso di abilitazione. Fare clic sul collegamento **Enable (Abilita** ) per avviare e abilitare il servizio.
1. È possibile decidere se si desidera che gli avvisi da Defender per gli eventi per generare automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti**selezionare **Abilita** per abilitare la regola per la creazione automatica degli eventi imprevisti dagli avvisi generati.  Questa regola può essere modificata o modificata in **analisi**  >  regole**attive** .

> [!NOTE]
>Per aggiornare l'elenco di hub dopo aver apportato le modifiche alla connessione, può essere necessario più di 10 secondi.

## <a name="using-log-analytics-for-alert-display"></a>Utilizzo di Log Analytics per la visualizzazione degli avvisi

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
