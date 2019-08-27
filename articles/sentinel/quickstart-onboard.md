---
title: Onboarding nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: rkarlin
ms.openlocfilehash: b609dc70c45941ec1132c7cdf614cf9bec8119ff
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019012"
---
# <a name="on-board-azure-sentinel-preview"></a>Anteprima di Azure Sentinel a bordo

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questa Guida introduttiva si apprenderà come caricare Azure Sentinel. 

Per la sentinella di Azure, è prima di tutto necessario abilitare Azure Sentinel e quindi connettere le origini dati. Azure Sentinel è dotato di un numero di connettori per le soluzioni Microsoft, disponibile in modalità predefinita e fornisce integrazione in tempo reale, incluse soluzioni di protezione dalle minacce Microsoft, Microsoft 365 origini, tra cui Office 365, Azure AD, Azure ATP e Microsoft Cloud App Security e altro ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. È anche possibile usare il formato di evento comune, syslog o REST-API per connettere le origini dati a Sentinel di Azure.  

Dopo aver connesso le origini dati, è possibile scegliere da una raccolta di dashboard creati in maniera specialistica che emergono informazioni dettagliate in base ai dati. Questi dashboard possono essere facilmente personalizzati in base alle esigenze.


## <a name="global-prerequisites"></a>Prerequisiti globali

- Una sottoscrizione di Azure attiva, se non è già disponibile, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Area di lavoro Log Analytics. Informazioni su come [creare un'area di lavoro log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Per abilitare Sentinel di Azure, sono necessarie le autorizzazioni di collaboratore per la sottoscrizione in cui risiede l'area di lavoro di Azure Sentinel. 
- Per usare Sentinel di Azure, è necessario disporre delle autorizzazioni Collaboratore o lettore per il gruppo di risorse a cui appartiene l'area di lavoro
- Potrebbero essere necessarie autorizzazioni aggiuntive per connettere origini dati specifiche
 
## Abilitare Sentinel di Azure<a name="enable"></a>

1. Passare alla portale di Azure.
2. Assicurarsi che la sottoscrizione in cui è stato creato Azure Sentinel sia selezionata. 
3. Cercare Azure Sentinel. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Fare clic su **+Aggiungi**.
1. Selezionare l'area di lavoro che si vuole usare o crearne una nuova. È possibile eseguire Azure Sentinel in più di un'area di lavoro, ma i dati sono isolati in una singola area di lavoro.

   ![cerca](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Le aree di lavoro predefinite create dal centro sicurezza di Azure non verranno visualizzate nell'elenco; non è possibile installare Sentinel di Azure su di essi.
   > - Azure Sentinel può essere eseguito su aree di lavoro distribuite in una delle aree seguenti: Australia orientale, Australia sudorientale, Canada centrale, India centrale, Stati Uniti orientali, Stati Uniti orientali 2 EUAP (Canary), Giappone orientale, Asia sudorientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Francia centrale, Corea centrale, Europa settentrionale, Stati Uniti orientali 2, Asia orientale, Stati Uniti occidentali, Stati Uniti orientali, Stati Uniti centro-meridionali.


6. Fare clic su **Aggiungi Sentinel di Azure**.
  

## <a name="connect-data-sources"></a>Connettere le origini dati

Azure Sentinel crea la connessione ai servizi e alle app connettendosi al servizio e inviando gli eventi e i log ad Azure Sentinel. Per i computer e le macchine virtuali, è possibile installare l'agente Sentinel di Azure che raccoglie i log e li trasmette ad Azure Sentinel. Per i firewall e i proxy, Azure Sentinel usa un server syslog Linux. L'agente è installato e da cui l'agente raccoglie i file di log e li trasmette ad Azure Sentinel. 
 
1. Fare clic su **raccolta dati**.
2. È disponibile un riquadro per ogni origine dati che è possibile connettere.<br>
Ad esempio, fare clic su **Azure Active Directory**. Se si connette questa origine dati, è possibile trasmettere tutti i log da Azure AD in Sentinel di Azure. È possibile selezionare il tipo di log da ottenere per i log di accesso e/o i log di controllo. <br>
In fondo, Azure Sentinel fornisce consigli per i dashboard da installare per ogni connettore, in modo da poter ottenere immediatamente informazioni interessanti sui dati. <br> Per ulteriori informazioni, seguire le istruzioni di installazione o [fare riferimento alla guida alla connessione pertinente](connect-data-sources.md) . Per informazioni sui connettori dati, vedere [connettere i servizi Microsoft](connect-data-sources.md).

Una volta connesse le origini dati, i dati avviano lo streaming in Sentinel di Azure ed è possibile iniziare a usare. È possibile visualizzare i log nei [dashboard predefiniti](quickstart-get-visibility.md) e iniziare a creare query in log Analytics per [analizzare i dati](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le origini dati ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
- Trasmettere i dati dalle [Appliance comuni in formato errore](connect-common-event-format.md) in Sentinel di Azure.
