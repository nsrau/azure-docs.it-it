<properties
	pageTitle="Estendere i gruppi di disponibilità AlwaysOn locali ad Azure | Microsoft Azure"
	description="Questa esercitazione sfrutta le risorse create con il modello di distribuzione classica e descrive come usare la procedura guidata Aggiungi replica in SQL Server Management Studio (SSMS) per aggiungere una replica del gruppo di disponibilità AlwaysOn in Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/12/2016"
	ms.author="MikeRayMSFT" />

# Estendere i gruppi di disponibilità AlwaysOn locali ad Azure

I gruppi di disponibilità AlwaysOn garantiscono un disponibilità elevata per i gruppi di database grazie all'aggiunta di repliche secondarie, che consentono il failover dei database in caso di errore. Le repliche possono essere usate anche per l'offload di carichi di lavoro di lettura o attività di backup.

È possibile estendere i gruppi di disponibilità locale a Microsoft Azure eseguendo il provisioning di una o più macchine virtuali di Azure con SQL Server e quindi aggiungendole come repliche ai gruppi di disponibilità locale.

L'esercitazione presuppone che l'utente disponga degli elementi seguenti:

- Una sottoscrizione di Azure attiva. È possibile [iscriversi per una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un gruppo di disponibilità AlwaysOn esistente in locale. Per altre informazioni sui gruppi di disponibilità, vedere [Gruppi di disponibilità AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx).

- Connettività tra la rete locale e la rete virtuale di Azure. Per altre informazioni sulla creazione della rete virtuale, vedere [Configurare una VPN da sito a sito nel portale di Azure classico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Procedura guidata Aggiungi replica di Azure

Questa sezione mostra come usare la **procedura guidata per l'aggiunta della replica di Azure** per estendere il gruppo di disponibilità AlwaysOn in modo da includere le repliche di Azure.

1. In SQL Server Management Studio espandere **Disponibilità elevata AlwaysOn** > **Gruppi di disponibilità** > **[nome del gruppo di disponibilità]**.

1. Fare doppio clic su **Repliche di disponibilità**, quindi fare clic su **Aggiungi Replica**.

1. Per impostazione predefinita, viene visualizzata la **procedura guidata Aggiungi replica di Azure**. Fare clic su **Avanti**. Se durante un precedente avvio della procedura guidata è stata selezionata l'opzione **Non visualizzare più questa pagina** nella parte inferiore della pagina, questa schermata non verrà visualizzata.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Verrà richiesto di connettersi a tutte le repliche secondarie esistenti. È possibile fare clic su **Connetti** accanto a ciascuna replica oppure su **Connetti tutto** nella parte inferiore della schermata. Dopo l'autenticazione, fare clic su **Avanti** per passare alla schermata successiva.

1. Nella parte superiore della pagina **Specifica repliche** sono disponibili più schede: **Repliche**, **Endpoint**, **Preferenze di backup** e **Listener**. Nella scheda **Repliche** fare clic su **Aggiungi replica di Azure** per avviare la procedura guidata Aggiungi replica di Azure.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Selezionare un certificato di gestione di Azure esistente dall'archivio certificati di Windows locale, se in precedenza ne è già stato installato uno. Selezionare o immettere l'ID di una sottoscrizione di Azure, se invece non ne è mai stato usato uno. È possibile fare clic su Download per scaricare e installare un certificato di gestione di Azure e scaricare l'elenco delle sottoscrizioni tramite un account di Azure.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. In questa pagina è necessario immettere in ciascun campo i valori richiesti che verranno usati per creare la macchina virtuale di Azure.

	|Impostazione|Descrizione|
|---|---|
|**Immagine**|Selezionare la combinazione di sistema operativo e SQL Server desiderata.|
|**Dimensioni macchina virtuale**|Selezionare le dimensioni della macchina virtuale più adatte alle esigenze aziendali.|
|**Nome macchina virtuale**|Specificare un nome univoco per la nuova macchina virtuale. Il nome deve contenere un numero di caratteri compreso tra 3 e 15, può essere composto solo da lettere, numeri e trattini e deve iniziare con una lettera e terminare con una lettera o con un numero.|
|**Nome utente macchina virtuale**|Specificare il nome utente che costituirà l'account amministratore della macchina virtuale.|
|**Password amministratore macchina virtuale**|Specificare una password per il nuovo account.|
|**Conferma password**|Confermare la password del nuovo account.|
|**Rete virtuale**|Specificare la rete virtuale di Azure che deve essere usata dalla nuova macchina virtuale. Per altre informazioni sulle reti virtuali, vedere la pagina di [panoramica sulle reti virtuali](../virtual-network/virtual-networks-overview.md).|
|**Rete virtuale/subnet**|Specificare la subnet della rete virtuale che deve essere usata dalla nuova macchina virtuale.|
|**Dominio**|Verificare che il valore presente nel campo relativo al dominio sia corretto.|
|**Nome utente dominio**|Specificare un account contenuto nel gruppo Administrators locale nei nodi cluster locali.|
|**Password**|Specificare la password per il nome utente di dominio.|

1. Fare clic su **OK** per convalidare le impostazioni di distribuzione.

1. In seguito vengono visualizzate le condizioni di licenza. Leggerne il contenuto e fare clic su **OK** per accettare le condizioni di licenza.

1. Verrà nuovamente visualizzata la pagina **Specifica repliche**. Verificare le impostazioni relative alla nuova replica di Azure nelle schede **Repliche**, **Endpoint** e **Preferenze di backup**. Modificare le impostazioni per soddisfare i requisiti aziendali. Per altre informazioni sui parametri contenuti in queste schede, vedere [Pagina Specifica repliche (Creazione guidata Gruppo di disponibilità/Aggiungi replica)](https://msdn.microsoft.com/library/hh213088.aspx). Tenere presente che non è possibile creare listener tramite la scheda Listener per gruppi di disponibilità che contengono repliche di Azure. Se, inoltre, è stato creato un listener prima dell'avvio della procedura guidata, verrà ricevuto un messaggio che avvisa che tale listener non è supportato in Azure. La sezione **Creare un listener del gruppo di disponibilità** illustra come creare un listener.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Fare clic su **Avanti**.

1. Selezionare il metodo di sincronizzazione dei dati che si desidera usare nella pagina **Seleziona sincronizzazione dati iniziale** e fare clic su **Avanti**. Nella maggior parte degli scenari, selezionare **Sincronizzazione dati completa**. Per altre informazioni sui metodi di sincronizzazione dei dati, vedere [Pagina Seleziona sincronizzazione dati iniziale (procedure guidate Gruppi di disponibilità AlwaysOn)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Verificare i risultati nella pagina di **convalida**. Correggere i problemi in sospeso e, se necessario, eseguire nuovamente la convalida. Fare clic su **Avanti**.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Verificare le impostazioni nella pagina **Riepilogo**, quindi fare clic su **Fine**.

1. Verrà avviato il processo di provisioning. Al termine della procedura guidata, fare clic su **Chiudi** per uscire.

>[AZURE.NOTE] Durante la procedura guidata Aggiungi replica di Azure viene creato un file di log in Utenti\\Nome utente\\AppData\\Local\\SQL Server\\AddReplicaWizard. Questo file può essere usato per risolvere i problemi relativi alle distribuzioni delle repliche di Azure. Se una qualsiasi azione della procedura guidata ha esito negativo, verrà eseguito il rollback di tutte le operazioni precedenti, inclusa l'eliminazione della macchina virtuale di cui è stato eseguito il provisioning.

## Creare un listener del gruppo di disponibilità

Dopo aver creato il gruppo di disponibilità, è necessario creare un listener che consenta ai client di connettersi alle repliche. I listener indirizzano le connessioni in ingresso alla replica primaria o a una replica secondaria di sola lettura. Per altre informazioni sui listener, vedere [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## Passaggi successivi

Oltre a usare la **procedura guidata per l'aggiunta della replica di Azure** per estendere ad Azure il gruppo di disponibilità AlwaysOn, è anche possibile spostare completamente in Azure alcuni carichi di lavoro di SQL Server. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0713_2016-->