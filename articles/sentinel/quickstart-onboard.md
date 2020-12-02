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
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 107806b6372360e501fe9cc0a21d25cff5cc35f9
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95248646"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Avvio rapido: Caricare dati in Azure Sentinel

Questa Guida introduttiva illustra come caricare dati in Azure Sentinel. 

Per caricare dati in Azure Sentinel, è necessario prima abilitare Azure Sentinel e quindi connettere le origini dati. Azure Sentinel include numerosi connettori predefiniti per l'integrazione in tempo reale con le soluzioni Microsoft, comprese le soluzioni Microsoft 365 Defender (in precedenza Microsoft Threat Protection), origini Microsoft 365 (tra cui Office 365), Azure AD, Microsoft Defender for Identity (in precedenza Azure ATP), Microsoft Cloud App Security, avvisi di Azure Defender dal Centro sicurezza di Azure e altro ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. Per connettere le origini dati con Azure Sentinel è anche possibile usare CEF (Common Event Format), Syslog o API REST. 

Dopo aver connesso le origini dati, è possibile effettuare una scelta in una raccolta di cartelle di lavoro create in maniera specialistica per far emergere informazioni dettagliate in base ai dati. Tali cartelle di lavoro possono essere facilmente personalizzate in base alle esigenze.

>[!IMPORTANT] 
> Per informazioni sugli addebiti relativi all'uso di Azure Sentinel, vedere [i prezzi di Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Prerequisiti globali

- Sottoscrizione attiva di di Azure. Se non se ne possiede una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Area d lavoro Log Analytics. Informazioni su [come creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md). Per altre informazioni sulle aree di lavoro Log Analytics, vedere [Progettazione della distribuzione dei log di Monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md).

- Per abilitare Azure Sentinel, è necessario disporre delle autorizzazioni per la sottoscrizione in cui si trova l'area di lavoro di Azure. 
- Per usare Azure Sentinel, è necessario disporre delle autorizzazioni di collaboratore o lettore per il gruppo di risorse a cui appartiene l'area di lavoro.
- Possono essere necessarie autorizzazioni aggiuntive per connettere origini dati specifiche.
- Azure Sentinel è un servizio a pagamento. Per informazioni sui prezzi, vedere [Informazioni su Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Disponibilità geografica e residenza dei dati

- Azure Sentinel può essere eseguito in aree di lavoro nella maggior parte delle [aree di disponibilità generale di Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), ad eccezione delle aree Cina e Germania (sovrane). A volte, in alcune nuove aree di Log Analytics l'onboarding del servizio Sentinel può richiedere del tempo. 

- I dati generati da Azure Sentinel, ad esempio eventi imprevisti, segnalibri e regole di analisi, possono contenere dati del cliente originati dalle aree di lavoro Log Analytics del cliente. I dati generati da Azure Sentinel vengono salvati nell'area geografica elencata nella tabella seguente, in base all'area geografica in cui si trova l'area di lavoro:

    | Area geografica dell'area di lavoro | Area geografica dei dati generati da Azure Sentinel |
    | --- | --- |
    | Stati Uniti<br>India<br>Brasile<br>Africa<br>Corea del Sud | Stati Uniti |
    | Europa<br>Francia<br>Svizzera | Europa |
    | Australia | Australia |
    | Regno Unito | Regno Unito |
    | Canada | Canada |
    | Giappone | Giappone |
    |

## <a name="enable-azure-sentinel"></a>Abilitare Azure Sentinel <a name="enable"></a>

1. Accedere al portale di Azure. Assicurarsi che la sottoscrizione in cui è stato creato Azure Sentinel sia selezionata.

1. Cercare e selezionare **Azure Sentinel**.

   ![Ricerca di servizi](./media/quickstart-onboard/search-product.png)

1. Selezionare **Aggiungi**.

1. Selezionare l'area di lavoro da usare o crearne una nuova. È possibile eseguire Azure Sentinel in più di un'area di lavoro, ma i dati sono isolati in una singola area di lavoro.

   ![Scegliere un'area di lavoro](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Le aree di lavoro predefinite create in Centro sicurezza di Azure non vengono visualizzate nell'elenco e non è possibile installare Azure Sentinel in tali aree.
   >

   >[!IMPORTANT]
   >
   > - Una volta distribuito in un'area di lavoro, Azure Sentinel **non supporta al momento** il trasferimento di tale area di lavoro in altri gruppi di risorse o sottoscrizioni. 
   >
   >   Se l'area di lavoro è già stata spostata, disabilitare tutte le regole attive in **Analytics** e abilitarle di nuovo dopo cinque minuti. Questa operazione dovrebbe essere efficace nella maggior parte dei casi, tuttavia, come già detto, non è supportata e viene eseguita a proprio rischio.

1. Selezionare **Aggiungi Azure Sentinel**.

## <a name="connect-data-sources"></a>Connettere le origini dati

Azure Sentinel inserisce i dati da app e servizi connettendosi al servizio e inoltrando gli eventi e i log ad Azure Sentinel. Per i computer fisici e le macchine virtuali, è possibile installare l'agente di Log Analytics che raccoglie i log e li inoltra ad Azure Sentinel. Per i firewall e i proxy, Azure Sentinel installa l'agente di Log Analytics in un server SysLog Linux, da cui l'agente raccoglie i file di log e li inoltra ad Azure Sentinel. 
 
1. Nel menu principale selezionare **Connettori dati**. Verrà visualizzata la raccolta di connettori dati.

1. La raccolta è un elenco di tutte le origini dati che è possibile connettere. Selezionare un'origine dati e quindi fare clic sul pulsante **Apri la pagina del connettore**.

1. La pagina del connettore mostra le istruzioni per la configurazione del connettore ed eventuali istruzioni aggiuntive eventualmente necessarie.<br>
Se ad esempio si seleziona l'origine dati **Azure Active Directory**, che consente di trasmettere in streaming i log da Azure AD ad Azure Sentinel, è possibile selezionare il tipo di log da recuperare, ad esempio log di accesso e/o log di controllo. <br> Per altre informazioni, seguire le istruzioni di installazione o [fare riferimento alla guida di connessione pertinente](connect-data-sources.md). Per informazioni sui connettori dei dati, vedere [Connettere i servizi Microsoft](connect-data-sources.md).

1. La scheda **Passaggi successivi** nella pagina del connettore mostra le cartelle di lavoro predefinite, le query di esempio e i modelli di regola di analisi pertinenti correlati al connettore dati. È possibile usarli così come sono o modificarli, ma in entrambi i casi si otterranno immediatamente informazioni dettagliate interessanti sui dati. <br>

Dopo che le origini dati sono connesse, viene avviato il trasferimento dei dati in Azure Sentinel ed è possibile iniziare a usarli. È possibile visualizzare i log nelle [cartelle di lavoro predefinite](quickstart-get-visibility.md) e iniziare a creare le query in Log Analytics per [esaminare i dati](tutorial-investigate-cases.md).

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso a eseguire l'onboarding e a connettere le origini dati ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- Trasmettere i dati da [appliance di Common Event Format](connect-common-event-format.md) in Azure Sentinel.
