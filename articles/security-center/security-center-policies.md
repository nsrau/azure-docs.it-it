---
title: Impostare i criteri di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 2593e6846c897644017083b49ad4ba8219696c6c
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="set-security-policies-in-azure-security-center"></a>Impostare i criteri di sicurezza nel Centro sicurezza di Azure
Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza mostrando i passaggi necessari per eseguire questa attività.

>[!NOTE] 
>A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>

## <a name="what-are-security-policies"></a>Informazioni sui criteri di sicurezza
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza è possibile definire i criteri per le sottoscrizioni di Azure in base alle esigenze di sicurezza della società e al tipo di applicazioni o al livello di riservatezza dei dati di ogni sottoscrizione.

Ad esempio, le risorse usate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle risorse usate per le applicazioni di produzione. In modo analogo, le applicazioni che usano dati regolamentati come le informazioni personali possono richiedere un maggiore livello di sicurezza. I criteri di protezione abilitati nel Centro sicurezza di Azure determinano i suggerimenti per la sicurezza e il monitoraggio per identificare le potenziali vulnerabilità e attenuare le minacce. Per altre informazioni su come determinare l'opzione più appropriata, vedere [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md) .

## <a name="set-security-policies"></a>Impostare i criteri di sicurezza
È possibile configurare criteri di sicurezza per ogni sottoscrizione. Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore di tale sottoscrizione. Accedere al portale di Azure e seguire questa procedura per configurare i criteri di sicurezza nel Centro sicurezza di Azure:

1. Fare clic sul riquadro **Criteri** nel dashboard del Centro sicurezza.
2. Nel pannello Criteri di sicurezza che viene visualizzato selezionare la sottoscrizione in cui abilitare i criteri di sicurezza.

    ![Definizione dei criteri](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Il pannello **Criteri di sicurezza** per la sottoscrizione selezionata viene aperto con un set di opzioni. Le opzioni disponibili in questo pannello sono:

   * **Criteri di prevenzione**: usare questa opzione per configurare criteri per ogni sottoscrizione o gruppo di risorse.  
   * **Notifica di posta elettronica**: usare questa opzione per configurare una notifica di posta elettronica che viene inviata alla prima occorrenza giornaliera di un avviso e per gli avvisi di elevata gravità. Le preferenze di posta elettronica possono essere configurate solo per i criteri della sottoscrizione. Per altre informazioni su come configurare una notifica di posta elettronica, vedere [Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure](security-center-provide-security-contact-details.md) .
   * **Piano tariffario**: usare questa opzione per aggiornare il piano tariffario selezionato. Per altre informazioni sui prezzi, vedere [Centro sicurezza Prezzi](security-center-pricing.md).
4. Verificare che l'opzione **Raccogli dati dalle macchine virtuali** sia **Sì**. Questa opzione abilita la raccolta automatica dei log per le risorse nuove ed esistenti tramite Microsoft Monitoring Agent, lo stesso agente usato da Operations Management Suite e dal servizio Log Analytics. I dati raccolti dall'agente verranno archiviati in una o più aree di lavoro di Log Analytics esistenti associate alla sottoscrizione di Azure o in una o più aree di lavoro nuove, a seconda dell'area geografica della macchina virtuale.

5. Nel pannello **Criteri di sicurezza** fare clic su **Criteri di prevenzione** per visualizzare le opzioni disponibili. Fare clic su **Sì** per abilitare le raccomandazioni sulla sicurezza da usare per la sottoscrizione.

    ![Selezione dei criteri di sicurezza](./media/security-center-policies/security-center-policies-fig4-newUI.png)

Usare la tabella seguente come riferimento per comprendere ogni opzione:

| Criteri | Quando lo stato è Sì |
| --- | --- |
| Aggiornamenti del sistema |Recupera un elenco giornaliero degli aggiornamenti della sicurezza e critici da Windows Update o da Windows Server Update Services. L'elenco recuperato dipende dal servizio configurato per tale macchina virtuale e consiglia di applicare gli aggiornamenti mancanti. Per i sistemi Linux, il criterio usa il sistema di gestione pacchetti fornito dalla distribuzione per determinare per quali pacchetti sono disponibili aggiornamenti. Controlla anche la presenza di aggiornamenti critici e della sicurezza dalle macchine virtuali di [Servizi cloud di Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilità del sistema operativo |Analizza giornalmente le configurazioni del sistema operativo per determinare i problemi che potrebbero rendere vulnerabile agli attacchi la macchina virtuale. Il criterio consiglia anche le modifiche alla configurazione necessarie per risolvere queste vulnerabilità. Vedere l' [elenco delle baseline consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per altre informazioni sulle configurazioni specifiche monitorate. (Al momento, Windows Server 2016 non è completamente supportato.) |
| Endpoint Protection |Suggerisce l'Endpoint Protection di cui eseguire il provisioning per tutte le macchine virtuali di Windows, per identificare e rimuovere virus, spyware e altro software dannoso. |
| Crittografia del disco |Suggerisce di abilitare la crittografia dischi in tutte le macchine virtuali per migliorare la protezione dei dati inattivi. |
| Gruppi di sicurezza di rete |Consiglia di configurare [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) per controllare il traffico in ingresso e in uscita nelle VM che hanno endpoint pubblici. I gruppi di sicurezza di rete configurati per una subnet verranno ereditati da tutte le interfacce di rete della macchina virtuale se non diversamente specificato. Oltre a controllare che sia stato configurato un gruppo di sicurezza di rete, questo criterio valuta le regole di sicurezza in ingresso per identificare le regole che consentono il traffico in ingresso. |
| Web application firewall |Consiglia di effettuare il provisioning di un Web application firewall nelle macchine virtuali quando una delle due affermazioni seguenti è vera: </br></br>Viene usato l'[IP pubblico a livello di istanza](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) e le regole di sicurezza in ingresso per il gruppo di sicurezza di rete associato vengono configurate in modo da consentire l'accesso alla porta 80/443.</br></br>IP con carico bilanciato e le regole NAT (Network Address Translation) in ingresso e di bilanciamento del carico associate sono configurate per consentire l'accesso alla porta 80/443. Per altre informazioni, vedere [Supporto di Azure Resource Manager per il servizio di bilanciamento del carico](../load-balancer/load-balancer-arm.md). |
| Firewall di nuova generazione |Estende le protezioni di rete oltre i gruppi di sicurezza di rete predefiniti di Azure. Il Centro sicurezza troverà le distribuzioni per cui è consigliabile un firewall di nuova generazione e consentirà di effettuare il provisioning di un'appliance virtuale. |
| Servizio di controllo SQL e rilevamento delle minacce |Consiglia l'abilitazione del controllo dell'accesso al database di Azure per la conformità e il rilevamento avanzato delle minacce, per scopi di analisi. |
| Crittografia SQL |Consiglia l'abilitazione della crittografia dati inattivi per il database SQL di Azure, i backup associati e file di log delle transazioni. Anche se i dati vengono violati, non saranno leggibili. |
| Valutazione della vulnerabilità |Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| Crittografia di archiviazione |Questa funzionalità è attualmente disponibile per BLOB di Azure e File di Azure. Si noti che dopo l'abilitazione della Crittografia del servizio di archiviazione verranno crittografati solo i nuovi dati, mentre i file già esistenti nell'account di archiviazione rimarranno non crittografati. |

Dopo avere configurato tutte le opzioni, fare clic su **OK** nel pannello **Criteri di sicurezza** contenente le raccomandazioni e quindi su **Salva** nel pannello **Criteri di sicurezza** contenente le impostazioni iniziali.

> [!NOTE]
> Il piano tariffario è ancora applicabile per il livello di gruppo di risorse. Per altre informazioni, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come configurare i criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md). Informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md). Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

