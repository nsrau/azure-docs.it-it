---
title: 'Guida introduttiva: Caricare dati in Azure Sentinel'
description: Questa Guida introduttiva illustra come raccogliere dati in Azure Sentinel.
services: sentinel
author: rkarlin
ms.author: rkarlin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 09/23/2019
ms.openlocfilehash: 182dec35d515966ae883aca405cf3a95b6f1f72c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133092"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Guida introduttiva: Caricare dati in Azure Sentinel

Questa Guida introduttiva illustra come caricare dati in Azure Sentinel. 

Per caricare dati in Azure Sentinel, è necessario prima abilitare Azure Sentinel e quindi connettere le origini dati. Azure Sentinel include numerosi connettori predefiniti per l'integrazione in tempo reale con le soluzioni Microsoft, comprese le soluzioni Microsoft Threat Protection e origini Microsoft 365, tra cui Office 365, Azure AD, Azure ATP, Microsoft Cloud App Security e altre ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. Per connettere le origini dati con Azure Sentinel è anche possibile usare Common Event Format, Syslog o API REST.  

Dopo aver connesso le origini dati, è possibile effettuare una scelta in una raccolta di cartelle di lavoro create in maniera specialistica per far emergere informazioni dettagliate in base ai dati. Tali cartelle di lavoro possono essere facilmente personalizzate in base alle esigenze.

>[!NOTE] 
> I [prezzi di Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) si basano sulla quantità di dati inseriti nell'area di lavoro Log Analytics di Monitoraggio di Azure. Per alcuni connettori di dati seguenti, l'inserimento non viene addebitato. Quando tuttavia si genera un qualsiasi tipo di avviso usando i dati archiviati inseriti nell'area di lavoro Log Analytics, verranno addebitati costi in base ai [prezzi di Log Analytics di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). 
  > - Attività di Azure (log attività per operazioni di Azure)
  > - Azure Active Directory Identity Protection (per i tenant con licenze AAD P2)
  > - Azure Information Protection
  > - Azure Advanced Threat Protection (avvisi)
  > - Centro sicurezza di Azure (avvisi)
  > - Microsoft Cloud App Security (solo avvisi)
  > - Microsoft Defender Advanced Threat Protection (avvisi degli agenti di monitoraggio)
  > - Office 365 (registri di Exchange e SharePoint)
>
> I log di controllo di Azure AD/Office365 non sono gratuiti e sono offerti come livello Premium, quindi l'inserimento di questi dati viene addebitato in base ai singoli GB. 
  

## <a name="global-prerequisites"></a>Prerequisiti globali

- Sottoscrizione attiva di di Azure. Se non se ne possiede una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Area d lavoro Log Analytics. Informazioni su [come creare un'area di lavoro Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Per altre informazioni sulle aree di lavoro Log Analytics, vedere [Progettazione della distribuzione dei log di Monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md).

-  Per abilitare Azure Sentinel, è necessario disporre delle autorizzazioni per la sottoscrizione in cui si trova l'area di lavoro di Azure. 
- Per usare Azure Sentinel, è necessario disporre delle autorizzazioni di collaboratore o lettore per il gruppo di risorse a cui appartiene l'area di lavoro.
- Possono essere necessarie autorizzazioni aggiuntive per connettere origini dati specifiche.
- Azure Sentinel è un servizio a pagamento. Per informazioni sui prezzi, vedere [Informazioni su Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## Abilitare Azure Sentinel <a name="enable"></a>

1. Accedere al portale di Azure.
2. Verificare che la sottoscrizione in cui è stato creato Azure Sentinel sia selezionata. 
3. Cercare Azure Sentinel. 
   ![ricerca](./media/quickstart-onboard/search-product.png)

1. Fare clic su **+Aggiungi**.
1. Selezionare l'area di lavoro da usare o crearne una nuova. È possibile eseguire Azure Sentinel in più di un'area di lavoro, ma i dati sono isolati in una singola area di lavoro.

   ![ricerca](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Le aree di lavoro predefinite create in Centro sicurezza di Azure non vengono visualizzate nell'elenco e non è possibile installare Azure Sentinel in tali aree.
   > - Azure Sentinel può essere eseguito in aree di lavoro in qualsiasi [area di disponibilità generale di Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), ad eccezione delle aree Cina, Germania e Azure per enti pubblici. I dati generati da Azure Sentinel (ad esempio eventi imprevisti, segnalibri e regole di avviso, che possono contenere dati del cliente originati da tali aree di lavoro), vengono salvati in Europa occidentale (per aree di lavoro situate in Europa) o negli Stati Uniti orientali (per tutte le aree di lavoro con base negli Stati Uniti e per qualsiasi altra area ad eccezione dell'Europa).

6. Fare clic su **Aggiungi Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Connettere le origini dati

Azure Sentinel crea la connessione ai servizi e alle app collegandosi al servizio e inviando gli eventi e i log ad Azure Sentinel. Per i computer e le macchine virtuali, è possibile installare l'agente di Azure Sentinel che raccoglie i log e li trasmette ad Azure Sentinel. Per i firewall e i proxy, Azure Sentinel usa un server Linux Syslog in cui è installato l'agente e da cui l'agente raccoglie i file di log e li trasmette ad Azure Sentinel. 
 
1. Fare clic su **Raccolta dati**.
2. È disponibile un riquadro per ogni origine dati che è possibile connettere.<br>
Fare clic ad esempio su **Azure Active Directory**. Se si connette questa origine dati, è possibile trasmettere tutti i log da Azure AD ad Azure Sentinel. È possibile selezionare il tipo di log da ottenere, ovvero log di accesso o di controllo. <br>
In via definitiva, Azure Sentinel offre consigli per le cartelle di lavoro da installare per ogni connettore, in modo da ottenere immediatamente analisi approfondite sui dati. <br> Per altre informazioni, seguire le istruzioni di installazione o [fare riferimento alla guida di connessione pertinente](connect-data-sources.md). Per informazioni sui connettori dei dati, vedere [Connettere i servizi Microsoft](connect-data-sources.md).

Dopo che le origini dati sono connesse, viene avviato il trasferimento dei dati in Azure Sentinel ed è possibile iniziare a usarli. È possibile visualizzare i log nei [dashboard predefiniti](quickstart-get-visibility.md) e iniziare a compilare le query in Log Analytics per [esaminare i dati](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le origini dati ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- Trasmettere i dati da [appliance di Common Error Format](connect-common-event-format.md) in Azure Sentinel.
