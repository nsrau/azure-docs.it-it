<properties
   pageTitle="Configurare una connessione da rete virtuale a rete virtuale | Microsoft Azure"
   description="Come connettere reti virtuali di Azure nelle stesse o diverse sottoscrizioni o aree mediante PowerShell e il portale di Azure classico. Questo articolo si applica alle reti virtuali create mediante il modello di distribuzione classico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/04/2015"
   ms.author="cherylmc"/>


# Configurare una connessione da rete virtuale a rete virtuale nel portale di Azure classico

> [AZURE.SELECTOR]
- [Azure Classic Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Questo articolo illustrerà la procedura per creare e connettere reti virtuali mediante il modello di distribuzione classica (noto anche come servizio di gestione). Questa procedura utilizzerà una combinazione del portale di Azure classico (non il portale di Azure) e dei cmdlet di PowerShell. Se si desidera connettere reti virtuali create mediante il modello di distribuzione di gestione risorse di Azure, vedere la scheda precedente. La procedura è diversa per ogni modello.

È inoltre possibile connettere una rete virtuale creata nel modello di distribuzione classica per una rete virtuale creata mediante il modello di Gestione risorse. Vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

La connessione di una rete virtuale a un'altra rete virtuale (da VNet a Vnet) è molto simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. Le reti virtuali possono trovarsi in diverse sottoscrizioni e aree geografiche diverse. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni multisito. In modo da definire topologie di rete che consentono di combinare più sedi locali connettività con connettività di rete virtuale tra, come illustrato nel diagramma riportato di seguito:

![Diagramma di connettività tra reti virtuali](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

## Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

- **Presenza e ridondanza in più aree geografiche**
	- È possibile impostare le proprie sincronizzazione o replica geografica con connettività sicura senza passare da endpoint con connessione internet.
	- Con il servizio di bilanciamento del carico di Azure e Microsoft o tecnologia di clustering di terze parti, è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è quella di configurare SQL Always On con gruppi di disponibilità distribuendo tra più aree di Azure.

- **Applicazioni multi livello in singole aree geografiche con alto grado di isolamento**
	- Nella stessa area, è possibile configurare applicazioni multilivello con più reti virtuali collegate tra loro forte isolamento e la comunicazione tra livelli sicura.

- **Comunicazione tra sottoscrizioni e organizzazioni in Azure**
	- Se si dispone di più sottoscrizioni di Azure, si possono connettere i carichi di lavoro da sottoscrizioni diverse tra loro in modo sicuro tra reti virtuali.
	- Per le aziende o i provider di servizi, è ora possibile abilitare la comunicazione tra organizzazioni con tecnologia VPN sicura in Azure.

## Domande frequenti relative alla connessione di reti virtuali

- Le reti virtuali possono essere nelle sottoscrizioni uguale o diverse.

- Le reti virtuali possono essere nelle sottoscrizioni uguale o diverse.

- Un servizio cloud o un endpoint di bilanciamento del carico non può estendersi tra reti virtuali, anche se sono connesse tra loro.

- Il collegamento di più reti virtuali non richiede alcun gateway VPN locale, a meno che la connettività cross-premise non sia obbligatoria.

- VNet to VNet supporta la connessione di reti virtuali di Azure. Non supporta le macchine virtuali o servizi non in una rete virtuale cloud.

- Per la connettività tra reti virtuali sono necessari gateway VPN di Azure e VPN con routing dinamico. Le VPN con routing statico di Azure non sono supportate.

- La connettività di rete virtuale può essere utilizzata contemporaneamente con VPN multisito, con un massimo di 10 tunnel VPN per gateway VPN di rete virtuale con connessione ad altre reti virtuali o a siti locali.

- Gli spazi degli indirizzi delle reti virtuali e dei siti di rete locali non devono sovrapporsi. Spazi degli indirizzi sovrapposti causerà la creazione di reti virtuali o caricare i file di configurazione netcfg esito negativo.

- Non sono supportati tunnel ridondanti tra una coppia di reti virtuali.

- Tutti i tunnel VPN della rete virtuale, tra cui reti VPN P2S, condividono la larghezza di banda disponibile sul gateway VPN di Azure e i tempi di gateway VPN stesso contratto di servizio in Azure.

- Il traffico da rete virtuale a rete virtuale scorre attraverso il backbone di Azure.

## Configurare una connessione tra reti virtuali

In questa procedura verrà esaminata si connette due reti virtuali, VNet1 e VNet2. È necessario avere una certa familiarità con la rete per sostituire gli intervalli di indirizzi IP sono compatibili con i requisiti di progettazione della rete. La connessione da una rete virtuale di Azure a un'altra rete virtuale di Azure è equivalente alla connessione a una rete locale tramite VPN da sito a sito (S2S).

Questa procedura viene usata principalmente nel portale di Azure classico, tuttavia è necessario usare i cmdlet di Microsoft Azure PowerShell per connettere i gateway VPN.

![Connessione tra reti virtuali](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

Sono disponibili 5 sezioni per pianificare e configurare. Configurare ogni sezione nell'ordine indicato di seguito:

1. [Pianificare gli intervalli di indirizzi IP](#plan-your-ip-address-ranges)
2. [Creare le reti virtuali](#create-your-virtual-networks)
3. [Reti locali](#add-local-networks)
4. [Creare il gateway con routing dinamico per ogni rete virtuale](#create-the-dynamic-routing-gateways-for-each-vnet)
5. [Connettere i gateway VPN](#connect-the-vpn-gateways)


## Pianificare gli intervalli di indirizzi IP

È importante definire gli intervalli che verrà utilizzato per configurare il file di configurazione di rete (netcfg). Dalla prospettiva di VNet1, VNet2 è semplicemente un'altra connessione VPN definita nella piattaforma Azure. E dalla prospettiva di VNet2, VNet1 è solo un'altra connessione VPN. Essi sono entrambi identificarsi come sito di rete locale. Tenere presente che è necessario assicurarsi che nessuno di intervalli di rete virtuale o intervalli di rete locale si sovrappongano in alcun modo.

Tabella 1 mostra un esempio di come definire le reti virtuali. Utilizzare gli intervalli di seguito come linea guida solo. Annotare gli intervalli che si utilizzeranno per le reti virtuali. Queste informazioni saranno necessarie per i passaggi successivi.

**Tabella 1**

|Rete virtuale |Definizione sito rete virtuale |Definizione sito rete virtuale|
|:----------------|:-------------------------------|:----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |VNet2 (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |VNet1 (10.1.0.0/16) |

## Creare le reti virtuali

Ai fini di questa esercitazione, verranno create due reti virtuali, VNet1 e VNet2. Quando si creano le reti virtuali, sostituire i valori. Ai fini di questa esercitazione, utilizzeremo i seguenti valori per le reti virtuali:

VNet1: Spazio di indirizzi = 10.1.0.0/16; Area = Stati Uniti occidentali

VNet2: Spazio di indirizzi = 10.2.0.0/16; Area = Giappone orientale

1. Accedere al **portale di Azure classico** (non al portale di Azure).

2. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.

Nella pagina **Dettagli della rete virtuale** immettere le informazioni seguenti.

  ![Dettagli della rete virtuale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Name -** assegnare un nome alla rete virtuale. Ad esempio, VNet1:
  - **Indirizzo** : quando si crea una rete virtuale viene associata a una località di Azure (area). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.



Nella pagina **Connettività VPN e server DNS** immettere le informazioni seguenti e quindi fare clic sulla freccia Avanti in basso a destra.

  ![Server DNS e connettività VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)


- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dall'elenco a discesa. Questa impostazione non crea un server DNS, consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale. Se si desidera disporre di risoluzione dei nomi tra le reti virtuali, sarà necessario configurare il server DNS, anziché utilizzare la risoluzione dei nomi fornita da Azure.

  - Non selezionare Nessuna delle caselle di controllo. Semplicemente fare clic sulla freccia in basso a destra per passare alla schermata successiva.

Nella pagina **Spazi di indirizzi della rete virtuale** specificare l'intervallo di indirizzi che si desidera usare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) che verranno assegnati per le macchine virtuali e altre istanze del ruolo da distribuire a questa rete virtuale. È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.


  ![Spazi di indirizzi della rete virtuale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  **Immettere le informazioni seguenti** e quindi fare clic sul segno di spunta in basso a destra per configurare la rete.

  - **Spazio degli indirizzi** inclusi IP iniziale e conteggio indirizzi. Verificare che gli spazi di indirizzi specificato non si sovrappongano a quelli degli spazi di indirizzi presenti nella rete locale. In questo esempio utilizzeremo 10.1.0.0/16 per VNet1.
  - **Aggiungi subnet** inclusi IP iniziale e conteggio indirizzi. Non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.

**Fare clic sul segno di spunta** in basso a destra nella pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, in *Stato* verrà visualizzato *Creato* nella pagina *Reti* del portale di Azure classico.

## Creare un’ulteriore rete virtuale

Successivamente, ripetere i passaggi precedenti per creare un'altra rete virtuale. In questo esercizio, si connetteranno queste due reti virtuali. Si noti che è molto importante che gli spazi degli indirizzi non siano duplicati o sovrapposti. Ai fini di questa esercitazione, è possibile utilizzare questi valori:

- **VNet2**
- **Spazio degli indirizzi**: 10.2.0.0/16
- **Area** = Giappone orientale

## Reti locali

Quando si crea una configurazione da rete virtuale a rete virtuale, è necessario configurare ciascuna rete virtuale in modo che si identifichino reciprocamente come sito di rete locale. In questa sezione si configurerà ciascuna rete virtuale come rete locale. Se sono già state configurate reti virtuali in precedenza, l'operazione è simile all'aggiunta di reti locali nel portale di Azure classico.

1. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Reti locali**

2. Nella pagina **Specificare i dettagli della rete locale** immettere in **Nome** il nome della rete virtuale che si desidera usare nella configurazione da rete virtuale a rete virtuale. In questo esempio verrà utilizzato 1 rete virtuale, come abbiamo VNet2 dovrà puntare a questa rete virtuale per la configurazione.

  Per indirizzo IP dispositivo VPN, è possibile utilizzare qualsiasi indirizzo IP. In genere, si utilizzerà l'indirizzo IP esterno effettivo di un dispositivo VPN. Per le configurazioni da rete virtuale a rete virtuale, si utilizza l'indirizzo IP del gateway. Tuttavia, dato che non è stato creato il gateway ancora, utilizziamo l'indirizzo IP specificato qui come segnaposto. Si verrà quindi tornare in queste impostazioni e configurare con i corrispondenti indirizzi IP gateway una volta generato da Azure.

3. Nel **specificare la pagina indirizzo**, si inserirà il IP indirizzo intervallo e indirizzo conteggio effettivo per VNet1. Deve corrispondere esattamente all'intervallo specificato in precedenza per VNet1.

4. Dopo aver configurato VNet1 come rete locale, tornare indietro e configurare VNet2 utilizzando i valori corrispondano.

5. È ora verrà puntare ogni rete virtuale per l'altro come una rete locale.È ora verrà puntare ogni rete virtuale per l'altro come una rete locale. Nel portale di Azure classico, passare alla pagina **Configura** per VNet1. In **connettività da sito a sito**, selezionare **connessione alla rete locale**, quindi selezionare **VNET2** come rete locale.

  ![Connetti alla rete locale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736058.jpg)

6. Nel **spazi di indirizzi della rete virtuale** nella stessa pagina fare clic su **Aggiungi subnet gateway**, quindi fare clic sui **salvare** icona nella parte inferiore della pagina per salvare la configurazione.

7. Ripetere il passaggio per vnet2 in modo da specificare VNet1 come rete locale.

## Creare il gateway con routing dinamico per ogni rete virtuale

Ora che avete ogni rete virtuale è configurata, è possibile configurare i gateway.

1. Nel **reti** verificare che la colonna di stato per la rete virtuale è **creato**.

2. Nel **nome** colonna, fare clic sul nome della rete virtuale.

3. Nel **Dashboard** pagina, si noti che la rete virtuale non è ancora un gateway configurato. Verrà visualizzato lo stato mentre si eseguono i passaggi necessari per configurare il gateway.

4. Fare clic su **CREA GATEWAY** nella parte inferiore della pagina.

  È necessario selezionare **il Routing dinamico**. Quando viene richiesto di confermare che si desidera procedere con la creazione del gateway, fare clic su Sì.

  ![Tipo gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. Si noti la rappresentazione grafica del gateway nella pagina diventa gialla con il testo Creazione del gateway. La creazione del gateway richiede in genere circa 15 minuti.

6. Ripetere gli stessi passaggi per l'altra rete virtuale, assicurarsi di selezionare **Gateway dinamico**. Non è necessario che il gateway di rete virtuale primo di completare prima di iniziare a creare il secondo.

7. Quando lo stato del gateway diventa Connessione in corso, l'indirizzo IP di ogni gateway sarà visibile nel dashboard. Annotare l'indirizzo IP corrispondente a ciascuna rete virtuale, avendo cura di non confonderli. Si tratta degli indirizzi IP che verranno usati durante la modifica degli indirizzi IP segnaposto per il dispositivo VPN nelle **Reti locali**.

## Modificare la rete locale

1. Nel **reti locali** pagina fare clic sul nome della rete locale che si desidera modificare, quindi fare clic su **Modifica** nella parte inferiore della pagina. Per **indirizzo IP dispositivo VPN**, immettere l'indirizzo IP del gateway corrispondente per la rete virtuale. Ad esempio, per VNet1, immettere l'indirizzo IP di gateway assegnato a VNet1. Fare clic sulla freccia nella parte inferiore della pagina.

2. Nel **specificare lo spazio degli indirizzi** pagina fare clic sul segno di spunta in basso a destra senza apportare alcuna modifica.

## Connettere i gateway VPN

Quando tutti i passaggi precedenti sono stati completati, si imposteranno le chiavi già condivise IPsec/IKE lo stesso. A tale scopo, utilizzare un'API REST o i cmdlet di PowerShell. Se si utilizza PowerShell, verificare di avere la versione più recente dei cmdlet di Microsoft Azure PowerShell. Gli esempi seguenti utilizzano cmdlet PowerShell per impostare il valore della chiave su A1b2C3D4. Si noti che entrambi utilizzano lo stesso valore di chiave. Modificare gli esempi seguenti in modo da riflettere i valori personalizzati.

Per VNet1

	PS C:\> Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2 -SharedKey A1b2C3D4

Per VNet2

	PS C:\> Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1 -SharedKey A1b2C3D4

Attendere l'inizializzazione delle connessioni. Dopo l'inizializzazione, il gateway assume l'aspetto illustrato nella figura seguente e le reti virtuali vengono connesse.

![Stato del gateway - connesso](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## Passaggi successivi


Se si desidera aggiungere macchine virtuali alla rete virtuale, vedere [Come creare una macchina virtuale](../virtual-machines/virtual-machines-windows-tutorial-classic-portal.md).

Per altre informazioni sullo schema di configurazione, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Per informazioni sull’API REST, vedere [Operazioni sui Gateway di rete virtuale](https://msdn.microsoft.com/library/azure/jj154113.aspx).

Per altre informazioni sui Gateway VPN, vedere [Domande frequenti sul Gateway VPN](vpn-gateway-vpn-faq.md).

Per altre informazioni sulle reti virtuali, vedere la [Panoramica sulla rete virtuale](../virtual-network/virtual-networks-overview.md) e le [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md).


[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=AcomDC_1203_2015-->