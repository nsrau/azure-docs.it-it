<properties title="SharePoint Server Farm" pageTitle="Farm di SharePoint Server" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="josephd" />

#Farm di SharePoint Server#

Farm di SharePoint Server è una funzionalità del portale di anteprima di Microsoft Azure che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ciò consente di risparmiare una notevole quantità di tempo in caso si necessiti di una farm di SharePoint di base o a disponibilità elevata per un ambiente di sviluppo e test o in caso si stia valutando l'opportunità di usare SharePoint Server 2013 come soluzione per la collaborazione all'interno dell'organizzazione.

La farm di SharePoint di base è costituita da tre macchine virtuali nella configurazione seguente:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

È possibile usare questa configurazione di farm per un'installazione semplificata per lo sviluppo di app SharePoint o per la prima valutazione di SharePoint 2013.

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali nella configurazione seguente:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

È possibile usare questa configurazione di farm per testare carichi client più elevati, la disponibilità elevata del sito di SharePoint esterno e i gruppi di disponibilità AlwaysOn per una farm di SharePoint. È anche possibile usare questa configurazione per lo sviluppo di app SharePoint in un ambiente a disponibilità elevata.
 
Per le informazioni di configurazione dettagliate di entrambe queste farm, vedere [Dettagli di configurazione della farm di SharePoint Server](../virtual-machines-sharepoint-farm-config-azure-preview/).

Queste farm includono un endpoint preconfigurato per consentire il traffico Web autenticato (porta TCP 80) al server Web SharePoint tramite un computer client connesso a Internet. Questo endpoint indirizza a un sito del team preconfigurato. Per accedere al sito del team, eseguire le operazioni seguenti:

1.	Nel portale di anteprima di Azure fare clic su **Sfoglia** e quindi su **Gruppi di risorse**. 
2.	Nell'elenco dei gruppi di risorse fare clic sul nome del gruppo di risorse della farm di SharePoint.
3.	Nel riquadro del gruppo di risorse della farm di SharePoint fare clic su **Cronologia distribuzioni**. 
4.	Nel riquadro **Cronologia distribuzioni** fare clic su **Microsoft.SharePointFarm**.
5.	Nel riquadro **Microsoft.SharePointFarm** selezionare l'URL nel campo SHAREPOINTSITEURL e copiarlo. 
6.	Nel browser Internet incollare l'URl nel campo dell'indirizzo.
7.	Quando richiesto, immettere le credenziali dell'account utente specificate al momento della creazione della farm.

Dal sito Amministrazione centrale di SharePoint è possibile configurare i siti personali, le applicazioni di SharePoint e altre funzionalità. Per altre informazioni, vedere [Configurare SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).  Per accedere al sito Amministrazione centrale di SharePoint, eseguire le operazioni seguenti:

1.	Nel portale di anteprima di Azure fare clic su **Sfoglia** e quindi su **Gruppi di risorse**. 
2.	Nell'elenco dei gruppi di risorse fare clic sul nome del gruppo di risorse della farm di SharePoint.
3.	Nel riquadro del gruppo di risorse della farm di SharePoint fare clic su **Cronologia distribuzioni**. 
4.	Nel riquadro **Cronologia distribuzioni** fare clic su **Microsoft.SharePointFarm**.
5.	Nel riquadro **Microsoft.SharePointFarm** selezionare l'URL nel campo SHAREPOINTCENTRALADMINURL e copiarlo. 
6.	Nel browser Internet incollare l'URl nel campo dell'indirizzo.
7.	Quando richiesto, immettere le credenziali dell'account utente specificate al momento della creazione della farm.


Note:

- il portale di anteprima di Azure crea le macchine virtuali all'interno della sottoscrizione corrente.
- Il portale di anteprima di Azure crea entrambe queste farm in una rete virtuale solo cloud con una presenza Web con connessione Internet. Non esiste alcuna connessione VPN da sito a sito alla rete dell'organizzazione. 
- È possibile amministrare questi server tramite connessioni Desktop remoto.

##Procedura per la configurazione##

Per creare la farm di Sharepoint con la funzionalità Farm di SharePoint, eseguire le operazioni seguenti:

1. Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Macchine virtuali** > **Farm di SharePoint Server**.
2. Nel riquadro **Crea una farm di SharePoint** digitare il nome di un gruppo di risorse.
3. Digitare il nome utente e la password per l'account amministratore locale in ogni macchina virtuale della farm. Scegliere un nome e una password difficili da indovinare.
4. Per abilitare la farm con disponibilità elevata, fare clic su **Abilita disponibilità elevata**.
5. Per configurare i controller di dominio, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), il nome del dominio radice della foresta (il valore predefinito è contoso.com) e le dimensioni dei controller di dominio (il valore predefinito è A1).
6. Per configurare i server SQL, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), le dimensioni dei server SQL (il valore predefinito è A5), il nome e la password dell'account di accesso al database (per impostazione predefinita l'account amministratore), il nome dell'account del servizio SQL Server (il valore predefinito è sqlservice) e la password (per impostazione predefinita è la stessa password dell'account amministratore).
7. Per configurare i server di SharePoint, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), le dimensioni dei server di SharePoint (il valore predefinito è A3), un account utente per SharePoint (il valore predefinito è sp_setup) e la relativa password, il nome dell'account della farm di SharePoint (il valore predefinito è sp_farm) e la relativa password e una passphrase per la farm di SharePoint. Per impostazione predefinita, in genere viene usata la password dell'amministratore per l'account utente, l'account della farm e la passphrase di Sharepoint.
8. Per configurare le impostazioni facoltative, ad esempio la rete virtuale, l'account di archiviazione o la diagnostica, fare clic sulla freccia.
9. Per specificare la sottoscrizione, fare clic sulla freccia.
10. Per specificare la località (area geografica), fare clic sulla freccia.
11. Al termine dell'operazione, scegliere **Crea**.

##Gestione risorse di Azure##

La funzionalità Farm di SharePoint Server usa Gestione risorse di Azure e script per creare automaticamente le configurazioni dell'infrastruttura e dei server per le farm di SharePoint. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](http://azure.microsoft.com/it-it/documentation/articles/powershell-azure-resource-manager/).


<!--HONumber=35_1-->
