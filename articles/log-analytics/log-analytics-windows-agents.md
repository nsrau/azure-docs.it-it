---
title: Connettere computer Windows a Log Analytics di Azure | Documentazione Microsoft
description: Questo articolo illustra i passaggi necessari per connettere i computer Windows dell&quot;infrastruttura locale al servizio Log Analytics usando una versione personalizzata di Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: ecee44194c32569f1d50001543ef4b37ecdb5eb3
ms.lasthandoff: 02/28/2017


---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Connettere computer Windows al servizio Log Analytics in Azure

Questo articolo illustra i passaggi necessari per connettere i computer Windows dell'infrastruttura locale alle aree di lavoro di OMS usando una versione personalizzata di Microsoft Monitoring Agent (MMA). È necessario installare e connettere gli agenti per tutti i computer da caricare affinché possano inviare dati al servizio Log Analytics e visualizzare ed eseguire operazioni su tali dati. Ogni agente può inviare report a più aree di lavoro.

È possibile installare gli agenti tramite il programma di installazione, la riga di comando o con Configurazione dello stato desiderato in Automazione di Azure.  

>[!NOTE]
Per le macchine virtuali eseguite in Azure, è possibile semplificare l'installazione usando l'[estensione macchina virtuale](log-analytics-azure-vm-extension.md).

Nei computer con connettività Internet, l'agente sfrutterà la connessione a Internet per inviare dati a OMS. Per i computer che non hanno connettività Internet, è possibile usare un proxy o il [gateway di OMS](log-analytics-oms-gateway.md).

La connessione dei computer Windows a OMS è semplice e richiede solo 3 passaggi:

1. Scaricare il file di installazione dell'agente dal portale di OMS
2. Installare l'agente usando il metodo che scelto
3. Configurare l'agente o aggiungere altre aree di lavoro, se necessario

Il diagramma seguente illustra la relazione tra i computer Windows e OMS dopo l'installazione e la configurazione degli agenti.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>Requisiti di sistema e configurazione richiesta
Prima di installare o distribuire gli agenti, esaminare i dettagli seguenti per assicurarsi che i requisiti necessari siano soddisfatti.

- È possibile installare MMA per OMS solo in computer che eseguono Windows Server 2008 Service Pack 1 o versione successiva oppure Windows 7 SP1 o versione successiva.
- È necessaria una sottoscrizione di OMS.  Per altre informazioni, vedere l'articolo di [introduzione a Log Analytics](log-analytics-get-started.md).
- Ogni computer Windows deve potersi connettere a Internet tramite il protocollo HTTPS o il gateway di OMS. Questa connessione può essere diretta, tramite un proxy o tramite il gateway di OMS.
- È possibile installare MMA per OMS in computer autonomi, server e macchine virtuali. Per connettere a OMS le macchine virtuali ospitate da Azure, vedere l'articolo [Connettere macchine virtuali di Azure a Log Analytics](log-analytics-azure-vm-extension.md).
- L'agente deve usare la porta TCP 443 per diverse risorse. Per altre informazioni, vedere l'articolo che illustra come [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

## <a name="download-the-agent-setup-file-from-oms"></a>Per scaricare il file di installazione dell'agente da OMS
1. Nella pagina **Panoramica** del portale di OMS fare clic sul riquadro **Impostazioni**.  Scegliere la scheda **Connected Sources** nella parte superiore.  
    ![Scheda Origini connesse](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Fare clic su **Server Windows** e quindi fare clic su **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.
3. A destra di **ID area di lavoro**fare clic sull'icona di copia e incollare l'ID nel Blocco note.
4. A destra di **Chiave primaria**fare clic sull'icona di copia e incollare la chiave nel Blocco note.     

## <a name="install-the-agent-using-setup"></a>Installare gli agenti con il programma di installazione
1. Eseguire il programma di installazione per installare l'agente in un computer da gestire.
2. Nella pagina di benvenuto fare clic su **Avanti**.
3. Nella pagina Condizioni di licenza leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina della cartella di destinazione modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina delle opzioni di installazione dell'agente è possibile scegliere di connettere l'agente ad Azure Log Analytics (OMS) o Operations Manager oppure di lasciare vuote le opzioni se si vuole configurare l'agente in un secondo momento. Fare clic su **Avanti**.   
    - Se si sceglie di connettersi ad Azure Log Analytics (OMS), incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note durante la procedura precedente e quindi fare clic su **Avanti**.  
        ![incollare ID area di lavoro e chiave primaria](./media/log-analytics-windows-agents/connect-workspace.png)
    - Se si sceglie di connettersi a Operations Manager, digitare il **nome del gruppo di gestione**, il nome del **server di gestione** e la **porta del server di gestione** e quindi fare clic su **Avanti**. Nella pagina Account azione agente scegliere l'Account di sistema locale o un account di dominio locale e quindi fare clic su **Avanti**.  
        ![configurazione del gruppo di gestione](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![account azione agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Nella pagina Pronto per l'installazione rivedere le scelte effettuate e quindi fare clic su **Installa**.
7. Nella pagina Configurazione completata fare clic su **Fine**.
8. Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso a Operational Insights (OMS). Quando si è connessi a OMS, l'agente visualizza un messaggio nel quale è indicato che **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite.**

## <a name="install-the-agent-using-the-command-line"></a>Installare l'agente usando la riga di comando
- Modificare e usare il seguente esempio per installare l'agente mediante la riga di comando.

    >[!NOTE]
    Per aggiornare un agente è necessario usare l'API di scripting di Log Analytics. La procedura per aggiornare un agente è descritta nella sezione successiva.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>Aggiornare l'agente e aggiungere un'area di lavoro usando uno script
È possibile aggiornare un agente e aggiungere un'area di lavoro usando l'API di scripting di Log Analytics con il seguente codice PowerShell di esempio.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[!NOTE]
Se in precedenza sono stati usati la riga di comando o uno script per installare o configurare l'agente, `EnableAzureOperationalInsights` è stato sostituito da `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installare l'agente usando DSC in Automazione di Azure

È possibile utilizzare lo script di esempio riportato di seguito per installare l'agente utilizzando DSC in Automazione di Azure. Nell'esempio viene installato l'agente a 64 bit, identificato dal valore `URI`. È possibile utilizzare anche la versione a 32 bit sostituendo il valore dell'URI. Gli URI per entrambe le versioni sono:

- Agente di Windows a 64 bit - https://go.microsoft.com/fwlink/?LinkId=828603
- Agente di Windows a 32 - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
La procedura e lo script di esempio riportati di seguito non determinano l'aggiornamento di un agente esistente.

1. Importare il modulo xPSDesiredStateConfiguration da [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) in Automazione di Azure.  
2.    Creare gli asset variabili di Automazione di Azure per *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Impostare *OPSINSIGHTS_WS_ID* sull'ID dell'area di lavoro di Log Analytics di OMS e impostare *OPSINSIGHTS_WS_KEY* sulla chiave primaria dell'area di lavoro.
3.    Usare lo script seguente e salvarlo come MMAgent.ps1
4.    Modificare e quindi usare l'esempio seguente per installare l'agente usando DSC in Automazione di Azure. Importare MMAgent.ps1 in Automazione di Azure tramite l'interfaccia di Automazione di Azure o il cmdlet.
5.    Assegnare un nodo alla configurazione. Entro 15 minuti il nodo controllerà la configurazione e verrà effettuato il push di MMA al nodo.

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>Ottenere il valore di ProductId più recente

Il `ProductId value` nello script MMAgent.ps1 di è univoco per ogni versione dell'agente. Quando viene pubblicata una versione aggiornata di ogni agente, il valore di ProductId cambia. Pertanto, quando il ProductId viene modificato in un secondo momento, è possibile individuare la versione dell'agente utilizzando un semplice script. Dopo aver installato la versione più recente dell'agente in un server di prova, è possibile utilizzare lo script seguente per ottenere il valore di ProductId installato. Utilizzando il valore di ProductId più recente, è possibile aggiornare il valore nello script MMAgent.ps1.

```
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configurare manualmente un agente o aggiungere nuove aree di lavoro
Se gli agenti sono stati installati ma non configurati o se si vuole che l'agente invii report a più aree di lavoro, è possibile usare le informazioni seguenti per abilitare un agente o riconfigurarlo. Dopo aver configurato l'agente, questo verrà registrato con il servizio agente e otterrà le informazioni di configurazione necessarie nonché i Management Pack contenenti informazioni sulla soluzione.

1. Dopo avere installato Microsoft Monitoring Agent, aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent** e fare clic sulla scheda **Azure Log Analytics (OMS)**.   
3. Fare clic su **Aggiungi** per aprire la casella **Aggiungi un'area di lavoro di Log Analytics**.
4. Incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)**, copiati nel Blocco note durante una procedura precedente relativa all'area di lavoro da aggiungere e quindi fare clic su **OK**.  
    ![configurazione di Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

Al termine della raccolta dei dati dai computer monitorati dall'agente, il numero di computer monitorati da OMS verrà visualizzato nel portale di OMS nella scheda **Origini connesse** della sezione **Impostazioni** come **server connessi**.


## <a name="to-disable-an-agent"></a>Per disabilitare l'agente
1. Dopo aver installato l'agente, aprire il **Pannello di controllo**.
2. Aprire Microsoft Monitoring Agent e fare clic sulla scheda **Azure Log Analytics (OMS)** .
3. Selezionare un'area di lavoro e quindi fare clic su **Rimuovi**. Ripetere questo passaggio per tutte le altre aree di lavoro.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Configurare facoltativamente gli agenti per l'invio di report a un gruppo di gestione di Operations Manager

Se si usa Operations Manager nell'infrastruttura IT, è anche possibile usare l'agente MMA come agente di Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Per configurare gli agenti MMA per l'invio di report a un gruppo di gestione di Operations Manager
1.    Nel computer in cui è installato l'agente aprire **Pannello di controllo**.  
2.    Aprire **Microsoft Monitoring Agent** e fare clic sulla scheda **Operations Manager**.  
    ![Microsoft Monitoring Agent scheda Operations Manager](./media/log-analytics-windows-agents/om-mg01.png)
3.    Se i server di Operations Manager sono configurati per l'integrazione con Active Directory, fare clic su **Aggiorna automaticamente assegnazioni gruppi di gestione da Servizi di dominio Active Directory**.
4.    Fare clic su **Aggiungi** per aprire la finestra di dialogo **Aggiungi gruppo di gestione**.  
    ![Microsoft Monitoring Agent Aggiungi gruppo di gestione](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.    In **Nome gruppo di gestione** digitare il nome del gruppo di gestione.
6.    Nella casella **Server di gestione primario** digitare il nome computer del server di gestione primario.
7.    Nella casella **Porta server di gestione** digitare il numero di porta TCP.
8.    In **Account azione agente**scegliere l'account di sistema locale o un account di dominio locale.
9.    Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi gruppo di gestione** e quindi fare clic su **OK** per chiudere la finestra di dialogo **Proprietà di Microsoft Monitoring Agent**.

## <a name="optionally-configure-agents-to-use-the-oms-gateway"></a>Facoltativamente, configurare gli agenti per l'uso del gateway di OMS

Se sono presenti server o client senza connessione a Internet, è comunque possibile configurarli per l'invio di dati a OMS tramite il gateway di OMS.  Quando si usa il gateway, tutti i dati degli agenti vengono inviati tramite un singolo server con accesso a Internet. Il gateway trasferisce i dati dagli agenti direttamente a OMS senza analizzarli.

Per altre informazioni sul gateway, comprese installazione e configurazione, vedere [Gateway di OMS](log-analytics-oms-gateway.md).

Per informazioni su come configurare gli agenti per usare un server proxy, in questo caso il gateway di OMS, vedere [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

## <a name="optionally-configure-proxy-and-firewall-settings"></a>Configurare facoltativamente le impostazioni di proxy e firewall
Se nell'ambiente sono presenti server proxy o firewall che limitano l'accesso a Internet, vedere [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md) per consentire agli agenti di comunicare con il servizio OMS.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md) per aggiungere funzionalità e raccogliere dati.
- [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md) se l'organizzazione usa un server proxy o un firewall per consentire agli agenti di comunicare con il servizio Log Analytics.

