---
title: 'Avvio rapido: Caricare dati in Azure Sentinel'
description: Questo argomento di avvio rapido illustra come eseguire l'onboarding di Azure Sentinel abilitando prima Sentinel e quindi connettendo le origini dati.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: e9d7c99a123bd92bf55a33c8d1faaf7da55d3e36
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90889029"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Avvio rapido: Caricare dati in Azure Sentinel

Questa Guida introduttiva illustra come caricare dati in Azure Sentinel. 

Per caricare dati in Azure Sentinel, è necessario prima abilitare Azure Sentinel e quindi connettere le origini dati. Azure Sentinel include numerosi connettori predefiniti per l'integrazione in tempo reale con le soluzioni Microsoft, comprese le soluzioni Microsoft 365 Defender (in precedenza Microsoft Threat Protection), origini Microsoft 365 (tra cui Office 365), Azure AD, Microsoft Defender for Identity (in precedenza Azure ATP), Microsoft Cloud App Security, avvisi di Azure Defender dal Centro sicurezza di Azure e altro ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. Per connettere le origini dati con Azure Sentinel è anche possibile usare CEF (Common Event Format), Syslog o API REST. 

Dopo aver connesso le origini dati, è possibile effettuare una scelta in una raccolta di cartelle di lavoro create in maniera specialistica per far emergere informazioni dettagliate in base ai dati. Tali cartelle di lavoro possono essere facilmente personalizzate in base alle esigenze.

>[!IMPORTANT] 
> Per informazioni sugli addebiti relativi all'uso di Azure Sentinel, vedere [i prezzi di Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Prerequisiti globali

- Sottoscrizione attiva di di Azure. Se non se ne possiede una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Area d lavoro Log Analytics. Informazioni su [come creare un'area di lavoro Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Per altre informazioni sulle aree di lavoro Log Analytics, vedere [Progettazione della distribuzione dei log di Monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md).

- Per abilitare Azure Sentinel, è necessario disporre delle autorizzazioni per la sottoscrizione in cui si trova l'area di lavoro di Azure. 
- Per usare Azure Sentinel, è necessario disporre delle autorizzazioni di collaboratore o lettore per il gruppo di risorse a cui appartiene l'area di lavoro.
- Possono essere necessarie autorizzazioni aggiuntive per connettere origini dati specifiche.
- Azure Sentinel è un servizio a pagamento. Per informazioni sui prezzi, vedere [Informazioni su Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Abilitare Azure Sentinel <a name="enable"></a>

1. Accedere al portale di Azure. Assicurarsi che la sottoscrizione in cui è stato creato Azure Sentinel sia selezionata.

1. Cercare e selezionare **Azure Sentinel**.

   ![Ricerca di servizi](./media/quickstart-onboard/search-product.png)

1. Selezionare **Aggiungi**.

1. Selezionare l'area di lavoro da usare o crearne una nuova. È possibile eseguire Azure Sentinel in più di un'area di lavoro, ma i dati sono isolati in una singola area di lavoro.

   ![Scegliere un'area di lavoro](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Le aree di lavoro predefinite create in Centro sicurezza di Azure non vengono visualizzate nell'elenco e non è possibile installare Azure Sentinel in tali aree.
   > - Azure Sentinel può essere eseguito in aree di lavoro in qualsiasi [area di disponibilità generale di Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), ad eccezione delle aree Cina e Germania (sovrane). I dati generati da Azure Sentinel (ad esempio eventi imprevisti, segnalibri e regole di avviso, che possono contenere dati del cliente originati da tali aree di lavoro), vengono salvati in Europa (per le aree di lavoro che risiedono in Europa), in Australia (per le aree di lavoro che risiedono in Australia) o nell'area Stati Uniti orientali (per le aree di lavoro che risiedono in tutte le altre aree).

   >[!IMPORTANT]
   >
   > - Una volta distribuito in un'area di lavoro, Azure Sentinel **non supporta al momento** il trasferimento di tale area di lavoro in altri gruppi di risorse o sottoscrizioni. 
   >
   >   Se l'area di lavoro è già stata spostata, disabilitare tutte le regole attive in **Analytics** e abilitarle di nuovo dopo cinque minuti. Questa operazione dovrebbe essere efficace nella maggior parte dei casi, tuttavia, come già detto, non è supportata e viene eseguita a proprio rischio.

1. Selezionare **Aggiungi Azure Sentinel**.

## <a name="connect-data-sources"></a>Connettere le origini dati

Azure Sentinel crea la connessione ai servizi e alle app collegandosi al servizio e inviando gli eventi e i log ad Azure Sentinel. Per i computer e le macchine virtuali, è possibile installare l'agente di Azure Sentinel che raccoglie i log e li trasmette ad Azure Sentinel. Per i firewall e i proxy, Azure Sentinel usa un server Linux Syslog in cui è installato l'agente e da cui l'agente raccoglie i file di log e li trasmette ad Azure Sentinel. 
 
1. Fare clic su **Connettori dati**.
1. È disponibile un riquadro per ogni origine dati che è possibile connettere.<br>
Fare clic ad esempio su **Azure Active Directory**. Se si connette questa origine dati, è possibile trasmettere tutti i log da Azure AD ad Azure Sentinel. È possibile selezionare il tipo di log da ottenere, ovvero log di accesso o di controllo. <br>
In via definitiva, Azure Sentinel offre consigli per le cartelle di lavoro da installare per ogni connettore, in modo da ottenere immediatamente analisi approfondite sui dati. <br> Per altre informazioni, seguire le istruzioni di installazione o [fare riferimento alla guida di connessione pertinente](connect-data-sources.md). Per informazioni sui connettori dei dati, vedere [Connettere i servizi Microsoft](connect-data-sources.md).

Dopo che le origini dati sono connesse, viene avviato il trasferimento dei dati in Azure Sentinel ed è possibile iniziare a usarli. È possibile visualizzare i log nei [dashboard predefiniti](quickstart-get-visibility.md) e iniziare a compilare le query in Log Analytics per [esaminare i dati](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le origini dati ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- Trasmettere i dati da [appliance di Common Event Format](connect-common-event-format.md) in Azure Sentinel.
