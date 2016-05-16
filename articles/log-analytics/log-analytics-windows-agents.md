<properties
	pageTitle="Connettere computer Windows a Log Analytics | Microsoft Azure"
	description="Questo articolo illustra i passaggi per connettere i computer Windows nell'infrastruttura locale direttamente a OMS usando una versione personalizzata di Microsoft Monitoring Agent (MMA)."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Connettere computer Windows a Log Analytics

Questo articolo illustra i passaggi per connettere i computer Windows nell'infrastruttura locale direttamente a OMS usando una versione personalizzata di Microsoft Monitoring Agent (MMA). È necessario installare e connettere gli agenti per tutti i computer da caricare in OMS affinché possano inviare dati a OMS e per visualizzare ed eseguire operazioni su tali dati nel portale di OMS.

È possibile installare gli agenti tramite il programma di installazione, la riga di comando o con Configurazione dello stato desiderato in Automazione di Azure.

Nei computer con connettività Internet l'agente utilizzerà la connessione a Internet per inviare dati a OMS. Per i computer che non hanno connettività Internet, è possibile usare un proxy o il server d'inoltro di Log Analytics di OMS.

La connessione dei computer Windows a OMS è semplice e richiede solo 3 passaggi:

1. Scaricare il file di installazione dell'agente
2. Installare l'agente usando il metodo che scelto
3. Configurare l'agente, se necessario

Il diagramma seguente illustra la relazione tra i computer Windows e OMS dopo l'installazione e la configurazione degli agenti.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## Requisiti di sistema e configurazione richiesta
Prima di installare o distribuire gli agenti, esaminare i dettagli seguenti per assicurarsi che i requisiti necessari siano soddisfatti.

- È possibile installare MMA per OMS solo in computer che eseguono Windows Server 2008 Service Pack 1 o versione successiva oppure Windows 7 SP1 o versione successiva.
- È necessaria una sottoscrizione di OMS. Per altre informazioni, vedere l'articolo di [introduzione a Log Analytics](log-analytics-get-started.md).
- Ogni computer Windows deve essere in grado di connettersi a Internet. Questa connessione può essere diretta tramite un proxy o tramite il server di inoltro di Log Analytics di OMS.
- È possibile installare MMA per OMS in computer autonomi, server e macchine virtuali. Per connettere le macchine virtuali ospitate da Azure a OMS, vedere l'articolo relativo che illustra come [connettere Archiviazione di Azure a Log Analytics](log-analytics-azure-storage.md).
- L'agente deve usare la porta TCP 443 per diverse risorse. Per altre informazioni, vedere l'articolo che illustra come [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

## Per scaricare il file di installazione dell'agente da OMS
1. Nella pagina **Panoramica** del portale di OMS fare clic sul riquadro **Impostazioni**. Scegliere la scheda **Connected Sources** nella parte superiore. ![Scheda Origini connesse](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. In **Collega i computer direttamente** fare clic su **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.
3. A destra di **ID area di lavoro** fare clic sull'icona di copia e incollare l'ID nel Blocco note.
4. A destra di **Chiave primaria** fare clic sull'icona di copia e incollare la chiave nel Blocco note. ![Copiare ID area di lavoro e Chiave primaria](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## Installare gli agenti con il programma di installazione
1. Eseguire il programma di installazione per installare l'agente in un computer da gestire.
2. Nella pagina di benvenuto fare clic su **Avanti**.
3. Nella pagina Condizioni di licenza leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina della cartella di destinazione modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina delle opzioni di installazione dell'agente è possibile scegliere di connettere l'agente a Operational Insights (OMS), Operations Manager, o lasciare vuote le opzioni se si vuole configurare l'agente in un secondo momento. Fare clic su **Avanti**.   
    - Se si sceglie di connettersi a Operational Insights (OMS), incollare l'**ID area di lavoro** e la **Chiave dell'area di lavoro (Chiave primaria)** copiati nel Blocco note nella procedura precedente e quindi fare clic su **Avanti**. ![Incollare ID area di lavoro e Chiave primaria](./media/log-analytics-windows-agents/oms-mma-aoi-setup.png)
    - Se si sceglie di connettersi a Operations Manager, digitare il **Nome gruppo di gestione **, il nome del **Server di gestione** e la **Porta server di gestione** e quindi fare clic su **Avanti**. Nella pagina Account azione agente scegliere l'Account di sistema locale o un account di dominio locale e quindi fare clic su **Avanti**. ![Configurazione del gruppo di gestione](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![Account azione agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Nella pagina Pronto per l'installazione rivedere le scelte effettuate e quindi fare clic su **Installa**.
7. Nella pagina Configurazione completata fare clic su **Fine**.
8. Al termine, **Microsoft Monitoring Agent** verrà visualizzato nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso a Operational Insights (OMS). Quando si è connessi a OMS, l'agente visualizza un messaggio nel quale è indicato che **Microsoft Monitoring Agent ha eseguito la connessione al servizio Azure Operational Insights (OMS).**

## Installare l'agente usando la riga di comando
- Modificare e usare il seguente esempio per installare l'agente mediante la riga di comando.

    ```
    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## Installare l'agente usando DSC in Automazione di Azure
1. Impostare il modulo DSC xPSDesiredStateConfiguration da [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) in Automazione di Azure.  

2.	Creare gli asset variabili di Automazione di Azure per *OPSINSIGHTS\_WS\_ID* e *OPSINSIGHTS\_WS\_KEY*. Impostare *OPSINSIGHTS\_WS\_ID* sull'ID dell'area di lavoro di Log Analytics di OMS e impostare *OPSINSIGHTS\_WS\_KEY* sulla chiave primaria dell'area di lavoro.
3.	Usare lo script seguente e salvarlo come MMAgent.ps1
4.	Modificare e quindi usare l'esempio seguente per installare l'agente usando DSC in Automazione di Azure. Importare MMAgent.ps1 in Automazione di Azure tramite l'interfaccia di Automazione di Azure o il cmdlet.
5.	Assegnare un nodo alla configurazione. Entro 15 minuti il nodo controllerà la configurazione e verrà effettuato il push di MMA al nodo.

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
        }

        xRemoteFile OIPackage {
            Uri = "https://opsinsight.blob.core.windows.net/publicfiles/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "E854571C-3C01-4128-99B8-52512F44E5E9"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## Configurare manualmente un agente
Se gli agenti sono stati installati ma non configurati o se sono state apportate modifiche alla configurazione, è possibile usare le informazioni seguenti per abilitarli o riconfigurarli. Dopo aver configurato l'agente, questo verrà registrato con il servizio agente e otterrà le informazioni di configurazione necessarie nonché i Management Pack contenenti informazioni sulla soluzione.

1. Dopo avere installato Microsoft Monitoring Agent, aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent** e fare clic sulla scheda **Connetti a Azure Operational Insights (OMS)**.   
3. Incollare l'**ID area di lavoro** e la **Chiave dell'area di lavoro (Chiave primaria)** copiati nel Blocco note nella procedura precedente e quindi fare clic su **OK**. ![Configurare Operational Insights](./media/log-analytics-windows-agents/oms-mma-aoi.png)

Dopo la raccolta dei dati dai computer monitorati dall'agente, il numero di computer monitorati da OMS verrà visualizzato nel portale di OMS nella scheda **Origini connesse** in **Impostazioni** come **Server connessi**


### Per disabilitare l'agente
1. Dopo aver installato l'agente, aprire il **Pannello di controllo**.
2. Aprire Microsoft Monitoring Agent e fare clic sulla scheda **Azure Operational Insights (OMS)**.
3. Deselezionare **Connect to Azure Operational Insights**.

## Configurare un agente usando la riga di comando

- È possibile usare Windows PowerShell con l'esempio seguente.
    ```
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableAzureOperationalInsights('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    ```

## Configurare facoltativamente gli agenti per l'invio di report a un gruppo di gestione di Operations Manager

Se si usa Operations Manager nell'infrastruttura IT, è anche possibile usare l'agente MMA come agente di Operations Manager.

### Per configurare gli agenti MMA per l'invio di report a un gruppo di gestione di Operations Manager
1.	Nel computer in cui è installato l'agente, aprire **Pannello di controllo**.
2.	Aprire **Microsoft Monitoring Agent** e fare clic sulla scheda **Operations Manager**. ![Microsoft Monitoring Agent scheda Operations Manager](./media/log-analytics-windows-agents/oms-mma-om01.png)
3.	Se i server di Operations Manager sono configurati per l'integrazione con Active Directory, fare clic su Aggiorna automaticamente assegnazioni gruppi di gestione da Servizi di dominio Active Directory.
4.	Fare clic su **Aggiungi** per aprire la finestra di dialogo **Aggiungi gruppo di gestione **. ![Microsoft Monitoring Agent Aggiungi gruppo di gestione](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.	In **Nome gruppo di gestione** digitare il nome del gruppo di gestione.
6.	Nella casella **Server di gestione primario** digitare il nome computer del server di gestione primario.
7.	Nella casella **Porta server di gestione** digitare il numero di porta TCP.
8.	In **Account azione agente** scegliere l'account di sistema locale o un account di dominio locale.
9.	Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi gruppo di gestione** e quindi fare clic su **OK** per chiudere la finestra di dialogo **Proprietà di Microsoft Monitoring Agent**.

## Configurare facoltativamente gli agenti per l'uso del server d'inoltro di Log Analytics di OMS

Se sono presenti server o client senza connessione a Internet, è comunque possibile configurarli per l'invio di dati al server d'inoltro di Log Analytics di OMS. Quando si usa il server d'inoltro, tutti i dati degli agenti vengono inviati tramite un singolo server con accesso a Internet. Il server d'inoltro trasferisce i dati dagli agenti direttamente a OMS senza analizzare i dati trasferiti.

Per altre informazioni sul server d'inoltro, inclusi il programma di installazione e la configurazione, vedere il blog relativo al [server d'inoltro di Log Analytics di OMS](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder).

Per informazioni su come configurare gli agenti per usare un server proxy, in questo caso il server d'inoltro di OMS, vedere [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

## Configurare facoltativamente le impostazioni di proxy e firewall
Se nell'ambiente sono presenti server proxy o firewall che limitano l'accesso a Internet, vedere [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md) per abilitare gli agenti alla comunicazione con il servizio OMS.

## Passaggi successivi

- [Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md) per aggiungere funzionalità e raccogliere i dati.
- [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md) se l'organizzazione usa un server proxy o un firewall per consentire agli agenti di comunicare con il servizio Log Analytics.

<!---HONumber=AcomDC_0504_2016-->