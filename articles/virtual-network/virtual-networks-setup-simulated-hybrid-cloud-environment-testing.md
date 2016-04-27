<properties 
	pageTitle="Ambienti di test cloud ibridi simulati | Microsoft Azure" 
	description="Creare un ambiente cloud ibrido simulato per test per professionisti IT o test di sviluppo, usando due reti virtuali di Azure e una connessione da rete virtuale a rete virtuale." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/28/2016" 
	ms.author="josephd"/>

# Impostare un ambiente cloud ibrido simulato per i test (modalità di distribuzione classica)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](../virtual-machines/virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).

Questo articolo è una guida alla creazione di un ambiente cloud ibrido simulato con Microsoft Azure per eseguire i test che usa due reti virtuali di Azure separate. Usare questa configurazione come alternativa a [ Impostazione di un ambiente cloud ibrido per i test](virtual-networks-setup-hybrid-cloud-environment-testing.md) quando non si dispone di una connessione diretta a Internet e un indirizzo IP pubblico. Di seguito è riportata la configurazione risultante.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Permette di simulare un ambiente di produzione cloud ibrido. È costituita da:

- Una rete locale semplificata e simulata ospitata in una rete virtuale di Azure (la rete virtuale TestLab).
- Una rete virtuale cross-premise simulata ospitata in Azure (TestVNET).
- Una connessione da rete virtuale a rete virtuale tra le due reti virtuali.
- Un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione fornisce una base e un punto di partenza comune da cui è possibile:

- Sviluppare e testare applicazioni in un ambiente cloud ibrido simulato.
- Creare configurazioni di test di computer, alcune nella rete virtuale TestLab e altre nella rete virtuale TestVNET, per simulare carichi di lavoro IT basati sul cloud.

La configurazione di questo ambiente di test cloud ibrido comprende quattro fasi principali:

1.	Configurare la rete virtuale TestLab.
2.	Creare la rete virtuale cross-premise.
3.	Creare la connessione VPN da rete virtuale a rete virtuale.
4.	Configurare DC2. 

Se non si ha ancora una sottoscrizione di Azure, è possibile iscriversi per ottenere una [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). Se si dispone di un abbonamento MSDN, vedere [Benefici di Azure per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Le macchine virtuali e i gateway di rete virtuale in Azure generano addebiti monetari in caso di esecuzione. Il costo viene addebitato sulla base della versione di valutazione gratuita, dell'abbonamento MSDN o della sottoscrizione a pagamento. Per ulteriori informazioni sulla riduzione dei costi di esecuzione dell'ambiente di test quando non viene utilizzato, vedere [Ridurre al minimo i costi di esercizio dell'ambiente](#costs) in questo articolo.


## Fase 1: Configurare la rete virtuale TestLab

Utilizzare le istruzioni in[Configurazione base dell’Ambiente di Test](../virtual-machines/virtual-machines-windows-classic-test-config-env.md) per configurare i computer DC1, APP1 e CLIENT1 in una rete virtuale Azure denominata TestLab.

Dal portale di gestione di Azure nel computer locale connettersi a DC1 con le credenziali CORP\\User1. Per configurare il dominio CORP in modo che i computer e gli utenti usino il controller di dominio locale per l'autenticazione, eseguire questi comandi da un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Fase 2: Creare la rete virtuale TestVNET

Creare prima di tutto una nuova rete virtuale denominata TestVNET.

1.	Nella barra delle applicazioni del portale di gestione di Azure fare clic su **Nuovo > Servizi di rete > Rete virtuale > Creazione personalizzata**.
2.	Nella pagina Dettagli della rete virtuale digitare **TestVNET** in **Nome**.
3.	In **Percorso** selezionare il percorso appropriato.
4.	Fare clic sulla freccia Avanti.
5.	In **Server DNS** nella pagina Server DNS e connettività VPN digitare **DC1** in **Selezionare o immettere nome**, quindi fare clic sulla freccia Avanti.
6.	Nella pagina Spazi di indirizzi della rete virtuale:
	- In **Spazio degli indirizzi** in **IP iniziale** selezionare o digitare **192.168.0.0**.
	- In **Subnet** fare clic su **Subnet-1** e sostituire il nome con **TestSubnet**. 
	- Nella colonna **CIDR (conteggio indirizzi)** per TestSubnet fare clic su **/24 (256)**.
7.	Fare clic sull'icona Completa. Attendere il completamento della creazione della rete virtuale prima di continuare.

Usare quindi le istruzioni disponibili in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare Azure PowerShell nel computer locale.

Creare quindi un nuovo servizio cloud per la rete virtuale TestVNET. È necessario selezionare un nome univoco. È ad esempio possibile specificare il nome **TestVNET-***UniqueSequence*, in cui *UniqueSequence* è un'abbreviazione dell'organizzazione. Se ad esempio il nome dell'organizzazione è Tailspin Toys, è possibile assegnare il nome **TestVNET-Tailspin** al servizio cloud.

Per verificare l'univocità del nome, è possibile usare questo comando di Azure PowerShell nel computer locale.

	Test-AzureName -Service <Proposed cloud service name>

Se questo comando restituisce "False", il nome proposto è univoco. Creare il servizio cloud con questo comando.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Fase 3: Creare la connessione da rete virtuale a rete virtuale

Creare prima di tutto le reti locali che rappresentano lo spazio di indirizzi di ogni rete virtuale.

1.	Nel portale di gestione di Azure nel computer locale fare clic su **Nuovo > Servizi di rete > Rete virtuale > Aggiungi rete locale**.
2.	Nella pagina Specificare i dettagli della rete locale digitare **TestLabLNet** in **Nome**, specificare **131.107.0.1** in **Indirizzo IP dispositivo VPN** e infine fare clic sulla freccia a destra.
3.	Nella pagina Specificare lo spazio di indirizzi in **IP iniziale** digitare **10.0.0.0**.
4.	In **CIDR (conteggio indirizzi)** selezionare **/24 (256)**, quindi fare clic sul segno di spunta.
5.	Fare clic su **Nuovo> Servizi di rete > Rete virtuale > Aggiungi rete locale**.
6.	Nella pagina Specificare i dettagli della rete locale digitare **TestVNETLNet** in **Nome**, specificare **131.107.0.2** in **Indirizzo IP dispositivo VPN** e infine fare clic sulla freccia a destra.
7.	Nella pagina Specificare lo spazio di indirizzi in **IP iniziale** digitare **192.168.0.0**.
8.	In **CIDR (conteggio indirizzi)** selezionare **/24 (256)**, quindi fare clic sul segno di spunta.

Si noti che gli indirizzi IP del dispositivo VPN 131.107.0.1 e 131.107.0.2 sono solo valori segnaposto temporanei, da usare fino alla configurazione dei gateway per le due reti virtuali.

Configurare quindi ogni rete virtuale per l'uso di una connessione VPN da sito a sito e la rete locale corrispondente all'altra rete virtuale.

1.	Nel portale di gestione di Azure nel computer locale fare clic su **Reti** nel riquadro a sinistra, quindi verificare che la colonna **Stato** per **TestLab** sia impostata su **Creata**.
2.	Fare clic su **TestLab** e quindi su **Configura**. Nella pagina TestLab fare clic su **Connetti alla rete locale** nella sezione **Connettività da sito a sito**. 
3.	In **Rete locale** selezionare **TestVNETLNet**.
4.	Fare clic su **Salva** nella barra delle applicazioni. In alcuni casi, potrebbe essere necessario fare clic su **Aggiungi subnet gateway** per creare un subnet usato dal gateway VPN di Azure.
5.	Fare clic su **Reti** nel riquadro a sinistra, quindi verificare che la colonna **Stato** per TestVNET sia impostata su **Creata**.
6.	Fare clic su **TestVNET** e quindi su **Configura**. Nella pagina TestVNET fare clic su **Connetti alla rete locale** nella sezione **Connettività da sito a sito**. 
7.	In **Rete locale** selezionare **TestLabLNet**.
8.	Fare clic su **Salva** nella barra delle applicazioni. In alcuni casi, potrebbe essere necessario fare clic su **Aggiungi subnet gateway** per creare un subnet usato dal gateway VPN di Azure.

Creare quindi i gateway di rete virtuale per le due reti virtuali.

1.	Nella pagina **Reti** del portale di gestione di Azure fare clic su **TestLab**. Nella pagina Dashboard dovrebbe essere visualizzato lo stato **Gateway non creato**.
2.	Nella barra delle applicazioni fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** quando richiesto. Attendere il completamento del gateway e l'impostazione del relativo stato su **Connessione**. L'operazione può richiedere alcuni minuti.
3.	Annotare l'**Indirizzo IP del gateway** dalla pagina Dashboard. Questo è l'indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale TestLab. Annotare questo indirizzo IP, poiché sarà necessario per la configurazione della connessione da rete virtuale a rete virtuale.
4.	Nella barra delle applicazioni fare clic su **Gestisci chiave**, quindi fare clic sull'icona Copia accanto alla chiave per copiarla negli Appunti. Incollare la chiave in un documento e salvare il documento. Questo valore di chiave sarà necessario per la configurazione della connessione da rete virtuale a rete virtuale.
5.	Nella pagina Reti fare clic su **TestVNET**. Nella pagina Dashboard dovrebbe essere visualizzato lo stato **Gateway non creato**.
6.	Nella barra delle applicazioni fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** quando richiesto. Attendere il completamento del gateway e l'impostazione del relativo stato su **Connessione**. L'operazione può richiedere alcuni minuti.
7.	Annotare l'**Indirizzo IP del gateway** dalla pagina Dashboard. Questo è l'indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale TestVNET. Annotare questo indirizzo IP, poiché sarà necessario per la configurazione della connessione da rete virtuale a rete virtuale.

Configurare quindi le reti virtuali TestLabLNet e TestVNETLNet con gli indirizzi IP pubblici ottenuti dalla creazione dei gateway di rete virtuale.

1.	Nella pagina Reti del portale di gestione di Azure fare clic su **Reti locali**. 
2.	Fare clic su **TestLabLNet** e quindi su **Modifica** nella barra delle applicazioni.
3.	Nella pagina Specificare i dettagli della rete locale digitare l'indirizzo IP del gateway di rete virtuale per la rete virtuale TestLab (ottenuto nel passaggio 3 della procedura precedente) in **Indirizzo IP dispositivo VPN (facoltativo)**, quindi fare clic sulla freccia a destra.
4.	Nella pagina Specificare lo spazio di indirizzi fare clic sul segno di spunta.
5.	Nella pagina Reti locali fare clic su **TestVNETLNet** e quindi su **Modifica** nella barra delle applicazioni.
6.	Nella pagina Specificare i dettagli della rete locale digitare l'indirizzo IP del gateway di rete virtuale per la rete virtuale TestVNET (ottenuto nel passaggio 7 della procedura precedente) in **Indirizzo IP dispositivo VPN (facoltativo)**, quindi fare clic sulla freccia a destra.
7.	Nella pagina Specificare lo spazio di indirizzi fare clic sul segno di spunta.

Configurare quindi la chiave precondivisa in modo che entrambi i gateway usino lo stesso valore, ovvero il valore di chiave determinato dal portale di gestione di Azure per la rete virtuale TestLab. Eseguire questi comandi da un prompt dei comandi di Azure PowerShell nel computer locale, specificando il valore della chiave precondivisa di TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

Nella pagina Rete del portale di gestione di Azure del computer locale fare quindi clic sulla rete virtuale **TestLab**, selezionare **Dashboard** e infine fare clic su **Connetti** nella barra delle applicazioni. Attendere fino alla visualizzazione dello stato Connesso per la rete virtuale TestLab.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Fase 4: Configurare DC2.

Creare prima di tutto una macchina virtuale di Azure per DC2. Eseguire questi comandi nel prompt dei comandi di Azure PowerShell nel computer locale.

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Accedere quindi alla nuova macchina virtuale DC2.

1.	Nel riquadro sinistro del portale di gestione di Azure fare clic su **Macchine virtuali**, quindi su **In esecuzione** nella colonna **Stato** per DC2.
2.	Nella barra delle applicazioni fare clic su **Connetti**. 
3.	Quando viene richiesto di aprire DC2.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Quando vengono richieste le credenziali, usare le seguenti:
- Nome: **DC2\**[Nome dell'account amministratore locale]
- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in DC2 eseguire questi comandi.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.4. Questo è un test del traffico nella connessione da rete virtuale a rete virtuale.

Quindi, aggiungere il disco dati aggiuntivo come nuovo volume con la lettera di unità F:.

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.	Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7.	Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8.	Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9.	Nella pagina Conferma selezioni, fare clic su **Crea**.
10.	Al termine, fare clic su **Chiudi**.

Configurare quindi DC2 come controller di dominio di replica per il dominio corp.contoso.com. Eseguire questi comandi dal prompt dei comandi di Windows PowerShell in DC2:

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Si noti che viene richiesto di specificare sia la password per CORP\\User1 che la password per la Modalità ripristino servizi directory (DSRM, Directory Services Restore Mode) e quindi di riavviare DC2.

Quando la rete virtuale TestVNET è stata associata a un server DNS specifico (DC2), sarà necessario configurare la rete virtuale TestVNET per l'uso di tale server DNS.

1.	Nel riquadro sinistro del portale di gestione di Azure fare clic su **Reti** e quindi su **TestVNET**.
2.	Fare clic su **Configure**.
3.	In **Server DNS** rimuovere la voce 10.0.0.4.
4.	In **Server DNS** aggiungere una voce con **DC2** come nome e **192.168.0.4** come indirizzo IP. 
5.	Nella barra dei comandi in basso fare clic su **Salva** e quindi su **Sì**, quando richiesto. Attendere fino al completamento dell'aggiornamento della rete TestVNet.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
L'ambiente cloud ibrido simulato è ora pronto per il testing.

## Passaggi successivi

- Impostare una [farm Intranet di SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), un'[applicazione line-of-business basata sul Web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) o un [server di sincronizzazione della directory di Office 365 (DirSync)](virtual-networks-setup-dirsync-hybrid-cloud-testing.md) nella rete virtuale TestVNET.


## <a id="costs"></a>Ridurre al minimo i costi di esercizio dell'ambiente

Per ridurre al minimo i costi di esecuzione delle macchine virtuali in questo ambiente, eseguire i test e le dimostrazioni necessari nel modo più rapido possibile, quindi eliminare o arrestare le macchine virtuali quando non sono in uso. È ad esempio possibile usare Automazione di Azure e un runbook per arrestare automaticamente le macchine virtuali nelle reti virtuali TestLab e Test\_VNET al termine di ogni giornata lavorativa. Quando si avviano di nuovo le macchine virtuali nella subnet Corpnet, avviare prima di tutto DC1.

Un gateway VPN di Azure viene implementato come set di due macchine virtuali di Azure che comportano un costo monetario continuativo. Per informazioni dettagliate, vedere [Rete virtuale - Prezzi](https://azure.microsoft.com/pricing/details/virtual-network/). Per ridurre al minimo i costi dei due gateway VPN (uno per TestLab e uno per TestVNET), creare l'ambiente di test ed eseguire i test e le dimostrazioni necessari il più rapidamente possibile oppure eliminare i gateway eseguendo i passaggi seguenti.
 
1.	Dal portale di gestione di Azure del computer locale fare clic su **Reti** nel riquadro sinistro, quindi su **TestLab** e infine su **Dashboard**.
2.	Nella barra delle applicazioni fare clic su **Elimina gateway**. Fare clic su **Sì** quando richiesto. Attendere l'eliminazione del gateway e l'impostazione del relativo stato su **Gateway non creato**.
3.	Fare clic su **Reti** nel riquadro sinistro, quindi su **TestVNET** e infine su **Dashboard**.
4.	Nella barra delle applicazioni fare clic su **Elimina gateway**. Fare clic su **Sì** quando richiesto. Attendere l'eliminazione del gateway e l'impostazione del relativo stato su **Gateway non creato**.

Se si eliminano i gateway e si vuole ripristinare questo ambiente di test, sarà necessario creare prima di tutto nuovi gateway.

1.	Nel portale di gestione di Azure nel computer locale fare clic su **Reti** nel riquadro sinistro, quindi su **TestLab**. Nella pagina Dashboard dovrebbe essere visualizzato lo stato **Gateway non creato**.
2.	Nella barra delle applicazioni fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** quando richiesto. Attendere il completamento del gateway e l'impostazione del relativo stato su **Connessione**. L'operazione può richiedere alcuni minuti.
3.	Annotare l'**Indirizzo IP del gateway** dalla pagina Dashboard. Questo è il nuovo indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale TestLab. Questo indirizzo IP è necessario per riconfigurare la rete locale TestLabLNet.
4.	Nella barra delle applicazioni fare clic su **Gestisci chiave**, quindi fare clic sull'icona Copia accanto alla chiave per copiarla negli Appunti. Incollare il valore di chiave in un documento e salvare il documento. Questo valore di chiave è necessario per riconfigurare il gateway VPN per la rete virtuale TestVNET.
5.	Nel portale di gestione di Azure del computer locale fare clic su **Reti** nel riquadro sinistro, quindi su **TestVNET**. Nella pagina Dashboard dovrebbe essere visualizzato lo stato **Gateway non creato**.
6.	Nella barra delle applicazioni fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** quando richiesto. Attendere il completamento del gateway e l'impostazione del relativo stato su Connessione. L'operazione può richiedere alcuni minuti.
7.	Annotare l'**Indirizzo IP del gateway** dalla pagina Dashboard. Questo è il nuovo indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale TestVNET. Questo indirizzo IP è necessario per riconfigurare la rete locale TestVNETLNet.

Configurare quindi le reti virtuali TestLabLNet e TestVNETLNet con i nuovi indirizzi IP pubblici ottenuti dalla creazione dei gateway di rete virtuale.

1.	Nella pagina Reti del portale di gestione di Azure fare clic su **Reti locali**. 
2.	Fare clic su **TestLabLNet** e quindi su **Modifica** nella barra delle applicazioni.
3.	Nella pagina Specificare i dettagli della rete locale digitare l'indirizzo IP del gateway di rete virtuale per la rete virtuale TestLab (ottenuto nel passaggio 3 della procedura precedente) in **Indirizzo IP dispositivo VPN (facoltativo)**, quindi fare clic sulla freccia a destra.
4.	Nella pagina Specificare lo spazio di indirizzi fare clic sul segno di spunta.
5.	Nella pagina Reti locali fare clic su **TestVNETLNet** e quindi su **Modifica** nella barra delle applicazioni.
6.	Nella pagina Specificare i dettagli della rete locale digitare l'indirizzo IP del gateway di rete virtuale per la rete virtuale TestVNET (ottenuto nel passaggio 7 della procedura precedente) in **Indirizzo IP dispositivo VPN (facoltativo)**, quindi fare clic sulla freccia a destra.
7.	Nella pagina Specificare lo spazio di indirizzi fare clic sul segno di spunta.

Configurare quindi la chiave precondivisa in modo che entrambi i gateway usino lo stesso valore, ovvero il valore di chiave determinato dal portale di gestione di Azure per la rete virtuale TestLab. Eseguire questi comandi da un prompt dei comandi di Azure PowerShell nel computer locale, specificando il valore della chiave precondivisa di TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

Nella pagina Rete del portale di gestione di Azure fare clic sulla rete virtuale **TestLab** e quindi su **Connetti** nella barra delle applicazioni. Attendere fino alla visualizzazione dello stato Connesso per la rete locale TestVNET.

<!---HONumber=AcomDC_0413_2016-->