---
title: Guida alla risoluzione dei problemi del Centro sicurezza di Azure | Documentazione Microsoft
description: Questa guida è destinata ai professionisti IT, agli analisti della sicurezza e agli amministratori cloud che devono risolvere i problemi correlati al Centro sicurezza di Azure.This guide is for IT professionals, security analysts, and cloud admins who need to troubleshoot Azure Security Center related issues.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 1c751fc31ba9066cf49eabbb86d37eda230c9c98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062895"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guida alla risoluzione dei problemi del Centro sicurezza di Azure

Questa guida è destinata a professionisti IT, analisti della sicurezza delle informazioni e amministratori cloud le cui organizzazioni usano il Centro sicurezza di Azure e devono risolvere i problemi correlati.

Il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.

## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questa guida illustra come risolvere i problemi correlati al Centro sicurezza.

Tipi di avviso:

* Analisi del comportamento delle macchine virtuali (VMBA)
* Analisi di rete
* Analisi di database SQL e SQL Data Warehouse
* Informazioni contestuali

A seconda dei tipi di avvisi, i clienti possono raccogliere le informazioni necessarie per esaminare l'avviso usando le risorse seguenti:

* Log di sicurezza del visualizzatore eventi della macchina virtuale in Windows
* AuditD in Linux
* I log attività di Azure e i log di diagnostica nella risorsa interessata.

I clienti possono condividere feedback per la descrizione e la pertinenza dell'avviso. Passare all'avviso, selezionare il pulsante **Le informazioni sono state utili?**, selezionare il motivo e quindi immettere un commento per spiegare i commenti e i suggerimenti. Questo canale di feedback viene monitorato costantemente allo scopo di migliorare gli avvisi forniti.

## <a name="audit-log"></a>Log di controllo

La maggior parte delle attività di risoluzione dei problemi nel Centro sicurezza di Azure viene eseguita osservando prima di tutto i record del [log di controllo](../azure-monitor/platform/platform-logs-overview.md) del componente in cui si è verificato il problema. Tramite i log di controllo, è possibile determinare:

* Quali operazioni sono state eseguite.
* Chi ha avviato l'operazione.
* Quando si è verificata l'operazione.
* Lo stato dell'operazione.
* I valori di altre proprietà che possono essere utili per ricerche sull'operazione.

Il log di controllo contiene tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse, ma non include quelle di lettura (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent

Il Centro sicurezza usa Microsoft Monitoring Agent, ovvero lo stesso agente usato dal servizio Monitoraggio di Azure, per raccogliere dati di sicurezza dalle macchine virtuali di Azure.Security Center uses the Microsoft Monitoring Agent – that is the same agent used by the Azure Monitor service , to collect security data from your Azure virtual machines. Dopo avere abilitato la raccolta dei dati e installato correttamente l'agente nel computer di destinazione, sarà in esecuzione il processo seguente:

* HealthService.exe

Se si apre la console di gestione dei servizi (services.msc), verrà visualizzato anche il servizio Microsoft Monitoring Agent in esecuzione come illustrato di seguito:

![Servizi](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Per visualizzare la versione dell'agente di cui si dispone, aprire **Gestione attività**, nella scheda **Processi** individuare il **Servizio Microsoft Monitoring Agent**, fare doppio clic su di esso e fare clic su **Proprietà**. Nella scheda **Dettagli** cercare la versione del file, come illustrato di seguito:

![File](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scenari di installazione di Microsoft Monitoring Agent

Esistono due scenari di installazione che possono produrre risultati diversi quando si installa Microsoft Monitoring Agent nel computer in uso. Gli scenari supportati sono:

* **Agente installato automaticamente tramite il Centro sicurezza**: in questo scenario sarà possibile visualizzare gli avvisi sia con il Centro sicurezza che tramite la ricerca log. Si riceveranno notifiche tramite posta elettronica all'indirizzo di posta elettronica configurato nei criteri di sicurezza per la sottoscrizione a cui appartiene la risorsa.

* **Agente installato manualmente in una macchina virtuale situata in Azure: in**questo scenario si usano agenti scaricati e installati manualmente prima di febbraio 2017, è possibile visualizzare gli avvisi nel portale del Centro sicurezza solo se si applica il filtro in base alla sottoscrizione a cui appartiene l'area di lavoro. Se si applica un filtro in base alla sottoscrizione a cui appartiene la risorsa, non verrà visualizzato alcun avviso. Si riceveranno notifiche tramite posta elettronica all'indirizzo di posta elettronica configurato nei criteri di sicurezza per la sottoscrizione a cui appartiene l'area di lavoro.

> [!NOTE]
> Per evitare il comportamento descritto nel secondo scenario, assicurarsi di scaricare la versione più recente dell'agente.

## <a name="monitoring-agent-health-issues"></a>Monitoraggio dei problemi di integrità degli agentiMonitoring agent health issues<a name="mon-agent"></a>

**Stato di monitoraggio** definisce il motivo per cui il Centro sicurezza non riesce a monitorare correttamente le VM e i computer inizializzati per il provisioning automatico. La tabella seguente mostra i valori, le descrizioni e le procedure di risoluzione di **Stato di monitoraggio**.

| Stato di monitoraggio | Descrizione | Procedura per la risoluzione |
|---|---|---|
| Installazione dell'agente in sospeso | L'installazione di Microsoft Monitoring Agent è ancora in esecuzione.  L'installazione può richiedere alcune ore. | Attendere il completamento dell'installazione automatica. |
| Stato dell'alimentazione disattivato | La macchina virtuale è stata arrestata.  Microsoft Monitoring Agent può essere installato solo su una VM in esecuzione. | Riavviare la VM. |
| L'agente di macchine virtuali di Azure è mancante o non valido | Microsoft Monitoring Agent non è stato ancora installato.  Un agente di macchine virtuali di Azure è necessario per l'installazione dell'estensione da parte del Centro sicurezza. | Installare, reinstallare o aggiornare l'agente di macchine virtuali di Azure sulla VM. |
| Lo stato della VM non è pronto per l'installazione  | Microsoft Monitoring Agent non è ancora installato perché la VM non è pronta per l'installazione. La macchina virtuale non è pronta per l'installazione a causa di un problema con l'agente di macchine virtuali o con il provisioning della VM. | Controllare lo stato della VM. Tornare a **Macchine virtuali** nel portale e selezionare la VM per ottenere informazioni sullo stato. |
|L'installazione non è riuscita - Errore generale | Microsoft Monitoring Agent è stato installato ma l'operazione non è riuscita a causa di un errore. | [Installare manualmente l'estensione](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) o disinstallare l'estensione in modo che il Centro sicurezza possa provare a reinstallarla. |
| L'installazione non è riuscita - L'agente locale è già installato | Non è stato possibile installare Microsoft Monitoring Agent. Il Centro sicurezza ha identificato un agente locale (Log Analytics o System Center Operations Manager) già installato nella macchina virtuale. Per evitare una configurazione multihosting, in cui la VM invia report a due aree di lavoro separate, l'installazione di Microsoft Monitoring Agent è stata arrestata. | È possibile risolvere il problema in due modi: [installare manualmente l'estensione](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) e connetterla all'area di lavoro specifica. In alternativa, configurare l'area di lavoro da usare come area di lavoro predefinita e abilitare il provisioning automatico dell'agente.  Vedere [Abilitare il provisioning automatico](security-center-enable-data-collection.md). |
| L'agente non riesce a connettersi all'area di lavoro | Microsoft Monitoring Agent è stato installato ma l'operazione non è riuscita a causa di un errore di connettività di rete.  Verificare che sia disponibile l'accesso a Internet o che un proxy HTTP corretto sia stato configurato per l'agente. | Vedere Requisiti di rete di Monitoring Agent. |
| L'agente è connesso a un'area di lavoro mancante o sconosciuta | Il Centro sicurezza ha identificato che l'agente di monitoraggio Microsoft installato nella macchina virtuale è connesso a un'area di lavoro a cui non ha accesso. | Questo problema può dipendere da due cause. L'area di lavoro è stata eliminata e non esiste più. Reinstallare l'agente con l'area di lavoro corretta o disinstallare l'agente e consentire al Centro sicurezza di completare l'installazione del provisioning automatico. In alternativa è possibile che l'area di lavoro sia parte di una sottoscrizione a cui il Centro sicurezza non è autorizzato ad accedere. Il Centro sicurezza necessita che le sottoscrizioni consentano l'accesso al provider di risorse per la sicurezza Microsoft. Per abilitare l'accesso, registrare la sottoscrizione nel provider di risorse per la sicurezza Microsoft. È possibile eseguire questa operazione tramite API, PowerShell, portale o semplicemente filtrando la sottoscrizione nel dashboard **Panoramica** del Centro sicurezza. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| L'agente non risponde o l'ID è mancante | Il Centro sicurezza non riesce a recuperare i dati di sicurezza analizzati dalla macchina virtuale, anche se l'agente è installato. | L'agente non segnala alcun dato, incluso l'heartbeat. È possibile che l'agente sia danneggiato o che il traffico sia bloccato. In alternativa, l'agente segnala i dati ma manca un ID risorsa di Azure, pertanto è impossibile associare i dati alla macchina virtuale di Azure.Or, the agent is reporting data but is missing an Azure resource ID so it's impossible to match the data to the Azure VM. Per risolvere i problemi relativi a Linux, vedere [Troubleshooting Guide for Log Analytics Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal) (Guida alla risoluzione dei problemi per l'agente di Log Analytics per Linux). Per risolvere i problemi relativi a Windows, vedere[Troubleshooting Windows Virtual Machines](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines) (Risoluzione dei problemi delle macchine virtuali Windows). |
| Agente non installato | La raccolta di dati è disabilitata. | Attivare la raccolta di dati nel criterio di sicurezza o installare manualmente Microsoft Monitoring Agent. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Risoluzione dei problemi relativi ai requisiti di rete degli agenti di monitoraggioTroubleshooting monitoring agent<a name="mon-network-req"></a>

Per far sì che gli agenti si connettano e si registrino con il Centro sicurezza, devono avere accesso alle risorse di rete, compresi gli URL di dominio e i numeri di porta.

* Per i server proxy, è necessario assicurarsi che le risorse del server proxy appropriate siano configurate nelle impostazioni dell'agente. Leggere questo articolo per altre informazioni su [come modificare le impostazioni del proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
* Per i firewall che limitano l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso a Log Analytics. Non è necessaria alcuna azione sulle impostazioni dell'agente.

Nella tabella seguente vengono visualizzate le risorse necessarie per la comunicazione.

| Risorsa agente | Porte | Ignorare l'analisi HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sì |
| *.oms.opinsights.azure.com | 443 | Sì |
| *.blob.core.windows.net | 443 | Sì |
| *.azure-automation.net | 443 | Sì |

Se si verificano problemi di caricamento con l'agente, assicurarsi di leggere l'articolo [Risoluzione dei problemi di integrazione di Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Risoluzione dei problemi relativi al mancato funzionamento della protezione degli endpoint

L'agente guest è il processo padre di tutte le operazioni eseguite dall'estensione [Microsoft Antimalware](../security/fundamentals/antimalware.md). Quando il processo dell'agente guest non riesce, anche Microsoft Antimalware che viene eseguito come processo figlio dell'agente guest potrebbe non riuscire.  In scenari come questi è consigliabile verificare le opzioni seguenti:

* Che la macchina virtuale di destinazione sia un'immagine personalizzata e che l'autore della macchina virtuale non abbia mai installato l'agente guest.
* Che la destinazione sia una VM di Linux e non una VM di Windows per cui l'installazione della versione di Windows dell'estensione antimalware su una VM Linux avrebbe esito negativo. L'agente guest Linux presenta requisiti specifici per quanto riguarda la versione del sistema operativo e i pacchetti necessari e se questi requisiti non vengono soddisfatti l'agente della VM non funzionerà.
* Che la VM sia stata creata con una versione precedente dell'agente guest. In questo caso è necessario tenere presente che alcuni agenti precedenti potrebbero non aggiornarsi automaticamente alla versione più recente e questo potrebbe causare il problema. Usare sempre la versione più recente dell'agente guest quando si creano le immagini personali.
* Alcuni software di amministrazione di terze parti possono disabilitare l'agente guest o bloccare l'accesso a determinati percorsi di file. Se sulla VM sono installati software terzi, assicurarsi che l'agente si trovi nell'elenco di esclusione.
* Alcune impostazioni del firewall o il gruppo di sicurezza di rete (NSG) potrebbero bloccare il traffico di rete da e verso l'agente guest.
* Determinati elenchi di controllo di accesso (ACL) potrebbero impedire l'accesso al disco.
* La mancanza di spazio su disco può bloccare il corretto funzionamento dell'agente guest.

Per impostazione predefinita l'interfaccia utente di Microsoft Antimalware è disabilitata, leggere [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Abilitazione dell'interfaccia utente di Microsoft Antimalware in seguito alla distribuzione delle VM in Azure Resource Manager) per ulteriori informazioni su come abilitarla in caso di necessità.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Risoluzione dei problemi di caricamento del dashboard

In caso di problemi di caricamento del dashboard del Centro sicurezza, assicurarsi che l'utente che registra la sottoscrizione al Centro sicurezza, vale a dire il primo utente che apre il Centro sicurezza con la sottoscrizione, e l'utente che vuole abilitare la raccolta dei dati siano *Proprietario* o *Collaboratore* nella sottoscrizione. A partire da quel momento, anche gli utenti con il ruolo *Lettore* nella sottoscrizione possono visualizzare i dashboard, gli avvisi, le raccomandazioni e i criteri.

## <a name="contacting-microsoft-support"></a>Contattare il supporto tecnico Microsoft

Alcuni problemi possono essere identificati usando le linee guida contenute in questo articolo, altri sono documentati nel [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)pubblico del Centro sicurezza. Tuttavia, se è necessaria un'ulteriore risoluzione dei problemi, è possibile aprire una nuova richiesta di supporto usando il portale di Azure come illustrato di seguito:However if you need further troubleshooting, you can open a new support request using **Azure portal** as shown below:

![Supporto tecnico Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Vedere anche

In questo documento è stato descritto come configurare i criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md) : informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
* [Monitoraggio dell'integrità](security-center-monitoring.md) della sicurezza nel Centro sicurezza di Azure: informazioni su come monitorare l'integrità delle risorse di AzureSecurity health monitoring in Azure Security Center - Learn how to monitor the health of your Azure resources
* [Gestione e risposta agli avvisi](security-center-managing-and-responding-alerts.md) di sicurezza nel Centro sicurezza di Azure: informazioni su come gestire e rispondere agli avvisi di sicurezzaManaging and responding to security alerts in Azure Security Center - Learn how to manage and respond to security alerts
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di AzureUnderstanding security alerts in Azure Security Center](security-center-alerts-type.md)
* [Tutorial: Respond to security incidents](tutorial-security-incident.md) (Esercitazione: Rispondere agli eventi imprevisti della sicurezza)
* [Convalida degli avvisi nel Centro sicurezza di Azure](security-center-alert-validation.md)
* [Notifiche tramite messaggio di posta elettronica nel Centro sicurezza di Azure](security-center-provide-security-contact-details.md)
* [Gestione degli eventi imprevisti della sicurezza nel Centro sicurezza di Azure](security-center-incident.md)
* [Analizzare gli eventi imprevisti e gli avvisi nel Centro sicurezza di Azure](security-center-investigation.md)
* [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md)
* [Monitoraggio delle soluzioni dei partner con](security-center-partner-solutions.md) il Centro sicurezza di Azure: informazioni su come monitorare lo stato di integrità delle soluzioni partner.
* Domande frequenti sul [Centro sicurezza di Azure:](faq-general.md) domande frequenti sull'uso del servizioAzure Security Center FAQ - Find frequently asked questions about using the service
* Blog sulla [sicurezza di Azure:](https://blogs.msdn.com/b/azuresecurity/) trova post di blog su sicurezza e conformità di AzureAzure Security Blog - Find blog posts about Azure security and compliance
