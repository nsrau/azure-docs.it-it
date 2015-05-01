<properties 
   pageTitle="connect-scom" 
   description="Connettere i computer direttamente a Operational Insights" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Connettere i computer direttamente a Operational Insights 

È possibile connettere i computer direttamente a Operational Insights installando l'agente di Operational Insights in ogni computer da caricare. 


##Scaricare, installare e configurare l'agente
###Per scaricare il file di installazione dell'agente
1. Nel **portale di Operations Insights**, nella pagina **Overview** fare clic su **Servers and Usage**.
1. In **Servers connected directly** fare clic su **configure**.
1. Accanto ad **Add Agents**, fare clic sul collegamento Agent per scaricare il file di installazione.
1. Nella casella **Primary Workspace Key** selezionare la chiave e copiarla (CTRL+C).


### Per installare gli agenti con il programma di installazione
1. Eseguire il programma di installazione per installare l'agente in un computer da gestire.
1. Selezionare **Connect the agent to Microsoft Azure Operational Insights**, quindi fare clic su **Next**.
1. Quando richiesto, immettere le informazioni copiate nel passaggio 4.
1. Al termine, l'**agente di gestione Microsoft** viene visualizzato nel **Pannello di controllo**.

### Per installare l'agente mediante la riga di comando
Modificare e usare il seguente esempio per installare l'agente mediante la riga di comando.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Riconfigurare Microsoft Monitoring Agent (facoltativo)
Usare le informazioni seguenti per abilitare un agente a comunicare direttamente con il servizio Microsoft Azure Operational Insights. Dopo aver configurato l'agente, questo verrà registrato con il servizio agente e otterrà le informazioni di configurazione necessarie nonché i Management Pack contenenti le informazioni dell'Intelligence Pack.

Dopo la raccolta dei dati dai computer monitorati dall'agente, nella pagina Usage in **Directly Connected Agents** del portale di Operational Insights verrà visualizzato il numero di computer monitorati. Nel portale di Operational Insights è possibile visualizzare le informazioni relative a dati e valutazioni per qualsiasi computer che invia dati.

Se necessario, è anche possibile disabilitare l'agente o abilitarlo, usando la riga di comando o uno script.

### Per configurare l'agente
- Dopo avere installato **Microsoft Monitoring Agent**, aprire il **Pannello di controllo**.
- Aprire Microsoft Monitoring Agent e fare clic sulla scheda Azure Operational Insights.
- Selezionare **Connect to Azure Operational Insights**.
- Nella casella **Workspace ID** digitare l'ID dell'area di lavoro fornito nel portale di Operational Insights.
- Nella casella Account Key incollare il valore di **Primary Workspace Key** copiati durante l'installazione dell'agente e fare clic su **OK**.

### Per disabilitare l'agente
- Dopo aver installato l'agente, aprire il **Pannello di controllo**.
- Aprire Microsoft Monitoring Agent e fare clic sulla scheda **Azure Operational Insights**.
- Deselezionare **Connect to Azure Operational Insights**.

### Per abilitare l'agente usando la riga di comando o uno script
È possibile usare Windows PowerShell or o uno script VB script come quello nell'esempio seguente.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Configurare le impostazioni di proxy e firewall (facoltativo)
Se l'ambiente include server proxy o firewall che limitano l'accesso a Internet, potrebbe essere necessario eseguire le procedure seguenti per abilitare Operations Manager e/o gli agenti alla comunicazione con il servizio Operational Insights 



- [Configurare le impostazioni di proxy e firewall (facoltativo)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## Contenuti correlati

- [Post di blog: Connettere i server direttamente a Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Post di blog: Abilitare Operational Insights per Macchine virtuali di Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52-->