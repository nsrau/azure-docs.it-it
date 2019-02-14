---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/3/2019
ms.author: rkarlin
ms.openlocfilehash: 52b347aef240f2338aedff2c2a1a3cd53743dc27
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114264"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per le soluzioni come quelle antimalware e di valutazione delle vulnerabilità, il Centro sicurezza può effettuare il provisioning dell'agente necessario nelle macchine virtuali, mentre per gli appliance firewall il Centro sicurezza può occuparsi di gran parte delle attività di configurazione di rete necessarie.
- **Rilevamenti integrati**: Gli eventi di sicurezza delle soluzioni partner vengono raccolti, aggregati e visualizzati automaticamente nell'ambito degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: I clienti possono usare gli eventi di integrità integrati per monitorare in modo immediato tutte le soluzioni partner. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente le soluzioni di sicurezza integrate includono:

- Web application firewall ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) e [gateway applicazione di Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall di nuova generazione ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) e [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Valutazione delle vulnerabilità ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) e [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> Centro sicurezza non installa Microsoft Monitoring Agent nelle appliance virtuali dei partner perché la maggior parte dei fornitori di soluzioni di sicurezza non consente che vengano eseguiti agenti esterni nelle proprie appliance.
>
>

## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. È anche possibile connettere altre origini dati di sicurezza, tra cui:

- Azure AD Identity Protection
- Computer in esecuzione in locale o in altri cloud
- Soluzione di sicurezza che supporta Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

3. Selezionare **Soluzioni di sicurezza** nel menu del Centro sicurezza.

  ![Panoramica del Centro sicurezza](./media/security-center-partner-integration/overview.png)

In **Soluzioni di sicurezza** è possibile visualizzare informazioni sull'integrità delle soluzioni di sicurezza integrate di Azure ed eseguire attività di gestione di base. È anche possibile connettere altri tipi di origini dati di sicurezza, ad esempio log dei firewall e avvisi di protezione di Azure Active Directory Identity Protection nel formato CEF (Common Event Format).

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** include le soluzioni di sicurezza attualmente connesse al Centro sicurezza e informazioni sullo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Una soluzione partner può avere uno degli stati seguenti:

* Integro (verde): non ci sono problemi di integrità.
* Danneggiato (rosso): è presente un problema di integrità che richiede attenzione immediata.
* Problemi di integrità (arancione): la soluzione non segnala più lo stato di integrità.
* Segnalazione non inviata (grigio): la soluzione non ha ancora inviato alcuna segnalazione. Lo stato di una soluzione può non essere segnalato se la soluzione è stata connessa recentemente ed è ancora in fase di distribuzione oppure se non sono disponibili dati relativi all'integrità.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati relativi all'integrità e non sono stati ricevuti avvisi negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non invia segnalazioni.
>
>

2. Selezionare **VISUALIZZA** per altre informazioni e opzioni, tra cui:

  - **Console della soluzione**. Apre l'esperienza di gestione per questa soluzione.
  - **Collega la macchina virtuale**. Apre il pannello Collega applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
  - **Elimina soluzione**.
  - **Configura**.

   ![Dettagli della soluzione di un partner](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure che non sono tuttavia connesse al Centro sicurezza e visualizza le soluzioni nella sezione **Soluzioni individuate**. Sono incluse le soluzioni di Azure, ad esempio [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), e le soluzioni dei partner.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

Selezionare **CONNETTI** in una soluzione per integrarla con il Centro sicurezza e ricevere avvisi di sicurezza.

![Discovered solutions (Soluzioni individuate)](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Il Centro sicurezza individua anche le soluzioni distribuite nella sottoscrizione che sono in grado di inoltrare log in formato CEF (Common Event Format). Vedere come [connettere al Centro sicurezza una soluzione di sicurezza](quick-security-solutions.md) che usa i log CEF.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

### <a name="connect-external-solutions"></a>Connettere soluzioni esterne

Oltre a raccogliere dati di sicurezza dai computer, è possibile integrare dati di sicurezza da una serie di altre soluzioni di sicurezza, incluse quelle che supportano Common Event Format (CEF). Common Event Format (CEF) è un formato standard di settore applicato ai messaggi Syslog, usato da molti fornitori di soluzioni per la sicurezza per consentire l'integrazione degli eventi tra diverse piattaforme.

Questa guida introduttiva illustra come effettuare le operazioni seguenti:
- Connettere una soluzione di sicurezza al Centro sicurezza usando i log di CEF
- Convalidare la connessione alla soluzione di sicurezza

#### <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

Per usare questa guida introduttiva è necessario il piano tariffario Standard del Centro sicurezza. È possibile provare il livello Standard del Centro sicurezza gratuitamente. La [Guida introduttiva per il Centro sicurezza di Azure ](security-center-get-started.md) illustra come eseguire l'aggiornamento al livello Standard. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

È anche necessario un [computer Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux) con il servizio Syslog già connesso al Centro sicurezza.

#### <a name="connect-solution-using-cef"></a>Connettere la soluzione usando CEF

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).
2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

    ![Selezionare Centro sicurezza](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. Selezionare **Soluzioni di sicurezza** nel menu principale del Centro sicurezza.
4. In **Aggiungi origini dati (3)** nella pagina Soluzioni di sicurezza, fare clic su **Aggiungi** nell'area **Common Event Format**.

    ![Aggiungere l'origine dati](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. Nella pagina Log Common Event Format espandere il secondo passaggio, ovvero **Configurare l'inoltro Syslog per inviare i log necessari all'agente sulla porta UDP 25226**, e seguire queste istruzioni nel computer Linux:

    ![Configurare Syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. Espandere il terzo passaggio, ovvero **Salvare il file di configurazione dell'agente nel computer dell'agente**, e seguire queste istruzioni nel computer Linux:

    ![Configurazione dell'agente](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. Espandere il quarto passaggio, ovvero **Riavvia il daemon syslog e l'agente**, e seguire queste istruzioni nel computer Linux:

    ![Riavviare il Syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


#### <a name="validate-the-connection"></a>Convalidare la connessione

Prima di procedere con i passaggi seguenti è necessario attendere che il Syslog inizi a inviare report al Centro sicurezza. Questa operazione può richiedere del tempo, in funzione delle dimensioni dell'ambiente.

1.  Nel riquadro sinistro del dashboard del Centro sicurezza fare clic su **Cerca**.
2.  Selezionare l'area di lavoro alla quale è connesso Syslog (computer Linux).
3.  Digitare *CommonSecurityLog* e fare clic sul pulsante **Cerca**.

L'esempio seguente mostra il risultato di questi passaggi: ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

#### <a name="clean-up-resources"></a>Pulire le risorse
Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, continuare a eseguire il livello Standard e tenere abilitato il provisioning automatico. Se non si intende proseguire oppure si vuole tornare al livello gratuito:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione o i criteri per i quali si vuole tornare al livello gratuito. Viene visualizzata la finestra **Criteri di sicurezza**.
3. In **COMPONENTI DEI CRITERI** selezionare **Piano tariffario**.
4. Selezionare **Gratuito** per modificare il livello della sottoscrizione da Standard a Gratuito.
5. Selezionare **Salva**.

Se si vuole disabilitare il provisioning automatico:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico.
3. In **Criteri di sicurezza - Raccolta dati** selezionare **No** in **Onboarding** per disabilitare il provisioning automatico.
4. Selezionare **Salva**.

>[!NOTE]
> La disabilitazione del provisioning automatico non rimuove Microsoft Monitoring Agent dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.
>

## <a name="exporting-data-to-a-siem"></a>Esportazione dei dati in un sistema di informazioni di sicurezza e gestione degli eventi

Gli eventi elaborati prodotti dal Centro sicurezza di Azure vengono pubblicati nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) di Azure, uno dei tipi di log disponibili tramite Monitoraggio di Azure. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Questa operazione viene eseguita trasmettendo i dati in un hub eventi in cui è possibile effettuarne il pull in uno strumento di partner.

Questa pipe usa la [singola pipeline di Monitoraggio di Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) per l'accesso ai dati di monitoraggio dall'ambiente Azure. Questo consente di configurare facilmente i SIEM e di monitorare gli strumenti per utilizzare i dati.

Le sezioni seguenti spiegano come configurare i dati per poterli trasmettere a un hub eventi. In questi passaggi si presuppone che l'utente abbia già configurato Centro sicurezza di Azure nella sottoscrizione Azure.

Panoramica generale

![Panoramica generale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quali dati della sicurezza di Azure vengono esposti a SIEM?

In questa versione vengono esposti gli [avvisi di sicurezza](../security-center/security-center-managing-and-responding-alerts.md). Nelle prossime versioni il set di dati verrà ampliato con le raccomandazioni sulla sicurezza.

### <a name="how-to-setup-the-pipeline"></a>Come configurare la pipeline

#### <a name="create-an-event-hub"></a>Creare un hub eventi

Prima di iniziare, è necessario [creare uno spazio dei nomi di Hub eventi](../event-hubs/event-hubs-create.md). Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi

Fare riferimento all'articolo [Trasmettere il log attività a Hub eventi](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Installare un connettore SIEM partner 

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner.

Fare riferimento al collegamento seguente per vedere l'elenco di [SIEM supportati](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Esempio di query sui dati 

Di seguito sono riportati due query Splunk che è possibile usare per eseguire il pull dei dati di avviso:

| **Descrizione della query** | **Query** |
|----|----|
| Tutti gli avvisi| index=main Microsoft.Security/locations/alerts|
| Riepilogare il numero di operazioni in base al nome| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Ottenere informazioni sugli avvisi: ora, nome, stato, ID e sottoscrizione | index=main Microsoft.Security/locations/alerts \| tabella \_time, properties.eventName, stato, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza di Azure)](security-center-ata-integration.md)
* [Connessione di Azure Active Directory Identity Protection al Centro sicurezza di Azure](security-center-aadip-integration.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
