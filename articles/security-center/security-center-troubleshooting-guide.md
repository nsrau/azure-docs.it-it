---
title: Guida alla risoluzione dei problemi del Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento facilita la risoluzione dei problemi nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: yurid
ms.openlocfilehash: 0e0a0ce5c0795cec0e47cd5f729099f4762381a2
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guida alla risoluzione dei problemi del Centro sicurezza di Azure
Questa guida è destinata a professionisti IT, analisti della sicurezza delle informazioni e amministratori cloud le cui organizzazioni usano il Centro sicurezza di Azure e devono risolvere i problemi correlati.

>[!NOTE] 
>A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>

## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi
Questa guida illustra come risolvere i problemi correlati al Centro sicurezza. La maggior parte delle attività di risoluzione dei problemi nel Centro sicurezza di Azure viene eseguita osservando prima di tutto i record del [log di controllo](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) del componente in cui si è verificato il problema. Tramite i log di controllo, è possibile determinare:

* Quali operazioni sono state eseguite.
* Chi ha avviato l'operazione.
* Quando si è verificata l'operazione.
* Lo stato dell'operazione.
* I valori di altre proprietà che possono essere utili per ricerche sull'operazione.

Il log di controllo contiene tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse, ma non include quelle di lettura (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Il Centro sicurezza usa Microsoft Monitoring Agent, lo stesso agente usato da Operations Management Suite e dal servizio Log Analytics, per raccogliere dati di protezione dalle macchine virtuali di Azure. Dopo avere abilitato la raccolta dei dati e installato correttamente l'agente nel computer di destinazione, sarà in esecuzione il processo seguente:

* HealthService.exe

Se si apre la console di gestione dei servizi (services.msc), verrà visualizzato anche il servizio Microsoft Monitoring Agent in esecuzione come illustrato di seguito:

![Services](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Per visualizzare la versione dell'agente di cui si dispone, aprire **Gestione attività**, nella scheda **Processi** individuare il **Servizio Microsoft Monitoring Agent**, fare doppio clic su di esso e fare clic su **Proprietà**. Nella scheda **Dettagli** cercare la versione del file, come illustrato di seguito:

![File](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)
   

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scenari di installazione di Microsoft Monitoring Agent
Esistono due scenari di installazione che possono produrre risultati diversi quando si installa Microsoft Monitoring Agent nel computer in uso. Gli scenari supportati sono:

* **Agente installato automaticamente tramite il Centro sicurezza**: in questo scenario sarà possibile visualizzare gli avvisi sia con il Centro sicurezza che tramite la ricerca log. Si riceveranno notifiche tramite posta elettronica all'indirizzo e-mail configurato nei criteri di sicurezza per la sottoscrizione a cui appartiene la risorsa.
.
* **Agente installato manualmente in una macchina virtuale situata in Azure**: in questo scenario, se si usano agenti scaricati e installati manualmente prima di febbraio 2017, sarà possibile visualizzare gli avvisi nel portale del Centro sicurezza solo se si filtra in base alla sottoscrizione a cui appartiene l'area di lavoro. Nel caso in cui si filtri in base alla sottoscrizione a cui che appartiene la risorsa, non sarà possibile visualizzare gli avvisi. Si riceveranno notifiche tramite posta elettronica all'indirizzo e-mail configurato nei criteri di sicurezza per la sottoscrizione a cui appartiene l'area di lavoro.

>[!NOTE]
> Per evitare il comportamento descritto nel secondo caso, assicurarsi di scaricare la versione più recente dell'agente.
> 

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Risoluzione dei problemi di rete di Microsoft Monitoring Agent
Per far sì che gli agenti si connettano e si registrino con il Centro sicurezza, devono avere accesso alle risorse di rete, compresi gli URL di dominio e i numeri di porta.

- Per i server proxy, è necessario assicurarsi che le risorse del server proxy appropriate siano configurate nelle impostazioni dell'agente. Leggere questo articolo per altre informazioni su [come modificare le impostazioni del proxy](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings).
- Per i firewall che limitano l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso a OMS. Non è necessaria alcuna azione sulle impostazioni dell'agente.

Nella tabella seguente vengono visualizzate le risorse necessarie per la comunicazione.

| Risorsa agente | Porte | Ignorare l'analisi HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sì |
| *.oms.opinsights.azure.com | 443 | Sì |
| *.blob.core.windows.net | 443 | Sì |
| *.azure-automation.net | 443 | Sì |

Se si verificano problemi di caricamento con l'agente, assicurarsi di leggere l'articolo [Risoluzione dei problemi di integrazione di Operations Management Suite](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Risoluzione dei problemi relativi al mancato funzionamento della protezione degli endpoint

L'agente guest è il processo padre di tutte le operazioni eseguite dall'estensione [Microsoft Antimalware](../security/azure-security-antimalware.md). Quando il processo dell'agente guest non riesce, anche Microsoft Antimalware che viene eseguito come processo figlio dell'agente guest potrebbe non riuscire.  In scenari come questi è consigliabile verificare le opzioni seguenti:

- Che la macchina virtuale di destinazione sia un'immagine personalizzata e che l'autore della macchina virtuale non abbia mai installato l'agente guest.
- Che la destinazione sia una VM di Linux e non una VM di Windows per cui l'installazione della versione di Windows dell'estensione antimalware su una VM Linux avrebbe esito negativo. L'agente guest Linux presenta requisiti specifici per quanto riguarda la versione del sistema operativo e i pacchetti necessari e se questi requisiti non vengono soddisfatti l'agente della VM non funzionerà. 
- Che la VM sia stata creata con una versione precedente dell'agente guest. In questo caso è necessario tenere presente che alcuni agenti precedenti potrebbero non aggiornarsi automaticamente alla versione più recente e questo potrebbe causare il problema. Usare sempre la versione più recente dell'agente guest quando si creano le immagini personali.
- Alcuni software di amministrazione di terze parti possono disabilitare l'agente guest o bloccare l'accesso a determinati percorsi di file. Se sulla VM sono installati software terzi, assicurarsi che l'agente si trovi nell'elenco di esclusione.
- Alcune impostazioni del firewall o il gruppo di sicurezza di rete (NSG) potrebbero bloccare il traffico di rete da e verso l'agente guest.
- Determinati elenchi di controllo di accesso (ACL) potrebbero impedire l'accesso al disco.
- La mancanza di spazio su disco può bloccare il corretto funzionamento dell'agente guest. 

Per impostazione predefinita l'interfaccia utente di Microsoft Antimalware è disabilitata, leggere [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Abilitazione dell'interfaccia utente di Microsoft Antimalware in seguito alla distribuzione delle VM in Azure Resource Manager) per ulteriori informazioni su come abilitarla in caso di necessità.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Risoluzione dei problemi di caricamento del dashboard

In caso di problemi di caricamento del dashboard del Centro sicurezza, assicurarsi che l'utente che registra la sottoscrizione al Centro sicurezza, vale a dire il primo utente che apre il Centro sicurezza con la sottoscrizione, e l'utente che vuole abilitare la raccolta dei dati siano *Proprietario* o *Collaboratore* nella sottoscrizione. A partire da quel momento, anche gli utenti con il ruolo *Lettore* nella sottoscrizione possono visualizzare i dashboard, gli avvisi, le raccomandazioni e i criteri.

## <a name="contacting-microsoft-support"></a>Contattare il supporto tecnico Microsoft
Alcuni problemi possono essere identificati usando le linee guida contenute in questo articolo, altri sono documentati nel [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)pubblico del Centro sicurezza. Tuttavia, se è necessario un altro tipo di risoluzione dei problemi, è possibile aprire una nuova richiesta di supporto tramite il **portale di Azure**, come illustrato di seguito: 

![Supporto tecnico Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come configurare i criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md) : informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure

