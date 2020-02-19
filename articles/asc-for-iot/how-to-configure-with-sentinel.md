---
title: Guida del Centro sicurezza di Azure per la configurazione con Sentinel di Azure (anteprima) | Microsoft Docs
description: Questa guida illustra come configurare Sentinel di Azure per ricevere dati dal centro sicurezza di Azure per la soluzione Internet delle cose.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463180"
---
> [!IMPORTANT]
> Il Centro sicurezza di Azure per il connettore dati su Internet in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Connetti i tuoi dati dal centro sicurezza di Azure per le cose a Sentinel di Azure (anteprima) 

Questa guida fornisce informazioni su come connettere il Centro sicurezza di Azure per i dati di Internet delle cose a Sentinel di Azure.  

> [!div class="checklist"]
> * Prerequisiti 
> * Impostazioni di connessione
> * Visualizzazione avvisi Log Analytics 

Connetti gli avvisi dal centro sicurezza di Azure per le cose e trasmettili direttamente in Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** dell'area di lavoro.
- Il **Centro sicurezza di Azure per** l'IT deve essere **abilitato** negli hub degli oggetti Internet pertinenti.
- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** per l' **Hub Azure** Internet che si vuole connettere.
- È necessario avere anche le autorizzazioni di **lettura** e **scrittura** per il **gruppo di risorse dell'hub Azure**.

> [!NOTE]
> È necessario avere a disposizione le licenze del livello standard del Centro sicurezza di Azure in esecuzione nella sottoscrizione per inviare avvisi generali sulle risorse di Azure. Con le licenze del livello gratuito necessarie per il Centro sicurezza di Azure, solo il Centro sicurezza di Azure per gli avvisi correlati all'IT verrà inviato ad Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Connetti al centro sicurezza di Azure per le cose

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul **Centro sicurezza di Azure per** il riquadro degli altri.
1. Dal riquadro in basso a destra fare clic su **Apri pagina connettore**. 
1. Fare clic su **Connetti**accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel. 
    - Se il Centro sicurezza di Azure non è abilitato nell'hub, verrà visualizzato un messaggio di avviso di abilitazione. Fare clic sul collegamento **Enable (Abilita** ) per avviare il servizio. 
1. È possibile decidere se si desidera che gli avvisi del Centro sicurezza di Azure possano generare automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti**selezionare **Abilita** per abilitare la regola di analisi predefinita per creare automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. Questa regola può essere modificata o modificata in **Analytics** > regole **attive** .

> [!NOTE]
>Per aggiornare l'elenco di hub dopo aver apportato le modifiche alla connessione, può essere necessario più di 10 secondi. 

## <a name="log-analytics-alert-display"></a>Visualizzazione Log Analytics avviso

Per usare lo schema pertinente in Log Analytics per visualizzare il Centro sicurezza di Azure per gli avvisi relativi agli elementi Internet:

1. Aprire **Logs** > **SecurityInsights** > **SecurityAlert**o cercare **SecurityAlert**. 
2. Filtrare per visualizzare solo il Centro sicurezza di Azure per gli avvisi generati dagli elementi con il seguente filtro KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Note servizio

Dopo la connessione di un hub Internet delle cose, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.


## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il Centro sicurezza di Azure per le cose a sentinella di Azure. Per ulteriori informazioni sul rilevamento delle minacce e sull'accesso ai dati di sicurezza, vedere gli articoli seguenti:

- Informazioni su come usare Sentinel di Azure per [ottenere visibilità dei dati e potenziali minacce](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Informazioni su come [accedere ai dati sulla sicurezza](how-to-security-data-access.md)