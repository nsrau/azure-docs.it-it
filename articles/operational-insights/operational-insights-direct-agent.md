<properties
	pageTitle="Connettere i computer direttamente a Operational Insights"
	description="È possibile connettere i computer direttamente a Operational Insights installando l'agente di Operational Insights in ogni computer da caricare."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/10/2015" 
	ms.author="banders"/>
# Connettere i computer direttamente a Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile connettere i computer direttamente a Operational Insights installando l'agente di Operational Insights in ogni computer da caricare.

> [AZURE.TIP]Per le macchine virtuali in esecuzione in Azure, installare l'agente seguendo i passaggi in [Analizzare i dati dai server in Microsoft Azure](operational-insights-analyze-data-azure.md)

## Scaricare e installare l'agente
Utilizzare le procedure seguenti per scaricare e installare l'agente di Operational Insights.

### Per scaricare il file di installazione dell'agente
1. Nel portale di Operations Insights, nella pagina **Overview** fare clic sul riquadro **Settings**. Scegliere la scheda **Connected Sources** nella parte superiore. ![pagina impostazioni](./media/operational-insights-direct-agent/direct-agent01.png)
2. In **Attach Servers Directly (64 bit)** fare clic sul pulsante Download Agent per scaricare il file di installazione.
3. Nella parte destra di **Workspace ID**, fare clic sull'icona di copia e incollare l'ID nel Blocco note.
4. Nella parte destra di **Primary Key**, fare clic sull'icona di copia e incollare l'ID nel Blocco note. ![pagina impostazioni](./media/operational-insights-direct-agent/direct-agent02.png)

### Per installare gli agenti con il programma di installazione
1. Eseguire il programma di installazione per installare l'agente in un computer da gestire.
2. Selezionare **Connect the agent to Microsoft Azure Operational Insights**, quindi fare clic su **Next**.
3. Quando richiesto, immettere i valori di **Workspace ID** e **Primary Key** copiati nel Blocco note nella procedura precedente.

4. Fare clic su **Next**. L'agente verifica che sia possibile connettersi a Operational Insights.
5. Al termine, l'**agente di gestione Microsoft** viene visualizzato nel **Pannello di controllo**.

### Per installare l'agente mediante la riga di comando
- Modificare e usare il seguente esempio per installare l'agente mediante la riga di comando. ```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Configurare Microsoft Monitoring Agent (facoltativo)
Usare le informazioni seguenti per abilitare un agente a comunicare direttamente con il servizio Microsoft Azure Operational Insights. Dopo aver configurato l'agente, questo verrà registrato con il servizio agente e otterrà le informazioni di configurazione necessarie nonché i Management Pack contenenti informazioni sulla soluzione.

Dopo la raccolta dei dati dai computer monitorati dall'agente, il numero di computer monitorati verrà visualizzato nel portale di Operational Insights nella scheda **Connected Sources** in **Settings** in **Attach Servers Directly (64 bit)** Nel portale di Operational Insights è possibile visualizzare le informazioni relative a dati e valutazioni per qualsiasi computer che invia dati.

Se necessario, è anche possibile disabilitare l'agente o abilitarlo, usando la riga di comando o uno script.

### Per configurare l'agente
1. Dopo avere installato **Microsoft Monitoring Agent**, aprire il **Pannello di controllo**.
2. Aprire Microsoft Monitoring Agent e fare clic sulla scheda Azure Operational Insights.
3. Selezionare **Connect to Azure Operational Insights**.
4. Nella casella **Workspace ID** incollare l'ID dell'area di lavoro fornito nel portale di Operational Insights.
5. Nella casella **Account Key**, incollare **Primary Key** dal portale di Operational Insights e quindi fare clic su **OK**.

### Per disabilitare l'agente
1. Dopo aver installato l'agente, aprire il **Pannello di controllo**.
2. Aprire Microsoft Monitoring Agent e fare clic sulla scheda **Azure Operational Insights**.
3. Deselezionare **Connect to Azure Operational Insights**.

### Per abilitare l'agente usando la riga di comando o uno script
- È possibile usare Windows PowerShell or o uno script VB script come quello nell'esempio seguente.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## Configurare le impostazioni di proxy e firewall (facoltativo)
Se l'ambiente include server proxy o firewall che limitano l'accesso a Internet, potrebbe essere necessario eseguire le procedure seguenti per abilitare Operations Manager o gli agenti alla comunicazione con il servizio Operational Insights.

- [Configurare le impostazioni di proxy e firewall (facoltativo)](operational-insights-proxy-firewall.md)

<!---HONumber=Sept15_HO3-->