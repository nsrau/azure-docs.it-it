---
title: Connettere Operations Manager a Log Analytics | Microsoft Docs
description: Per gestire l'investimento esistente in System Center Operations Manager e usare le funzionalità estese con Log Analytics, è possibile integrare Operations Manager con l'area di lavoro.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: ba15ecdb59eb98094367d8cd4760323d863332c4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222710"
---
# <a name="connect-operations-manager-to-log-analytics"></a>Connettere Operations Manager a Log Analytics
Per gestire l'investimento esistente in System Center Operations Manager e usare le funzionalità estese con Log Analytics, è possibile integrare Operations Manager con l'area di lavoro di Log Analytics. Ciò consente di sfruttare le opportunità di Log Analytics pur continuando a usare Operations Manager per:

* Monitorare l'integrità dei servizi IT con Operations Manager
* Gestire l'integrazione con le soluzioni ITSM che supportano la gestione di eventi imprevisti e problemi
* Gestire il ciclo di vita degli agenti distribuiti in macchine virtuali IaaS locali e nel cloud pubblico monitorate con Operations Manager

L'integrazione con System Center Operations Manager accresce il valore della strategia delle operazioni del servizio sfruttando la velocità e l'efficienza di Log Analytics nella raccolta, nell'archiviazione e nell'analisi dei dati di Operations Manager. Log Analytics semplifica la correlazione e permette di identificare le cause dei problemi e di esporre le nuove occorrenze per supportare il processo di gestione dei problemi esistenti. La flessibilità del motore di ricerca per esaminare prestazioni, dati di eventi e avvisi, con funzionalità di creazione di report e dashboard avanzati per esporre questi dati in modo significativo è indicativa dei vantaggi di Log Analytics come complemento di Operations Manager.

Gli agenti di segnalazione al gruppo di gestione di Operations Manager raccolgono i dati dai server basati sulle soluzioni e sulle origini dati di Log Analytics abilitate nell'area di lavoro. A seconda della soluzione abilitata, i dati vengono inviati direttamente da un server di gestione di Operations Manager al servizio oppure, a causa del volume dei dati raccolti nel sistema gestito dall'agente, vengono inviati direttamente dall'agente a Log Analytics. Il server di gestione inoltra direttamente i dati al servizio. I dati non vengono mai scritti nel database operativo o nel database del data warehouse. Quando un server di gestione perde la connettività con Log Analytics, memorizza i dati nella cache locale finché non viene ristabilita la comunicazione con Log Analytics. Se il server di gestione è offline a causa di interventi di manutenzione pianificata o di un'interruzione non pianificata, un altro server di gestione del gruppo di gestione riprenderà la connettività con Log Analytics.  

Il diagramma seguente illustra la connessione tra gli agenti e i server di gestione in un gruppo di gestione di System Center Operations Manager e Log Analytics, includendo anche la direzione e le porte.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Se i criteri di sicurezza IT non consentono ai computer nella rete di connettersi a Internet, i server di gestione possono essere configurati per la connessione al gateway OMS per poter ricevere le informazioni di configurazione e inviare i dati raccolti a seconda della soluzione abilitata. Per altre informazioni e procedure su come configurare il gruppo di gestione di Operations Manager per comunicare tramite un Gateway OMS con il servizio Log Analytics, vedere [Connettere i computer a OMS tramite il Gateway OMS](log-analytics-oms-gateway.md).  

## <a name="prerequisites"></a>Prerequisiti 
Prima di iniziare, esaminare i requisiti seguenti.

* Log Analytics supporta solo System Center Operations Manager 1807, Operations Manager 1801, Operations Manager 2016, Operations Manager 2012 SP1 UR6 o versioni successive e Operations Manager 2012 R2 UR2 o versioni successive. Il supporto per il proxy è stato aggiunto in Operations Manager 2012 SP1 UR7 e Operations Manager 2012 R2 UR3.
* Tutti gli agenti di Operations Manager devono soddisfare i requisiti di supporto minimo. Verificare che gli agenti dispongano dell'aggiornamento minimo richiesto, altrimenti le comunicazioni degli agenti di Windows possono avere esito negativo e generare errori nel log eventi di Operations Manager.
* Un'area di lavoro di Log Analytics. Per altre informazioni, vedere [Raccogliere dati dai computer dell'ambiente con Log Analytics](log-analytics-concept-hybrid.md).
* Autenticazione in Azure con un account membro del [ruolo di collaboratore di Log Analytics](log-analytics-manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Modifiche recenti alle API di Azure impediranno ai clienti di configurare correttamente per la prima volta l'integrazione tra il gruppo di gestione e Log Analytics. I clienti che hanno già integrato il proprio gruppo di gestione con il servizio non sono interessati da queste modifiche, a meno che non debbano riconfigurare la connessione esistente.  
>Per ogni versione di Operations Manager è stato rilasciato un nuovo Management Pack:  
>* Per System Center Operations Manager 1801, scaricare il Management Pack da [qui](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Per System Center 2016 - Operations Manager, scaricare il Management Pack da [qui](https://www.microsoft.com/download/details.aspx?id=57172)  
>* Per System Center Operations Manager 2012 R2, scaricare il Management Pack da [qui](https://www.microsoft.com/en-us/download/details.aspx?id=57171)  

### <a name="network"></a>Rete
Le informazioni di seguito elencano i dettagli di configurazione del proxy e del firewall necessari all'agente di Operations Manager, ai server di gestione e alla Console operatore per comunicare con Log Analytics. Il traffico in uscita da ogni componente di rete viene indirizzato al servizio Log Analytics.   

|Risorsa | Numero della porta| Ignorare l'analisi HTTPS|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Yes|  
|\*.oms.opinsights.azure.com| 443|Yes|  
|\*.blob.core.windows.net| 443|Yes|  
|\*.azure-automation.net| 443|Yes|  
|**Server di gestione**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Yes|  
|\*.ods.opinsights.azure.com| 443| Yes|  
|*.azure-automation.net | 443| Yes|  
|**Dalla console di Operations Manager a quella di OMS**|||  
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
Per garantire la sicurezza dei dati in transito verso Log Analytics, è consigliabile configurare l'agente e il gruppo di gestione in modo da usare almeno il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**. Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-log-analytics"></a>Connettere Operations Manager a Log Analytics
Eseguire questa serie di passaggi per configurare il gruppo di gestione di Operations Manager per connettersi a una delle aree di lavoro di Log Analytics.

Durante la registrazione iniziale del gruppo di gestione di Operations Manager con un'area di lavoro di Log Analytics, l'opzione per specificare la configurazione proxy per il gruppo di gestione non è disponibile nella console operatore.  Il gruppo di gestione deve essere registrato correttamente con il servizio prima che questa opzione sia disponibile.  Per risolvere questo problema, è necessario aggiornare la configurazione del proxy di sistema tramite Netsh nel sistema da cui si esegue la console operatore per configurare l'integrazione e tutti i server di gestione nel gruppo di gestione.  

1. Aprire un prompt dei comandi con privilegi elevati.
   a. Passare a **Start** e digitare **cmd**.
   b. Fare clic con il pulsante destro del mouse su **Prompt dei comandi** e selezionare Esegui come amministratore**.
1. Immettere il comando seguente e premere **INVIO**:

    `netsh winhttp set proxy <proxy>:<port>`

Dopo aver completato la procedura di integrazione seguente con Log Analytics, è possibile rimuovere la configurazione eseguendo `netsh winhttp reset proxy` e quindi usare l'opzione **Configure proxy server** (Configura server proxy) nella Console operatore per specificare il server proxy o il server del Gateway OMS. 

1. Nella console di Operations Manager selezionare l'area di lavoro **Amministrazione** .
1. Espandere il nodo Operations Management Suite e fare clic su **Connessione**.
1. Fare clic sul collegamento **Registrazione a Operations Management Suite** .
1. Nella pagina**Caricamento guidato di Operations Management Suite: Autenticazione**, immettere l'indirizzo di posta elettronica o il numero di telefono e la password dell'account amministratore associato alla sottoscrizione OMS e fare clic su **Accedi**.
1. Al termine dell'autenticazione, nella pagina **Operations Management Suite Onboarding Wizard: Select Workspace** (Caricamento guidato di Operations Management Suite: selezione area di lavoro) verrà chiesto di selezionare il tenant e la sottoscrizione di Azure e l'area di lavoro di Log Analytics. Se si ha più di un'area di lavoro, selezionare l'area di lavoro che si vuole registrare con il gruppo di gestione di Operations Manager nell'elenco a discesa e quindi fare clic su **Avanti**.
   
   > [!NOTE]
   > Operations Manager supporta solo un'area di lavoro di Log Analytics alla volta. La connessione e i computer registrati in Log Analytics con l'area di lavoro precedente vengono rimossi da Log Analytics.
   > 
   > 
1. Nella pagina **Caricamento guidato di Operations Management Suite: Riepilogo** confermare le impostazioni e, se sono corrette, fare clic su **Crea**.
1. Nella pagina **Caricamento guidato di Operations Management Suite: Fine** fare clic su **Chiudi**.

### <a name="add-agent-managed-computers"></a>Aggiungere computer gestiti dagli agenti
Dopo aver configurato l'integrazione con l'area di lavoro di Log Analytics, viene stabilita solo una connessione con il servizio, ma nessun dato viene raccolto dagli agenti che fanno riferimento al gruppo di gestione. Questo avverrà solo dopo aver configurato i computer specifici gestiti dagli agenti che devono raccogliere i dati per Log Analytics. È possibile selezionare gli oggetti computer singolarmente o selezionare un gruppo contenente gli oggetti computer Windows. Non è possibile selezionare un gruppo contenente istanze di un'altra classe, ad esempio dischi logici o database SQL.

1. Aprire la console di Operations Manager e selezionare lo spazio di lavoro **Amministrazione** .
1. Espandere il nodo Operations Management Suite e fare clic su **Connessione**.
1. Fare clic sul collegamento **Aggiungi computer/gruppo** nell'intestazione Azioni sul lato destro del riquadro.
1. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi da caricare in Log Analytics, fare clic su **Aggiungi** e quindi su **OK**.

È possibile visualizzare i computer e i gruppi configurati per raccogliere dati dal nodo Computer gestiti in Operations Management Suite nell'area di lavoro **Amministrazione** della console operatore. Da qui è possibile aggiungere o rimuovere i computer e i gruppi in base alle esigenze.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configurare le impostazioni del proxy nella Console operatore
Seguire questa procedura se tra il gruppo di gestione e il servizio Log Analytics è presente un server proxy interno. Queste impostazioni vengono gestite centralmente dal gruppo di gestione e distribuite ai sistemi gestiti dagli agenti inclusi nell'ambito della raccolta dati per Log Analytics.  Questo è un vantaggio nel caso di alcune soluzioni che ignorano il server di gestione e inviano i dati direttamente al servizio.

1. Aprire la console di Operations Manager e selezionare lo spazio di lavoro **Amministrazione** .
1. Espandere Operations Management Suite e quindi fare clic su **Connessioni**.
1. Nella visualizzazione OMS Connection (Connessione OMS), fare clic su **Configure Proxy Server**(Configura server proxy).
1. Nella pagina **Configurazione guidata impostazioni di Operations Management Suite: Server proxy** selezionare **Usa un server proxy per accedere a Operations Management Suite** e immettere l'URL con il numero di porta, ad esempio http://corpproxy:80, quindi fare clic su **Fine**.

Se il server proxy richiede l'autenticazione, seguire questa procedura per configurare le credenziali e le impostazioni da propagare ai computer gestiti che faranno riferimento a OMS nel gruppo di gestione.

1. Aprire la console di Operations Manager e selezionare lo spazio di lavoro **Amministrazione** .
1. In **Configurazione RunAs** selezionare **Profili**.
1. Aprire il profilo **Proxy del profilo RunAs di System Center Advisor** .
1. In Creazione guidata profilo RunAs fare clic su Aggiungi per usare un account RunAs. È possibile creare un [account RunAs](https://technet.microsoft.com/library/hh321655.aspx) oppure usare un account esistente. L'account deve avere autorizzazioni sufficienti per passare attraverso il server proxy.
1. Per impostare l'account da gestire, scegliere **Classe, gruppo o oggetto selezionato**, fare clic su **Seleziona...** e su **Gruppo...** per aprire la finestra **Ricerca gruppi**.
1. Cercare e quindi selezionare **Gruppo di server di monitoraggio di Microsoft System Center Advisor**. Fare clic su **OK** dopo avere selezionato il gruppo per chiudere la finestra **Ricerca gruppi**.
1. Fare clic su **OK** per chiudere la finestra **Aggiungi account RunAs**.
1. Fare clic su **Salva** per completare la procedura guidata e salvare le modifiche.

Una volta creata la connessione e aver configurato gli agenti che raccoglieranno e segnaleranno i dati a Log Analytics, nel gruppo di gestione viene applicata la configurazione seguente, non necessariamente in questo ordine:

* Viene creato l'account RunAs **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**, che viene associato al profilo RunAs **Microsoft System Center Advisor eseguito come profilo BLOB** e che specifica come destinazione due classi, **Server raccolta** e **Gruppo di gestione di Operations Manager**.
* Vengono creati due connettori.  Il primo è denominato **Microsoft.SystemCenter.Advisor.DataConnector** e viene configurato automaticamente con una sottoscrizione che inoltra tutti gli avvisi generati dalle istanze di tutte le classi nel gruppo di gestione a Log Analytics. Il secondo connettore è **Advisor Connector**, che è responsabile della comunicazione con il servizio Web OMS e della condivisione dei dati.
* Gli agenti e i gruppi selezionati per raccogliere i dati nel gruppo di gestione vengono aggiunti al **Gruppo di server di monitoraggio di Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aggiornamenti di Management Pack
Al termine della configurazione, il gruppo di gestione di Operations Manager stabilisce una connessione con il servizio Log Analytics. Il server di gestione si sincronizza con il servizio Web e riceve le informazioni di configurazione aggiornate sotto forma di Management Pack per le soluzioni abilitate che si integrano con Operations Manager. Operations Manager cerca gli aggiornamenti per questi Management Pack e, se disponibili, li scarica e li importa automaticamente. Esistono due regole in particolare che controllano questo comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate**: aggiorna i Management Pack di Log Analytics di base. Per impostazione predefinita, viene eseguita ogni 12 ore.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : aggiorna i Management Pack delle soluzioni abilitati nell'area di lavoro. Per impostazione predefinita, viene eseguita ogni cinque (5) minuti.

È possibile eseguire l'override di queste due regole per impedire il download automatico disabilitandole o per modificare la frequenza di sincronizzazione del server di gestione con OMS per determinare se è disponibile un nuovo Management Pack e se deve essere scaricato. Seguire i passaggi in [Come eseguire l'override di una regola o di un'attività di monitoraggio](https://technet.microsoft.com/library/hh212869.aspx) per modificare il parametro **Frequenza** con un valore espresso in secondi per cambiare la pianificazione della sincronizzazione oppure per modificare il parametro **Abilitata** per disabilitare le regole. Specificare come destinazione gli override di tutti gli oggetti della classe Gruppo di gestione di Operations Manager.

Per continuare a seguire il processo di controllo delle modifiche esistente per controllare le versioni dei Management Pack nel gruppo di gestione di produzione, è possibile disabilitare le regole e abilitarle in orari specifici in cui gli aggiornamenti sono consentiti. Se nell'ambiente è presente un gruppo di sviluppo o di gestione del controllo di qualità con connettività a Internet, è possibile configurare tale gruppo di gestione con un'area di lavoro di Log Analytics per supportare questo scenario. Questo consente di rivedere e valutare le versioni iterative dei Management Pack di Log Analytics prima di rilasciarle nel gruppo di gestione di produzione.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Passare un gruppo di Operations Manager a una nuova area di lavoro di Log Analytics
1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
1. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics** e quindi creare un'area di lavoro.  
1. Aprire la console di Operations Manager con un account membro del ruolo Amministratori di Operations Manager e selezionare l’area di lavoro **Amministrazione** .
1. Espandere Operations Management Suite e selezionare **Connessioni**.
1. Selezionare il collegamento **Riconfigura Operation Management Suite** nella parte centrale del riquadro.
1. Seguire le indicazioni in **Operations Management Suite Onboarding Wizard** (Caricamento guidato di Operations Management Suite) e immettere l'indirizzo di posta elettronica o il numero di telefono e la password dell'account amministratore associato alla nuova area di lavoro Log Analytics.
   
   > [!NOTE]
   > La pagina **Caricamento guidato di Operations Management Suite: Selezione area di lavoro** presenta l'area di lavoro esistente in uso.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Convalidare l'integrazione di Operations Manager con Log Analytics
È possibile verificare che l'integrazione tra Log Analytics e Operations Manager sia riuscita in diversi modi.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Per verificare l'integrazione dal portale di Azure
1. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input.
1. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro applicabile.  
1. Selezionare **Impostazioni avanzate**, **Origini connesse**, quindi **System Center**. 
1. Nella tabella presente nella sezione System Center Operations Manager verrà visualizzato il nome del gruppo di gestione elencato con il numero di agenti e lo stato relativi all'ultima ricezione di dati.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Per verificare l'integrazione dalla console operatore
1. Aprire la console di Operations Manager e selezionare lo spazio di lavoro **Amministrazione** .
1. Selezionare **Management Pack** e nella casella di testo **Cerca** digitare **Advisor** o **Intelligence**.
1. A seconda delle soluzioni abilitate, nei risultati della ricerca viene elencato un Management Pack corrispondente.  Se, ad esempio, è stata abilitata la soluzione Gestione avvisi, nell'elenco sarà presente il Management Pack Gestione avvisi di Microsoft System Center Advisor.
1. Dalla visualizzazione **Monitoraggio** passare alla visualizzazione **Operations Management Suite\Stato di integrità**.  Selezionare un server di gestione nel riquadro **Management Server State** (Stato server di gestione) e nel riquadro **Visualizzazione Dettagli** confermare che il valore per la proprietà **Authentication service URI** (URI servizio di autenticazione) corrisponda all'ID area di lavoro di Log Analytics.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Rimuovere l'integrazione con Log Analytics
Quando l'integrazione tra il gruppo di gestione di Operations Manager e l'area di lavoro di Log Analytics non è più richiesta, sono necessari diversi passaggi per rimuovere correttamente la connessione e la configurazione nel gruppo di gestione. Per la procedura seguente è necessario aggiornare l'area di lavoro personale di Log Analytics eliminando il riferimento del gruppo di gestione, eliminare i connettori di Log Analytics e infine eliminare i Management Pack che supportano l'integrazione con il servizio.  

I Management Pack per le soluzioni abilitate che si integrano con Operations Manager e i Management Pack necessari per supportare l'integrazione con il servizio Log Analytics non possono essere facilmente eliminati dal gruppo di gestione. Questo perché alcuni Management Pack di Log Analytics hanno dipendenze su altri Management Pack correlati. Per eliminare i Management pack con una dipendenza su altri Management pack, scaricare lo script per [rimuovere un Management pack con le dipendenze](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) da TechNet Script Center.  

1. Aprire la shell dei comandi di Operations Manager con un account membro del ruolo Amministratori di Operations Manager.
   
    > [!WARNING]
    > Prima di procedere, verificare di non disporre di alcun Management Pack personalizzato che contenga nel nome la parola Advisor o IntelligencePack, altrimenti verranno eliminati dal gruppo di gestione nei passaggi seguenti.
    > 

1. Dal prompt della shell dei comandi, digitare `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Quindi digitare `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Per rimuovere tutti i Management pack rimanenti che presentano una dipendenza da altri Management pack di System Center Advisor, usare lo script *RecursiveRemove.ps1* scaricato in precedenza da TechNet Script Center.  
 
    > [!NOTE]
    > Il passaggio per rimuovere i Management Pack di Advisor con PowerShell non eliminerà automaticamente Microsoft System Center Advisor o i Management Pack interni di Microsoft System Center Advisor.  Non tentare di eliminarli.  
    >  

1. Aprire la console di Operations Manager con un account membro del ruolo Amministratori di Operations Manager.
1. In **Amministrazione** selezionare il nodo **Management Pack** e nella casella **Cerca:** digitare **Advisor** e verificare che i seguenti Management Pack siano ancora importati nel gruppo di gestione:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Nel portale di OMS fare clic sul riquadro **Impostazioni**.
1. Selezionare **Origini connesse**.
1. Nella tabella della sezione System Center Operations Manager dovrebbe essere visualizzato il nome del gruppo di gestione che si desidera rimuovere dall'area di lavoro. Nella colonna **Ultimi dati** fare clic su **Rimuovi**.  
   
    > [!NOTE]
    > Il collegamento **Rimuovi** non sarà disponibile finché non saranno trascorsi 14 giorni senza alcuna attività rilevata dal gruppo di gestione connesso.  
    > 

1. Verrà visualizzata una finestra in cui viene chiesto di confermare che si desidera procedere con la rimozione.  Fare clic su **Sì** per procedere. 

Per eliminare i due connettori, Microsoft.SystemCenter.Advisor.DataConnector e Advisor Connector, salvare lo script di PowerShell riportato di seguito nel computer ed eseguirlo usando gli esempi seguenti:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Il computer da cui si esegue questo script, se non è un server di gestione, deve avere installata la shell dei comandi di Operations Manager, a seconda della versione del gruppo di gestione.
> 
> 

```
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

Se in futuro si prevede di riconnettere il gruppo di gestione a un'area di lavoro di Log Analytics, è necessario importare nuovamente il file del Management Pack `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`. A seconda della versione di System Center Operations Manager distribuita nell'ambiente in uso, è possibile che questo file si trovi nel percorso seguente:

* Nel supporto di origine nella cartella `\ManagementPacks` per System Center 2016 - Operations Manager e versioni successive.
* Nel rollup dell'aggiornamento più recente applicato al gruppo di gestione. Per Operations Manager 2012, la cartella di origine è ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` e per la versione 2012 R2 si trova in `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Passaggi successivi
Per aggiungere funzionalità e raccogliere i dati, vedere [Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md).


