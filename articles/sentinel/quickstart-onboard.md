---
title: Eseguire l'onboarding nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere i dati in Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/12/2019
ms.author: rkarlin
ms.openlocfilehash: c055b3d9dec2e2538a7dc7ac0eb707c97313d0d6
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006599"
---
# <a name="on-board-azure-sentinel-preview"></a>Anteprima di onboarding Sentinel di Azure

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questa Guida introduttiva si apprenderà come caricare Sentinel di Azure. 

Per eseguire l'onboarding di Azure Sentinel, è necessario innanzitutto connettersi alle origini dati. Sentinel Azure viene fornito con una serie di connettori per le soluzioni Microsoft, disponibile all'esterno di finestra e fornendo integrazione in tempo reale, incluse le soluzioni di protezione dalle minacce di Microsoft, Microsoft 365 origini, tra cui Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security e altro ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. È anche possibile usare comune formato di eventi, Syslog o REST-API per connettersi alle origini dati con Azure Sentinel.  

Dopo la connessione alle origini dati, è possibile scegliere da una raccolta di dashboard creato in modo accurato che informazioni dettagliate basate sui dati della superficie di attacco. Questi dashboard possono essere personalizzati facilmente in base alle esigenze.


## <a name="global-prerequisites"></a>Prerequisiti globali

- Sottoscrizione di Azure attiva, se non hai uno, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Area di lavoro di Analitica di log. Informazioni su come [creare un'area di lavoro di Log Analitica](../log-analytics/log-analytics-quick-create-workspace.md)

    - Autorizzazioni di collaboratore al gruppo di risorse associato a Azure Sentinel
    - Autorizzazioni di collaboratore al gruppo di risorse per abilitare Azure Sentinel 

## Abilitare Azure Sentinel <a name="enable"></a>

1. Passare al portale di Azure.
2. Assicurarsi che sia selezionata la sottoscrizione in cui Azure Sentinel viene creato. 
3. Ricerca di Azure Sentinel. 
   ![ricerca](./media/quickstart-onboard/search-product.png)

1. Fare clic su **+Aggiungi**.
1. Selezionare l'area di lavoro che si desidera usare o crearne uno nuovo. È possibile eseguire Azure Sentinel in più di un'area di lavoro, ma i dati sono isolati in una singola area di lavoro.

   ![ricerca](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Percorso area di lavoro** è importante sapere che tutti i dati di cui si esegue lo streaming da Azure Sentinel sono archiviati nella posizione geografica dell'area di lavoro selezionato.  
   > - Aree di lavoro predefinite create dal Centro sicurezza di Azure non verranno visualizzati nell'elenco. è possibile installare Azure Sentinel su di essi.
   > - Sentinel Azure possono eseguire nelle aree di lavoro vengono distribuiti in una qualsiasi delle seguenti aree:  Australia sud-orientale, Canada centrale, India centrale, Stati Uniti orientali, Stati Uniti orientali 2 EUAP (Canary), Giappone orientale, Asia sud-orientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2.

6. Fare clic su **aggiungere Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Connettere le origini dati

Sentinel Azure crea la connessione a servizi e App per la connessione al servizio e l'inoltro degli eventi e log a Azure Sentinel. Per computer e macchine virtuali, è possibile installare l'agente Azure Sentinel che raccoglie i log e li inoltra al Sentinel di Azure. Per i firewall e proxy, Azure Sentinel utilizza un server Syslog di Linux. Su cui è installato l'agente e da cui l'agente raccoglie i log di file e li inoltra al Sentinel di Azure. 
 
1. Fare clic su **la raccolta dei dati**.
2. È disponibile un riquadro per ogni origine dati che è possibile connettersi.<br>
Ad esempio, fare clic su **Azure Active Directory**. Se ci si connette questa origine dati, il flusso di tutti i log da Azure AD in Azure Sentinel. È possibile selezionare il tipo di log di cui si desidera per ottenere - i log di accesso e/o log di controllo. <br>
Nella parte inferiore, Sentinel Azure fornisce raccomandazioni per i dashboard di cui è necessario installare per ogni connettore in modo che si può immediatamente ottenere interessanti insights tra i dati. <br> Seguire le istruzioni di installazione oppure [, vedere la Guida relativa connessione](connect-data-sources.md) per altre informazioni. Per informazioni sui connettori di dati, vedere [servizi di Microsoft Connect](connect-data-sources.md).

Dopo che i dati origini connesse, i dati viene avviato lo streaming in Azure Sentinel ed sono pronti per poter iniziare a usare. È possibile visualizzare i log nel [dashboard incorporati](quickstart-get-visibility.md) e iniziare a compilare le query in Log Analitica per [esaminare i dati](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passaggi successivi
In questo documento sono illustrate la connessione a origini dati a Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
- Stream dei dati dal [Appliance di formato di errore comune](connect-common-event-format.md) in Sentinel di Azure.
