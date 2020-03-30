---
title: Connettere Operations Manager ad Monitoraggio di Azure . Documenti Microsoft
description: Per gestire l'investimento esistente in System Center Operations Manager e usare le funzionalità estese con Log Analytics, è possibile integrare Operations Manager con l'area di lavoro.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274346"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Connettere Operations Manager ad Monitoraggio di AzureConnect Operations Manager to Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Per mantenere l'investimento esistente in [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) e usare le funzionalità estese con Monitoraggio di Azure, è possibile integrare Operations Manager con l'area di lavoro di Log Analytics. Ciò consente di sfruttare le opportunità di log in Monitoraggio di Azure continuando a usare Operations Manager per:This allows you to leverage the opportunities of logs in Azure Monitor while continuing to use Operations Manager to:

* Monitorare l'integrità dei servizi IT con Operations Manager
* Gestire l'integrazione con le soluzioni ITSM che supportano la gestione di eventi imprevisti e problemi
* Gestire il ciclo di vita degli agenti distribuiti in macchine virtuali IaaS locali e nel cloud pubblico monitorate con Operations Manager

L'integrazione con System Center Operations Manager aggiunge valore alla strategia per le operazioni del servizio utilizzando la velocità e l'efficienza di Monitoraggio di Azure nella raccolta, archiviazione e analisi dei dati di log da Operations Manager. Le query di log di Monitoraggio di Azure consentono di correlare e lavorare per identificare gli errori dei problemi e le ricorrenze ricorrenze a supporto del processo di gestione dei problemi esistente. La flessibilità del motore di query per esaminare i dati relativi a prestazioni, eventi e avvisi, con dashboard avanzati e funzionalità di creazione di report per esporre questi dati in modo significativo, dimostra la forza che Monitoraggio Azure comporta per completare Operations Manager.

Gli agenti che segnalano al gruppo di gestione operations Manager raccolgono dati dai server in base alle origini dati e alle soluzioni di [Log Analytics](agent-data-sources.md) abilitate nell'area di lavoro. A seconda delle soluzioni abilitate, i dati vengono inviati direttamente da un server di gestione di Operations Manager al servizio oppure, a causa del volume di dati raccolti nel sistema gestito tramite agente, vengono inviati direttamente dall'agente a Log workspace Analytics. Il server di gestione inoltra direttamente i dati al servizio. I dati non vengono mai scritti nel database operativo o nel database del data warehouse. Quando un server di gestione perde la connettività con Monitoraggio di Azure, memorizza i dati nella cache in locale fino a quando non viene ristabilita la comunicazione. Se il server di gestione è offline a causa di manutenzione pianificata o interruzione non pianificata, un altro server di gestione nel gruppo di gestione riprende la connettività con Monitoraggio di Azure.If the management server is offline due to planned maintenance or unplanned outage, another management server in the management group resumes connectivity with Azure Monitor.  

Nel diagramma seguente viene illustrata la connessione tra i server di gestione e gli agenti in un gruppo di gestione di System Center Operations Manager e Monitoraggio di Azure, incluse la direzione e le porte.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se i criteri di sicurezza IT non consentono ai computer nella rete di connettersi a Internet, i server di gestione possono essere configurati per la connessione al gateway Log Analytics per poter ricevere le informazioni di configurazione e inviare i dati raccolti a seconda della soluzione abilitata. Per altre informazioni e procedure su come configurare il gruppo di gestione di Operations Manager per comunicare tramite un gateway di Log Analytics a Monitoraggio di Azure, vedere [Connettere i computer a Monitoraggio di Azure usando il gateway di Log Analytics.](../../azure-monitor/platform/gateway.md)  

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, esaminare i requisiti seguenti.

* Monitoraggio di Azure supporta solo System Center Operations Manager 2016 o versione successiva, Operations Manager 2012 SP1 UR6 o versione successiva e Operations Manager 2012 R2 UR2 o versione successiva. Il supporto per il proxy è stato aggiunto in Operations Manager 2012 SP1 UR7 e Operations Manager 2012 R2 UR3.
* L'integrazione di System Center Operations Manager 2016 con il cloud del governo degli Stati Uniti richiede un Management Pack Advisor aggiornato incluso con l'aggiornamento cumulativo 2 o versione successiva. System Center Operations Manager 2012 R2 richiede un Management Pack dell'advisor aggiornato incluso con l'aggiornamento cumulativo 3 o versione successiva.
* Tutti gli agenti di Operations Manager devono soddisfare i requisiti di supporto minimo. Verificare che gli agenti dispongano dell'aggiornamento minimo richiesto, altrimenti le comunicazioni degli agenti di Windows possono avere esito negativo e generare errori nel log eventi di Operations Manager.
* Un'area di lavoro Log Analytics. Per altre informazioni, vedere [Panoramica delle aree di lavoro Log Analytics](design-logs-deployment.md). 
* Autenticazione in Azure con un account membro del [ruolo di collaboratore di Log Analytics](manage-access.md#manage-access-using-azure-permissions).

* Aree supportate: solo le aree di Azure seguenti sono supportate da System Center Operations Manager per la connessione a un'area di lavoro di Log Analytics:Supported Regions - Only the following Azure regions are supported by System Center Operations Manager to connect to a Log Analytics workspace:
    - Stati Uniti centro-occidentali
    - Australia sud-orientale
    - Europa occidentale
    - Stati Uniti orientali
    - Asia sud-orientale
    - Giappone orientale
    - Regno Unito meridionale
    - India centrale
    - Canada centrale
    - Stati Uniti occidentali 2

>[!NOTE]
>Le modifiche recenti alle API di Azure impediranno ai clienti di configurare correttamente l'integrazione tra il gruppo di gestione e Monitoraggio di Azure per la prima volta. I clienti che hanno già integrato il proprio gruppo di gestione con il servizio non sono interessati da queste modifiche, a meno che non debbano riconfigurare la connessione esistente.  
>Per le versioni seguenti di Operations Manager è stato rilasciato un nuovo Management Pack:
> - Per System Center Operations Manager 2019, questo Management Pack è incluso nel supporto di origine e installato durante l'installazione di un nuovo gruppo di gestione o durante un aggiornamento.
>- Il Management Pack di Operations Manager 1801 è applicabile anche a Operations Manager 1807.
>- Per System Center Operations Manager 1801, scaricare il Management Pack da [qui](https://www.microsoft.com/download/details.aspx?id=57173).
>- Per System Center 2016 - Operations Manager, scaricare il Management Pack da [qui](https://www.microsoft.com/download/details.aspx?id=57172).  
>- Per System Center Operations Manager 2012 R2, scaricare il Management Pack da [qui](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Rete

The information below list the proxy and firewall configuration information required for the Operations Manager agent, management servers, and Operations console to communicate with Azure Monitor. Il traffico proveniente da ogni componente è in uscita dalla rete ad Azure Monitor.Traffic from each component is outbound from your network to Azure Monitor.

|Risorsa | Numero della porta| Ignorare l'analisi HTTPS|  
|---------|------|-----------------------|  
|**Agente**|||  
|\*.ods.opinsights.azure.com| 443 |Sì|  
|\*.oms.opinsights.azure.com| 443|Sì|  
|\*.blob.core.windows.net| 443|Sì|  
|\*.azure-automation.net| 443|Sì|  
|**Server di gestione**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Sì|  
|\*.ods.opinsights.azure.com| 443| Sì|  
|*.azure-automation.net | 443| Sì|  
|**Console di Operations Manager per Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 e 443||  
|\*.microsoft.com| 80 e 443||  
|\*.microsoftonline.com| 80 e 443||  
|\*.mms.microsoft.com| 80 e 443||  
|login.windows.net| 80 e 443||  
|portal.loganalytics.io| 80 e 443||
|api.loganalytics.io| 80 e 443||
|docs.loganalytics.io| 80 e 443||  

### <a name="tls-12-protocol"></a>Protocollo TLS 1.2

Per garantire la sicurezza dei dati in transito in Monitoraggio di Azure, è consigliabile configurare l'agente e il gruppo di gestione per l'utilizzo almeno di Transport Layer Security (TLS) 1.2.To sure the security of data in transit to Azure Monitor, we strongly incoraggiato to configure the agent and management group to use at least Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**. Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Connessione di Operations Manager ad Monitoraggio di AzureConnecting Operations Manager to Azure Monitor

Eseguire questa serie di passaggi per configurare il gruppo di gestione di Operations Manager per connettersi a una delle aree di lavoro di Log Analytics.

Durante la registrazione iniziale del gruppo di gestione di Operations Manager con un'area di lavoro Log Analytics, l'opzione per specificare la configurazione proxy per il gruppo di gestione non è disponibile nella console operatore.  Il gruppo di gestione deve essere registrato correttamente con il servizio prima che questa opzione sia disponibile.  Per risolvere questo problema, è necessario aggiornare la configurazione del proxy di sistema tramite Netsh nel sistema da cui si esegue la console operatore per configurare l'integrazione e tutti i server di gestione nel gruppo di gestione.  

1. Aprire un prompt dei comandi con privilegi elevati.
   a. Passare a **Start** e digitare **cmd**.
   b. Fare clic con il pulsante destro del mouse su **Prompt dei comandi** e selezionare Esegui come amministratore.
1. Immettere il comando seguente e premere **INVIO**:

    `netsh winhttp set proxy <proxy>:<port>`

Dopo aver completato i passaggi seguenti per l'integrazione `netsh winhttp reset proxy` con Monitoraggio di Azure, è possibile rimuovere la configurazione eseguendo e quindi usare l'opzione Configura server proxy nella Console operatore per specificare il proxy o il server gateway di Log Analytics.After completing the following steps to integrate with Azure Monitor, you can remove the configuration by running and then use the **Configure proxy server** option in the Operations console to specify the proxy or Log Analytics gateway server.

1. Nella console di Operations Manager selezionare l'area di lavoro **Amministrazione** .
1. Espandere il nodo Operations Management Suite e fare clic su **Connessione**.
1. Fare clic sul collegamento **Registrazione a Operations Management Suite** .
1. Nella pagina**Caricamento guidato di Operations Management Suite: Autenticazione**, immettere l'indirizzo di posta elettronica o il numero di telefono e la password dell'account amministratore associato alla sottoscrizione OMS e fare clic su **Accedi**.

   >[!NOTE]
   >Il nome di Operations Management Suite è stato ritirato.

1. Al termine dell'autenticazione, nella pagina **Operations Management Suite Onboarding Wizard: Select Workspace** (Caricamento guidato di Operations Management Suite: selezione area di lavoro) verrà chiesto di selezionare il tenant e la sottoscrizione di Azure e l'area di lavoro Log Analytics. Se si ha più di un'area di lavoro, selezionare l'area di lavoro che si vuole registrare con il gruppo di gestione di Operations Manager nell'elenco a discesa e quindi fare clic su **Avanti**.

   > [!NOTE]
   > Operations Manager supporta solo un'area di lavoro Log Analytics alla volta. The connection and the computers that were registered to Azure Monitor with the previous workspace are removed from Azure Monitor.
   >
   >
1. Nella pagina **Caricamento guidato di Operations Management Suite: Riepilogo** confermare le impostazioni e, se sono corrette, fare clic su **Crea**.
1. Nella pagina **Caricamento guidato di Operations Management Suite: Fine** fare clic su **Chiudi**.

### <a name="add-agent-managed-computers"></a>Aggiungere computer gestiti dagli agenti

Dopo aver configurato l'integrazione con l'area di lavoro Log Analytics, viene stabilita solo una connessione con il servizio, ma nessun dato viene raccolto dagli agenti che fanno riferimento al gruppo di gestione. Ciò si verifica solo dopo aver configurato i computer gestiti tramite agente specifici che raccolgono i dati di log per Monitoraggio di Azure.This won't happen until after you configure which specific agent-managed computers collect log data for Azure Monitor. È possibile selezionare gli oggetti computer singolarmente o selezionare un gruppo contenente gli oggetti computer Windows. Non è possibile selezionare un gruppo contenente istanze di un'altra classe, ad esempio dischi logici o database SQL.

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Amministrazione**.
1. Espandere il nodo Operations Management Suite e fare clic su **Connessione**.
1. Fare clic sul collegamento **Aggiungi computer/gruppo** nell'intestazione Azioni sul lato destro del riquadro.
1. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi, incluso il server di gestione di Operations Manager per l'onboarding ad Azure Monitor, fare clic su **Aggiungi**e quindi su **OK**.

È possibile visualizzare i computer e i gruppi configurati per raccogliere dati dal nodo Computer gestiti in Operations Management Suite nell'area di lavoro **Amministrazione** della console operatore. Da qui è possibile aggiungere o rimuovere i computer e i gruppi in base alle esigenze.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configurare le impostazioni del proxy nella Console operatore

Eseguire la procedura seguente se un server proxy interno è compreso tra il gruppo di gestione e Monitoraggio di Azure.Perform the following steps if an internal proxy server is between the management group and Azure Monitor. Queste impostazioni vengono gestite centralmente dal gruppo di gestione e distribuite ai sistemi gestiti tramite agente inclusi nell'ambito per raccogliere i dati di log per Monitoraggio di Azure.These settings are centrally managed from the management group and distributed to agent-managed systems that are included in the scope to collect log data for Azure Monitor.  Questo è un vantaggio nel caso di alcune soluzioni che ignorano il server di gestione e inviano i dati direttamente al servizio.

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Amministrazione**.
1. Espandere Operations Management Suite e quindi fare clic su **Connessioni**.
1. Nella visualizzazione OMS Connection (Connessione OMS), fare clic su **Configure Proxy Server**(Configura server proxy).
1. Nella pagina **Configurazione guidata impostazioni di Operations Management Suite: Server proxy** selezionare **Usa un server proxy per accedere a Operations Management Suite** e immettere l'URL con il numero di porta, ad esempio http://corpproxy:80, quindi fare clic su **Fine**.

Se il server proxy richiede l'autenticazione, eseguire la procedura seguente per configurare le credenziali e le impostazioni che devono essere propagate ai computer gestiti che segnalano Azure Monitor nel gruppo di gestione.

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Amministrazione**.
1. In **RunAs Configuration** selezionare **Profiles**.
1. Aprire il profilo **Proxy del profilo RunAs di System Center Advisor** .
1. In Creazione guidata profilo RunAs fare clic su Aggiungi per usare un account RunAs. È possibile creare un [account RunAs](https://technet.microsoft.com/library/hh321655.aspx) oppure usare un account esistente. L'account deve avere autorizzazioni sufficienti per passare attraverso il server proxy.
1. Per impostare l'account da gestire, scegliere **Classe, gruppo o oggetto selezionato**, fare clic su **Seleziona...** e su **Gruppo...** per aprire la finestra **Ricerca gruppi**.
1. Cercare e quindi selezionare **Gruppo di server di monitoraggio di Microsoft System Center Advisor**. Fare clic su **OK** dopo avere selezionato il gruppo per chiudere la finestra **Ricerca gruppi**.
1. Fare clic su **OK** per chiudere la finestra **Aggiungi account RunAs**.
1. Fare clic su **Salva** per completare la procedura guidata e salvare le modifiche.

Dopo aver creato la connessione e aver configurato gli agenti che raccoglieranno e segnaleranno i dati del log in Monitoraggio di Azure, nel gruppo di gestione viene applicata la configurazione seguente, non necessariamente nell'ordine:After the connection is created and you configure which agents will collect and report log data to Azure Monitor, the following configuration is applied in the management group, not necessarily in order:

* Viene creato l'account RunAs **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**, che viene associato al profilo RunAs **Microsoft System Center Advisor eseguito come profilo BLOB** e che specifica come destinazione due classi, **Server raccolta** e **Gruppo di gestione di Operations Manager**.
* Vengono creati due connettori.  Il primo è denominato Microsoft.SystemCenter.Advisor.DataConnector e viene configurato automaticamente con una sottoscrizione che inoltra tutti gli avvisi generati da istanze di tutte le classi nel gruppo di gestione a Monitoraggio di Azure.The first is named **Microsoft.SystemCenter.Advisor.DataConnector** and is automatically configured with a subscription that forwards all alerts generated from instances of all classes in the management group to Azure Monitor. Il secondo connettore è **Advisor Connector**, responsabile della comunicazione con Monitoraggio di Azure e della condivisione dei dati.
* Gli agenti e i gruppi selezionati per raccogliere i dati nel gruppo di gestione vengono aggiunti al **Gruppo di server di monitoraggio di Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aggiornamenti di Management Pack

Al termine della configurazione, il gruppo di gestione di Operations Manager stabilisce una connessione con Monitoraggio di Azure.After configuration is completed, the Operations Manager management group establishes a connection with Azure Monitor. Il server di gestione si sincronizza con il servizio Web e riceve le informazioni di configurazione aggiornate sotto forma di Management Pack per le soluzioni abilitate che si integrano con Operations Manager. Operations Manager cerca gli aggiornamenti per questi Management Pack e, se disponibili, li scarica e li importa automaticamente. Esistono due regole in particolare che controllano questo comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate:** aggiorna i Management Pack di Base di Monitoraggio di Azure. Per impostazione predefinita, viene eseguita ogni 12 ore.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : aggiorna i Management Pack delle soluzioni abilitati nell'area di lavoro. Per impostazione predefinita, viene eseguita ogni cinque (5) minuti.

È possibile eseguire l'override di queste due regole per impedire il download automatico disabilitandoli o modificare la frequenza con cui il server di gestione si sincronizza con Monitoraggio di Azure per determinare se un nuovo Management Pack è disponibile e deve essere scaricato. Seguire i passaggi in [Come eseguire l'override di una regola o di un'attività di monitoraggio](https://technet.microsoft.com/library/hh212869.aspx) per modificare il parametro **Frequenza** con un valore espresso in secondi per cambiare la pianificazione della sincronizzazione oppure per modificare il parametro **Abilitata** per disabilitare le regole. Specificare come destinazione gli override di tutti gli oggetti della classe Gruppo di gestione di Operations Manager.

Per continuare a seguire il processo di controllo delle modifiche esistente per controllare le versioni dei Management Pack nel gruppo di gestione di produzione, è possibile disabilitare le regole e abilitarle in orari specifici in cui gli aggiornamenti sono consentiti. Se nell'ambiente è presente un gruppo di sviluppo o di gestione del controllo di qualità con connettività a Internet, è possibile configurare tale gruppo di gestione con un'area di lavoro Log Analytics per supportare questo scenario. In questo modo è possibile esaminare e valutare le versioni iterative dei Management Pack di Monitoraggio di Azure prima di rilasciarli nel gruppo di gestione della produzione.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Passare un gruppo di Operations Manager a una nuova area di lavoro Log Analytics

1. Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .
1. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics** e quindi creare un'area di lavoro.  
1. Aprire la console di Operations Manager con un account membro del ruolo Amministratori di Operations Manager e selezionare l’area di lavoro **Amministrazione** .
1. Espandere Log Analytics e selezionare **Connessioni**.
1. Selezionare il collegamento **Riconfigura Operation Management Suite** nella parte centrale del riquadro.
1. Seguire le indicazioni in **Caricamento guidato di Operations Management Suite** e immettere l'indirizzo di posta elettronica o il numero di telefono e la password dell'account amministratore associato alla nuova area di lavoro Log Analytics.

   > [!NOTE]
   > La pagina **Caricamento guidato di Operations Management Suite: Selezione area di lavoro** presenta l'area di lavoro esistente in uso.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Convalidare l'integrazione di Operations Manager con Monitoraggio di AzureValidate Operations Manager Integration with Azure Monitor

Esistono diversi modi per verificare che l'integrazione di Monitoraggio di Azure a Operations Manager sia corretta.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Per verificare l'integrazione dal portale di Azure

1. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input.
1. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro applicabile.  
1. Selezionare **Impostazioni avanzate**, **Origini connesse**, quindi **System Center**.
1. Nella tabella presente nella sezione System Center Operations Manager verrà visualizzato il nome del gruppo di gestione elencato con il numero di agenti e lo stato relativi all'ultima ricezione di dati.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Per verificare l'integrazione dalla console operatore

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Amministrazione**.
1. Selezionare **Management Pack** e nella casella di testo **Cerca** digitare **Advisor** o **Intelligence**.
1. A seconda delle soluzioni abilitate, nei risultati della ricerca viene elencato un Management Pack corrispondente.  Se, ad esempio, è stata abilitata la soluzione Gestione avvisi, nell'elenco sarà presente il Management Pack Gestione avvisi di Microsoft System Center Advisor.
1. Dalla visualizzazione **Monitoraggio** passare alla visualizzazione **Operations Management Suite\Stato di integrità**.  Selezionare un server di gestione nel riquadro **Management Server State** (Stato server di gestione) e nel riquadro **Visualizzazione Dettagli** confermare che il valore per la proprietà **Authentication service URI** (URI servizio di autenticazione) corrisponda all'ID area di lavoro Log Analytics.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Rimuovere l'integrazione con Monitoraggio di AzureRemove Integration with Azure Monitor

Quando l'integrazione tra il gruppo di gestione di Operations Manager e l'area di lavoro Log Analytics non è più richiesta, sono necessari diversi passaggi per rimuovere correttamente la connessione e la configurazione nel gruppo di gestione. Nella procedura seguente è possibile aggiornare l'area di lavoro di Log Analytics eliminando il riferimento del gruppo di gestione, eliminare i connettori di Monitoraggio di Azure e quindi eliminare i Management Pack che supportano l'integrazione con il servizio.  

I Management Pack per le soluzioni abilitate che si integrano con Operations Manager e i Management Pack necessari per supportare l'integrazione con Monitoraggio di Azure non possono essere eliminati facilmente dal gruppo di gestione. Ciò è dovuto al fatto che alcuni dei Management Pack di Monitoraggio di Azure hanno dipendenze da altri Management Pack correlati. Per eliminare i Management pack con una dipendenza su altri Management pack, scaricare lo script per [rimuovere un Management pack con le dipendenze](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) da TechNet Script Center.  

1. Aprire la shell dei comandi di Operations Manager con un account membro del ruolo Amministratori di Operations Manager.

    > [!WARNING]
    > Prima di procedere, verificare di non disporre di alcun Management Pack personalizzato che contenga nel nome la parola Advisor o IntelligencePack, altrimenti verranno eliminati dal gruppo di gestione nei passaggi seguenti.
    >

1. Dal prompt della shell dei comandi, digitare `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Quindi digitare `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Per rimuovere tutti i Management pack rimanenti che presentano una dipendenza da altri Management pack di System Center Advisor, usare lo script *RecursiveRemove.ps1* scaricato in precedenza da TechNet Script Center.  

    > [!NOTE]
    > Il passaggio per rimuovere i Management Pack di Advisor con PowerShell non eliminerà automaticamente Microsoft System Center Advisor o i Management Pack interni di Microsoft System Center Advisor.  Non tentare di eliminarli.  
    >  

1. Aprire la console di Operations Manager con un account membro del ruolo Amministratori di Operations Manager.
1. In **Amministrazione** selezionare il nodo **Management Pack** e nella casella **Cerca:** digitare **Advisor** e verificare che i seguenti Management Pack siano ancora importati nel gruppo di gestione:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Nel portale di Azure fare clic sul riquadro **Impostazioni**.
1. Selezionare **Origini connesse**.
1. Nella tabella della sezione System Center Operations Manager dovrebbe essere visualizzato il nome del gruppo di gestione che si desidera rimuovere dall'area di lavoro. Nella colonna **Ultimi dati** fare clic su **Rimuovi**.  

    > [!NOTE]
    > Il collegamento **Rimuovi** non sarà disponibile finché non saranno trascorsi 14 giorni senza alcuna attività rilevata dal gruppo di gestione connesso.  
    >

1. Verrà visualizzata una finestra in cui viene chiesto di confermare che si desidera procedere con la rimozione.  Fare clic su **Sì** per continuare.

Per eliminare i due connettori, Microsoft.SystemCenter.Advisor.DataConnector e Advisor Connector, salvare lo script di PowerShell riportato di seguito nel computer ed eseguirlo usando gli esempi seguenti:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Il computer da cui si esegue questo script, se non è un server di gestione, deve avere installata la shell dei comandi di Operations Manager, a seconda della versione del gruppo di gestione.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Se in futuro si prevede di riconnettere il gruppo di gestione a un'area di lavoro Log Analytics, è necessario importare nuovamente il file del Management Pack `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`. A seconda della versione di System Center Operations Manager distribuita nell'ambiente in uso, è possibile che questo file si trovi nel percorso seguente:

* Nel supporto di origine nella cartella `\ManagementPacks` per System Center 2016 - Operations Manager e versioni successive.
* Nel rollup dell'aggiornamento più recente applicato al gruppo di gestione. Per Operations Manager 2012, `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` la cartella di origine è e per `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`2012 R2, si trova in .

## <a name="next-steps"></a>Passaggi successivi

Per aggiungere funzionalità e raccogliere dati, vedere [Aggiungere soluzioni di Monitoraggio di Azure dalla raccolta soluzioni](../../azure-monitor/insights/solutions.md).
