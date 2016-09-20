<properties 
	pageTitle="Configurare una connessione da un indicizzatore di Ricerca di Azure in SQL Server in una macchina virtuale di Azure | Microsoft Azure | Indicizzatori" 
	description="Abilitare connessioni crittografate e configurare il firewall per consentire connessioni a SQL Server in una macchina virtuale (VM) Azure da un indicizzatore in Ricerca di Azure." 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>  

# Configurare una connessione da un indicizzatore di Ricerca di Azure a SQL Server in una VM Azure

Come indicato in [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), la creazione di indici su **SQL Server in VM di Azure** (o **VM SQL Azure** per brevità) è supportata da Ricerca di Azure, ma prima è necessario occuparsi di due prerequisiti riguardanti la sicurezza.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Abilitare le connessioni crittografate

Ricerca di Azure richiede una connessione sicura per leggere i dati dal database. Ciò significa che è necessario abilitare le connessioni crittografate nella VM di SQL Azure tramite la configurazione di un certificato SSL.

I passaggi per abilitare le connessioni crittografate a SQL Server sono documentati in [Abilitazione di connessioni crittografate al Motore di database](https://msdn.microsoft.com/library/ms191192.aspx), ma per le connessioni Internet pubbliche, ad esempio una connessione da Ricerca di Azure a una VM di SQL Azure, esistono alcuni requisiti aggiuntivi per il corretto funzionamento.

### Specificare un FQDN nel certificato SSL

Il nome dell'oggetto del certificato SSL deve essere un nome di dominio completo (o **FQDN**) della VM di SQL Azure. È lo stesso FQDN che si specificherà nella stringa di connessione del database durante la creazione di un'origine dati nel servizio di ricerca. Un FQDN è formattato come `<your-VM-name>.<region>.cloudapp.azure.com` per le VM di **Resource Manager**. Se ci si trova ancora in VM **classiche**, è formattato come `<your-cloud-service-name.cloudapp.net>`. È possibile trovare il nome di dominio completo della VM di SQL Azure come nome/etichetta DNS nel [portale Azure](https://portal.azure.com/).

### Usare REGEDIT per configurare il certificato SSL

Gestione configurazione SQL Server non è in grado di mostrare il certificato SSL del FQDN nell'elenco a discesa **Certificato** come descritto nella documentazione. La soluzione alternativa è configurare il certificato SSL modificando questa chiave di registro: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate**. La parte *[MSSQL13.MSSQLSERVER]* varia in base alla versione e al nome dell'istanza di SQL Server. Questa chiave deve essere aggiornata con l'**identificazione personale** del certificato SSL che è stato installato nella VM di SQL Azure.

### Concedere autorizzazioni all'account del servizio

Assicurarsi che l'account del servizio SQL Server riceva l'autorizzazione appropriata per la chiave privata del certificato SSL. Se si ignora questo passaggio, SQL Server non verrà avviato.

## Configurare il firewall per consentire le connessioni da Ricerca di Azure

Non è insolito configurare il firewall e il corrispondente endpoint o elenco di controllo di accesso (ACL) di Azure in modo da rendere accessibile la VM di Azure da parte di altri soggetti. Probabilmente questa configurazione è già stata eseguita per consentire alla logica applicativa di connettersi alla VM di SQL Azure. Non è diverso per una connessione di Ricerca di Azure alla VM di SQL Azure. Se non lo si è ancora fatto, ecco alcune procedure consigliate di sicurezza da tenere presenti.

Se si usano macchine virtuali di **Resource Manager**, vedere [Connect to a SQL Server Virtual Machine on Azure using Resource Manager](../virtual-machines/virtual-machines-windows-sql-connect.md) (Connettersi a una macchina virtuale SQL Server su Azure tramite Resource Manager). Se si usano ancora VM **classiche**, vedere [Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

### Limitare l'accesso all'indirizzo IP del servizio di ricerca

Per qualsiasi modello di distribuzione, quando si configurano le connessioni da Ricerca di Azure si consiglia di limitare l'accesso all'indirizzo IP del servizio di ricerca nell'elenco di controllo di accesso anziché aprire le VM di SQL Azure a tutte le richieste di connessione. È possibile trovare facilmente l'indirizzo IP eseguendo il ping del nome FQDN (ad esempio, `<your-search-service-name>.search.windows.net`) del servizio di ricerca.

### Configurare l'intervallo di indirizzi IP

Si noti che se il servizio di ricerca ha solo un'unità di ricerca (vale a dire una replica e una partizione), l'indirizzo IP può cambiare durante il riavvio del servizio di routine. Per evitare errori di connessione, è necessario specificare l'intervallo di indirizzi IP dell'area Azure in cui viene eseguito il provisioning del servizio di ricerca. L'elenco degli intervalli IP da cui gli indirizzi IP pubblici vengono allocati alle risorse di Azure è pubblicato negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Includere gli indirizzi IP del portale di Ricerca di Azure

Inoltre, se si usa il portale di Azure per creare un indicizzatore, anche la logica del portale di Ricerca di Azure deve poter accedere alla VM di SQL Azure durante la fase di creazione. Gli indirizzi IP del portale di Ricerca di Azure sono reperibile eseguendo il ping di `stamp1.search.ext.azure.com` e `stamp2.search.ext.azure.com`.

## Passaggi successivi

Dopo aver soddisfatto i precedenti requisiti di configurazione, è possibile specificare un SQL Server nella VM di Azure come origine dati per un indicizzatore di Ricerca di Azure. Per altre informazioni, vedere [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

<!---HONumber=AcomDC_0907_2016-->