<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# Farm di SharePoint Server

Farm di SharePoint Server è una funzionalità del portale di anteprima di Microsoft Azure che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ciò consente di risparmiare una notevole quantità di tempo in caso si necessiti di una farm di SharePoint di base o a disponibilità elevata per un ambiente di sviluppo e test o in caso si stia valutando l'opportunità di usare SharePoint Server 2013 come soluzione per la collaborazione all'interno dell'organizzazione.

La farm di SharePoint di base è costituita da tre macchine virtuali nella configurazione seguente:

![sharepointfarm][]

È possibile usare questa configurazione di farm per un'installazione semplificata per lo sviluppo di app SharePoint o per la prima valutazione di SharePoint 2013.

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali nella configurazione seguente:

![sharepointfarm][1]

È possibile usare questa configurazione di farm per testare carichi client più elevati, la disponibilità elevata del sito di SharePoint esterno e i gruppi di disponibilità AlwaysOn per una farm di SharePoint. È anche possibile usare questa configurazione per lo sviluppo di app SharePoint in un ambiente a disponibilità elevata.

Per le informazioni di configurazione dettagliate di entrambe queste farm, vedere [Dettagli di configurazione della farm di SharePoint Server][].

Queste farm includono un endpoint preconfigurato per consentire il traffico Web autenticato (porta TCP 80) al server Web SharePoint tramite un computer client connesso a Internet. Questo endpoint indirizza a un sito del team preconfigurato. Per accedere al sito del team, è prima necessario ottenere il nome di dominio del server Web della farm di SharePoint:

1.  Nel portale di anteprima di Azure fare clic su **Sfoglia** e quindi su **Macchine virtuali**.
2.  Nell'elenco di macchine virtuali fare clic sul nome del server Web in uso (termina con SP, WEB1 o WEB2).
3.  Nel riquadro per la macchina virtuale del server Web fare clic su **Proprietà**.
4.  Prendere nota del nome di dominio completo in **Nome di dominio**.
5.  Dal browser Internet accedere all'URL **[http://[nome di dominio completo]][]**. Ad esempio, per il nome di dominio spfarm1-web1-contoso.cloudpapp.net, l'URL è **<http://spfarm1-web1-contoso.cloudpapp.net>**.
6.  Quando richiesto, immettere le credenziali dell'account utente specificate al momento della creazione della farm.

Dal sito Amministrazione centrale di SharePoint è possibile configurare i siti personali, le applicazioni di SharePoint e altre funzionalità. Per altre informazioni, vedere [Configurare SharePoint 2013][]. Per accedere al sito Amministrazione centrale di SharePoint, è prima necessario ottenere il relativo numero di porta esterna.

1.  Nel portale di anteprima di Azure fare clic su **Sfoglia** e quindi su **Macchine virtuali**.
2.  Nell'elenco di macchine virtuali fare clic sul nome del server SharePoint per la configurazione di base (termina con SP) o sul nome dei server applicazioni per la configurazione a disponibilità elevata (termina con APP1 o APP2).
3.  Nel riquadro per la macchina virtuale fare clic su **Proprietà**.
4.  Prendere nota del nome di dominio completo in **Nome di dominio**.
5.  Espandere il riquadro **Proprietà**.
6.  Nel riquadro per la macchina virtuale scorrere verso il basso fino all'elenco di **Endpoint**.
7.  Prendere nota del numero di porta per l'endpoint denominato SPCentralAdmin.
8.  Dal browser Internet accedere all'URL **[http://[nome di dominio completo]][]:[numero porta]**. Ad esempio, per il nome di dominio spfarm1-app1-contoso.cloudpapp.net e per il numero di porta esterna 54398, l'URL è **<http://spfarm1-app1-contoso.cloudpapp.net:54398>**.
9.  Quando richiesto, immettere le credenziali dell'account utente specificate al momento della creazione della farm.

Note:

-   il portale di anteprima di Azure crea le macchine virtuali all'interno della sottoscrizione corrente.
-   Il portale di anteprima di Azure crea entrambe queste farm in una rete virtuale solo cloud con una presenza Web con connessione Internet. Non esiste alcuna connessione VPN da sito a sito alla rete dell'organizzazione.
-   È possibile amministrare questi server tramite connessioni Desktop remoto.

## Procedura per la configurazione

Per creare la farm di Sharepoint con la funzionalità Farm di SharePoint, eseguire le operazioni seguenti:

1.  Nel [portale di anteprima di Azure][] fare clic su **Macchine virtuali** \> **Farm di SharePoint Server**.
2.  Nel riquadro **Crea una farm di SharePoint** digitare il nome di un gruppo di risorse.
3.  Digitare il nome utente e la password per l'account amministratore locale in ogni macchina virtuale della farm. Scegliere un nome e una password difficili da indovinare.
4.  Per abilitare la farm con disponibilità elevata, fare clic su **Abilita disponibilità elevata**.
5.  Per configurare i controller di dominio, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), il nome del dominio radice della foresta (il valore predefinito è contoso.com) e le dimensioni dei controller di dominio (il valore predefinito è A1).
6.  Per configurare i server SQL, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), le dimensioni dei server SQL (il valore predefinito è A5), il nome e la password dell'account di accesso al database (per impostazione predefinita l'account amministratore), il nome dell'account del servizio SQL Server (il valore predefinito è sqlservice) e la password (per impostazione predefinita è la stessa password dell'account amministratore).
7.  Per configurare i server di SharePoint, fare clic sulla freccia. È possibile specificare un prefisso di nome host (il valore predefinito è il nome del gruppo di risorse), le dimensioni dei server di SharePoint (il valore predefinito è A23), un account utente per SharePoint (il valore predefinito è sp\_setup) e la relativa password, il nome dell'account della farm di SharePoint (il valore predefinito è sp\_farm) e la relativa password e una passphrase per la farm di SharePoint. Per impostazione predefinita, in genere viene usata la password dell'amministratore per l'account utente, l'account della farm e la passphrase di Sharepoint.
8.  Per configurare le impostazioni facoltative, ad esempio la rete virtuale, l'account di archiviazione o la diagnostica, fare clic sulla freccia.
9.  Per specificare la sottoscrizione, fare clic sulla freccia.
10. Per specificare la località (area geografica), fare clic sulla freccia.
11. Al termine dell'operazione, scegliere **Crea**.

## Gestione risorse di Azure

La funzionalità Farm di SharePoint Server usa Gestione risorse di Azure e script per creare automaticamente le configurazioni dell'infrastruttura e dei server per le farm di SharePoint. Per altre informazioni, vedere [Utilizzo di Windows PowerShell con Gestione risorse][].

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [Dettagli di configurazione della farm di SharePoint Server]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  [http://[nome di dominio completo]]: http://[FQDN]
  [Configurare SharePoint 2013]: http://technet.microsoft.com/library/ee836142.aspx
  [portale di anteprima di Azure]: https://portal.azure.com/
  [Utilizzo di Windows PowerShell con Gestione risorse]: http://azure.microsoft.com/en-us/documentation/articles/powershell-azure-resource-manager/
