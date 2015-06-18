<properties 
	pageTitle="Farm di SharePoint Server" 
	description="Descrive la nuova funzionalità Farm di SharePoint Server disponibile nel portale di anteprima di Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="josephd"/>

# Farm di SharePoint Server

Farm di SharePoint Server è una funzionalità del portale di anteprima di Microsoft Azure che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ciò consente di risparmiare una notevole quantità di tempo in caso si necessiti di una farm di SharePoint di base o a disponibilità elevata per un ambiente di sviluppo e test o in caso si stia valutando l'opportunità di usare SharePoint Server 2013 come soluzione per la collaborazione all'interno dell'organizzazione.

La farm di SharePoint di base è costituita da tre macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

È possibile usare questa configurazione di farm per un'installazione semplificata per lo sviluppo di app SharePoint o per la prima valutazione di SharePoint 2013.

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

È possibile usare questa configurazione di farm per testare carichi client più elevati, la disponibilità elevata del sito di SharePoint esterno e i gruppi di disponibilità AlwaysOn per una farm di SharePoint. È anche possibile usare questa configurazione per lo sviluppo di app SharePoint in un ambiente a disponibilità elevata.
 
Per le informazioni di configurazione dettagliate di entrambe queste farm, vedere [Dettagli di configurazione della farm di SharePoint Server](virtual-machines-sharepoint-farm-config-azure-preview.md).

## Procedura per la configurazione
 
Per creare la farm di SharePoint con il modello Farm di SharePoint Server, eseguire le operazioni seguenti:

1. Nel [portale di anteprima di Microsoft Azure](https://portal.azure.com/) fare clic su **Nuovo** > **Calcolo** > **Farm di SharePoint Server**. Se **Farm di SharePoint Server** non viene visualizzato, fare clic su **Nuovo** > **Calcolo** > **Azure Marketplace**, digitare **SharePoint** in **Cerca tutto** e quindi fare clic su **Farm di SharePoint Server**. 
2. Nel riquadro **Crea una farm di SharePoint** digitare il nome di un gruppo di risorse.
3. Digitare il nome utente e la password per l'account amministratore locale in ogni macchina virtuale della farm. Scegliere un nome e una password difficili da indovinare e trascriverli in un luogo sicuro.
4. Per abilitare la farm con disponibilità elevata, fare clic su **Abilita disponibilità elevata**.
5. Per configurare i controller di dominio, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), il nome del dominio radice della foresta (il valore predefinito è contoso.com) e le dimensioni dei controller di dominio (il valore predefinito è A1).
6. Per configurare i server SQL, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), le dimensioni dei server SQL (il valore predefinito è A5), il nome e la password dell'account di accesso al database (per impostazione predefinita l'account amministratore), il nome dell'account del servizio SQL Server (il valore predefinito è sqlservice) e la password (per impostazione predefinita è la stessa password dell'account amministratore).
7. Per configurare i server di SharePoint, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), le dimensioni dei server di SharePoint (il valore predefinito è A2), un account utente per SharePoint (il valore predefinito è sp_setup) e la relativa password, il nome dell'account della farm di SharePoint (il valore predefinito è sp_farm) e la relativa password e una passphrase per la farm di SharePoint. Per impostazione predefinita, in genere viene usata la password dell'amministratore per l'account utente, l'account della farm e la passphrase di SharePoint.
8. Per configurare le impostazioni facoltative per la rete virtuale, l'account di archiviazione o la diagnostica, fare clic sulla freccia corrispondente.
9. Per specificare la sottoscrizione, fare clic sulla freccia.
10. Al termine dell'operazione, scegliere **Crea**.

> [AZURE.NOTE]Nel controller di dominio per impostazione predefinita non sono installati gli strumenti di gestione di Active Directory. Per installarli, eseguire il comando **Install-WindowsFeature AD-Domain-Services -IncludeManagementTools** da un prompt dei comandi di Windows PowerShell a livello di amministratore nella macchina virtuale del controller di dominio.

## Eseguire l'accesso e gestire le farm di SharePoint

Le farm di SharePoint includono un endpoint preconfigurato per consentire il traffico Web non autenticato (porta TCP 80) al server Web SharePoint per un computer client connesso a Internet. Questo endpoint indirizza a un sito del team preconfigurato. Per accedere al sito del team, eseguire le operazioni seguenti:

1.	Nel portale di anteprima di Azure fare clic su **Sfoglia** e quindi su **Gruppi di risorse**. 
2.	Nell'elenco dei gruppi di risorse fare clic sul nome del gruppo di risorse della farm di SharePoint.
3.	Nel riquadro del gruppo di risorse della farm di SharePoint fare clic su **Cronologia distribuzioni**. 
4.	Nel riquadro **Cronologia distribuzioni** fare clic su **Microsoft.SharePointFarm**.
5.	Nel riquadro **Microsoft.SharePointFarm** selezionare l'URL nel campo SHAREPOINTSITEURL e copiarlo. 
6.	Nel browser Internet incollare l'URl nel campo dell'indirizzo.
7.	Quando richiesto, immettere le credenziali dell'account utente specificate al momento della creazione della farm.

Dal sito Amministrazione centrale di SharePoint è possibile configurare i siti personali, le applicazioni di SharePoint e altre funzionalità. Per altre informazioni, vedere [Configurare SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx). Per accedere al sito Amministrazione centrale di SharePoint, eseguire le operazioni seguenti:

1.	Nel portale di anteprima di Azure fare clic su **Sfoglia** e quindi su **Gruppi di risorse**. 
2.	Nell'elenco dei gruppi di risorse fare clic sul nome del gruppo di risorse della farm di SharePoint.
3.	Nel riquadro del gruppo di risorse della farm di SharePoint fare clic su **Cronologia distribuzioni**. 
4.	Nel riquadro **Cronologia distribuzioni** fare clic su **Microsoft.SharePointFarm**.
5.	Nel riquadro **Microsoft.SharePointFarm** selezionare l'URL nel campo SHAREPOINTCENTRALADMINURL e copiarlo. 
6.	Nel browser Internet incollare l'URl nel campo dell'indirizzo.
7.	Quando richiesto, immettere le credenziali dell'account utente specificate al momento della creazione della farm.


Note:

- il portale di anteprima di Azure crea le macchine virtuali all'interno della sottoscrizione corrente.
- Il portale di anteprima di Azure crea entrambe queste farm in una rete virtuale solo cloud con una presenza Web con connessione Internet. Non esiste alcuna connessione VPN o ExpressRoute da sito a sito alla rete dell'organizzazione. 
- È possibile amministrare questi server tramite connessioni Desktop Remoto. Per altre informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).


## Gestione risorse di Azure

La funzionalità Farm di SharePoint Server usa Gestione risorse di Azure e script per creare automaticamente le configurazioni dell'infrastruttura e dei server per le farm di SharePoint. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

## Risorse aggiuntive

[Dettagli di configurazione della farm di SharePoint Server](virtual-machines-sharepoint-farm-config-azure-preview.md)

[SharePoint nei servizi infrastruttura di Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

<!---HONumber=58--> 