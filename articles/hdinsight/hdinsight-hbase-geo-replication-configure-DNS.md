<properties 
   pageTitle="Configurare DNS tra due reti virtuali di Azure | Microsoft Azure" 
   description="Informazioni su come configurare le connessioni VPN e la risoluzione dei nomi di dominio tra due reti virtuali e come configurare la replica geografica di HBase" 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/08/2015"
   ms.author="jgao"/>

# Configurare DNS tra due reti virtuali di Azure

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 


Informazioni su come aggiungere e configurare i server DNS nelle reti virtuali di Azure per gestire la risoluzione dei nomi all'interno e tra le reti virtuali.

Questa esercitazione fa parte della seconda parte della [serie][hdinsight-hbase-geo-replication] sulla creazione di replica geografica di HBase.

- [Configurare una connessione VPN tra due reti virtuali di Azure][hdinsight-hbase-geo-replication-vnet]
- Configurare il server DNS tra reti virtuali (questa esercitazione)
- [Configurare la replica geografica di HBase][hdinsight-hbase-geo-replication]


Il diagramma seguente illustra le due reti virtuali create nell'articolo su come [configurare una connettività VPN tra due reti virtuali][hdinsight-hbase-geo-replication-vnet]\:

![Grafico della rete virtuale di replica di HBase in HDInsight][img-vnet-diagram]

##Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il seguente cmdlet:

		Add-AzureAccount

	Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

		Select-AzureSubscription <AzureSubscriptionName>

- **Due reti virtuali di Azure con connettività VPN**. Per istruzioni, vedere [Configurare una connessione VPN tra due reti virtuali di Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE]I nomi dei servizi Azure e i nomi delle macchine virtuali devono essere univoci. Il nome usato in questa esercitazione è Contoso-[servizio di Azure/nome macchina virtuale]-[EU/US]. Ad esempio, Contoso-VNet-EU è la rete virtuale di Azure nel data center in Europa settentrionale; Contoso-DNS-US è la macchina virtuale del server DNS nel data center degli Stati Uniti orientali. È necessario indicare dei nomi a scelta.
 
 
##Creare macchine virtuali di Azure da usare come server DNS

**Per creare una macchina virtuale all'interno di Contoso-VNet-EU, denominato Contoso-DNS-EU**

1.	Fare clic su **NUOVO**, **CALCOLO**, **MACCHINA VIRTUALE**, **DA RACCOLTA**.
2.	Fare clic su **Windows Server 2012 R2 Datacenter**.
3.	Digitare:
	- **NOME MACCHINA VIRTUALE**: Contoso-DNS-EU
	- **NUOVO NOME UTENTE**: 
	- **NUOVA PASSWORD**: 
4.	Digitare:
	- **SERVIZIO CLOUD**: creare un nuovo servizio cloud
	- **AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE**: (selezionare Contoso-VNet-EU)
	- **SUBNET DELLA RETE VIRTUALE**: Subnet-1
	- **ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente
	
		Il nome del servizio cloud sarà identico al nome della macchina virtuale. In questo caso, è Contoso-DNS-EU. Per le macchine virtuali successive, è possibile scegliere di usare lo stesso servizio cloud. Tutte le macchine virtuali nello stesso servizio cloud condividono la stessa rete virtuale e lo stesso suffisso di dominio.

		L'account di archiviazione viene usato per archiviare il file di immagine della macchina virtuale. 
	- **ENDPOINT**: (scorrere verso il basso e selezionare **DNS**) 

Dopo aver creato la macchina virtuale, individuare l'IP interno e l'IP esterno.

1.	Fare clic sul nome della macchina virtuale **Contoso-DNS-EU**.
2.	Fare clic su **Dashboard**.
3.	Annotare quanto segue:
	- INDIRIZZO IP VIRTUALE PUBBLICO (VIP)
	- INDIRIZZO IP INTERNO


**Per creare una macchina virtuale all'interno di Contoso-VNet-US, denominato Contoso-DNS-UE**

- Ripetere la stessa procedura per creare una macchina virtuale con i valori seguenti:
	- NOME MACCHINA VIRTUALE: Contoso-DNS-US
	- AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE: selezionare Contoso-VNet-US
	- SUBNET DELLA RETE VIRTUALE: Subnet-1
	- ACCOUNT DI ARCHIVIAZIONE: usare un account di archiviazione generato automaticamente
	- ENDPOINT: (selezionare DNS)

##Impostare gli indirizzi IP statici per le due macchine virtuali

Per i server DNS sono necessari gli indirizzi IP statici. Questo passaggio non può essere eseguito dal portale di Azure. Si dovrà usare Azure PowerShell.

**Per configurare l'indirizzo IP statico per le due macchine virtuali**

1. Aprire Windows PowerShell ISE.
2. Eseguire i cmdlet seguenti:  

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName è il nome del servizio cloud. Poiché il server DNS è la prima macchina virtuale del servizio cloud, il nome del servizio cloud è identico al nome della macchina virtuale.

	Potrebbe essere necessario aggiornare ServiceName e Name affinché corrispondano con i nomi a disposizione.


##Aggiungere il ruolo del server DNS alle due macchine virtuali

**Per aggiungere il ruolo del server DNS per Contoso-DNS-EU**

1.	Dal portale di Azure fare clic su **Macchine virtuali** a sinistra. 
2.	Fare clic su **Contoso-DNS-EU**.
3.	Fare clic su **DASHBOARD** nella parte superiore.
4.	Fare clic su **CONNETTI** in basso e seguire le istruzioni per la connessione alla macchina virtuale tramite RDP.
2.	All'interno della sessione RDP, fare clic sul pulsante di Windows nell'angolo inferiore sinistro per aprire la schermata Start.
3.	Fare clic sul riquadro **Server Manager**.
4.	Fare clic su **Aggiungi ruoli e funzionalità**.
5.	Fare clic su **Avanti**
6.	Selezionare **Installazione basata su ruoli o basata su funzionalità** e fare clic su **Avanti**.
7.	Selezionare la macchina virtuale DNS (deve essere già evidenziata) e quindi fare clic su **Avanti**.
8.	Selezionare **Server DNS**.
9.	Fare clic su **Aggiungi funzionalità** e quindi su **Continua**.
10.	Fare clic su **Avanti** tre volte e quindi fare clic su **Installa**. 

**Per aggiungere il ruolo del server DNS per Contoso-DNS-US**

- Ripetere i passaggi per aggiungere il ruolo DNS a **Contoso-DNS-US**.

##Assegnare i server DNS alle reti virtuali

**Per registrare i due server DNS**

1.	Dal portale di Azure fare clic su **NUOVO**, **SERVIZI DI RETE**, **RETE VIRTUALE**, **REGISTRA SERVER DNS**.
2.	Digitare:
	- **NOME**: Contoso-DNS-EU
	- **INDIRIZZO IP DEL SERVER DNS**: 10.1.0.4: l'indirizzo IP deve corrispondere all'indirizzo IP della macchina virtuale del server DNS.
	 
3.	Ripetere la procedura per registrare Contoso-DNS-US con le impostazioni seguenti:
	- **NOME**: Contoso-DNS-US
	- **INDIRIZZO IP DEL SERVER DNS**: 10.2.0.4

**Per assegnare i due server DNS a due reti virtuali**

1.	Fare clic su **Reti** dal riquadro sinistro nel portale di gestione.
2.	Fare clic su **Contoso-VNet-EU**.
3.	Fare clic su **CONFIGURA**.
4.	Selezionare **Contoso-DNS-EU** nella sezione del **server dns**.
5.	Fare clic su **SALVA** nella parte inferiore della pagina e quindi su **Sì** per confermare.
6.	Ripetere il processo per assegnare il server DNS **Contoso-DNS-US** alla rete virtuale **Contoso-VNet-US**.

Per aggiornare la configurazione del server DNS, è necessario riavviare tutte le macchine virtuali che sono state distribuite sulle reti virtuali.

**Per riavviare le macchine virtuali**

1. Dal portale di Azure fare clic su **Macchine virtuali** a sinistra.
2. Fare clic su **Contoso-DNS-EU**.
3. Fare clic su **Dashboard** nella parte superiore.
4. Fare clic su **RIAVVIA** nella parte inferiore.
5. Ripetere gli stessi passaggi per riavviare il computer **Contoso-DNS-US**.


##Configurare i server di inoltro condizionale DNS

Il server DNS su ogni rete virtuale può solo risolvere i nomi DNS all'interno della rete virtuale. È necessario configurare un server di inoltro condizionale in modo che punti al server DNS peer per le risoluzioni dei nomi nella rete virtuale peer.

Per configurare il server di inoltro condizionale, è necessario conoscere i suffissi di dominio dei due server DNS. I suffissi DNS possono essere diversi a seconda della configurazione di servizi cloud usata per creare le macchine virtuali. Per ogni suffisso DNS usato nella rete virtuale è necessario aggiungere un server di inoltro condizionale.

**Per trovare i suffissi di dominio dei due server DNS**

1. Effettuare una connessione RDP in **Contoso-DNS-EU**.
2. Aprire una console Windows PowerShell o un prompt dei comandi.
3. Eseguire **ipconfig** e annotare il **suffisso DNS specifico della connessione**.
4. Non chiudere la sessione RDP, sarà necessario usarla più avanti nell'esercitazione. 
5. Ripetere gli stessi passaggi per scoprire il **suffisso DNS specifico della connessione** di **Contoso-DNS-US**.


**Per configurare i server di inoltro DNS**
 
1.	Dalla sessione RDP a **Contoso-DNS-EU** fare clic sul tasto di Windows in basso a sinistra.
2.	Fare clic su **Strumenti di amministrazione**.
3.	Fare clic su **DNS**.
4.	Nel riquadro sinistro espandere **DSN**, **Contoso-DNS-EU**.
5.	Immettere le seguenti informazioni:
	- **Dominio DNS**: immettere il suffisso DNS di Contoso-DNS-US. Ad esempio: Contoso-DNS-US.b5.internal.cloudapp.net.
	- **Indirizzi IP dei server master**: immettere 10.2.0.4, ovvero l'indirizzo IP di Contoso-DNS-US.
6.	Premere **INVIO** e quindi fare clic su **OK**. A questo punto sarà possibile risolvere l'indirizzo IP di Contoso-DNS-US da Contoso-DNS-EU.
7.	Ripetere i passaggi per aggiungere un server di inoltro DNS al servizio DNS nella macchina virtuale di Contoso-DNS-US con i valori seguenti:
	- **Dominio DNS**: immettere il suffisso DNS di Contoso-DNS-EU. 
	- **Indirizzi IP dei server master**: immettere 10.2.0.4, ovvero l'indirizzo IP di Contoso-DNS-EU.

##Testare la risoluzione dei nomi tra le reti virtuali

A questo punto è possibile testare la risoluzione dei nomi tra le reti virtuali. Per impostazione predefinita il comando ping viene bloccato dal firewall. È possibile usare nslookup per risolvere le macchine virtuali del server DNS (è necessario usare FQDN) nelle reti peer.


##Passaggi successivi

In questa esercitazione si è appreso come configurare la risoluzione dei nomi tra reti virtuali con connessioni VPN. Gli altri due articoli della serie trattano gli argomenti seguenti:

- [Configurare una connessione VPN tra due reti virtuali di Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurare la replica geografica di HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: ../install-configure-powershell.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---HONumber=August15_HO6-->