<properties 
	pageTitle="Ambienti di test di applicazioni LOB | Microsoft Azure" 
	description="Informazioni su come creare un'applicazione line-of-business basata su Web in un ambiente cloud ibrido per professionisti IT o test di sviluppo." 
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
	ms.date="01/28/2016" 
	ms.author="josephd"/>

# Configurazione di un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


In questo argomento viene descritta la creazione di un ambiente cloud ibrido per testare un'applicazione line-of-business (LOB) Intranet ospitata in Microsoft Azure. Di seguito è riportata la configurazione risultante.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)

Per un esempio di applicazione LOB di produzione ospitata in Azure, vedere il progetto di architettura **Applicazioni line-of-business** in [Progetti e diagrammi dell’architettura del Software Microsoft](http://msdn.microsoft.com/dn630664).

Questa configurazione consente di simulare un'applicazione LOB nell'ambiente di produzione di Azure dal percorso su Internet. È costituita da:

- Una rete locale semplificata (la subnet Corpnet).
- Una rete virtuale cross-premise ospitata in Azure (TestVNET).
- Una connessione VPN da sito a sito.
- Un server line-of-business, SQL Server e un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

- Sviluppare e testare le applicazioni LOB ospitate in Internet Information Services (IIS) con un back-end del database di SQL Server 2014 in Azure.
- Eseguire il test di questo carico di lavoro IT basato sul cloud ibrido.

La configurazione di questo ambiente di test cloud ibrido prevede tre fasi principali:

1.	Configurare l'ambiente cloud ibrido per l'esecuzione di test.
2.	Configurare il computer SQL server (SQL1).
3.	Configurare il server LOB (LOB1).

Se non si dispone di una sottoscrizione Azure, è possibile effettuare l'iscrizione per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). Se si dispone di un abbonamento MSDN, vedere [Benefici di Azure per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Impostare l'ambiente cloud ibrido

Utilizzare le istruzioni nell’argomento [Configurare l’ambiente cloud ibrido per il test](virtual-networks-setup-hybrid-cloud-environment-testing.md). Poiché l'ambiente di test non richiede la presenza del server APP1 nella subnet Corpnet, è possibile arrestarlo per il momento.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_1.png)

> [AZURE.NOTE] Per la fase 1, è possibile configurare l'ambiente di test cloud ibrido simulato. Vedere [Impostare un ambiente cloud ibrido simulato per i test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) per le istruzioni.
 
## Fase 2: Configurare il computer SQL server (SQL1)

Dal portale di gestione di Azure, avviare il computer DC2, se necessario.

Successivamente, creare una macchina virtuale di Azure per SQL1 con questi comandi in un prompt dei comandi di Azure PowerShell nel computer locale. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri < and >.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Successivamente, connettersi alla nuova macchina virtuale SQL1 *utilizzando l'account amministratore locale*.

1.	Nel riquadro sinistro del portale di gestione di Azure, fare clic su **Macchine virtuali**, quindi su **In esecuzione** nella colonna Stato per SQL1.
2.	Nella barra delle applicazioni fare clic su **Connetti**. 
3.	Quando viene richiesto di aprire SQL1.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Quando vengono richieste le credenziali, usare le seguenti:
	- Nome: **SQL1\**[Nome dell'account amministratore locale]
	- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Configurare le regole di Windows Firewall per consentire il traffico per il test di connettività di base e SQL Server. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in SQL1 eseguire questi comandi.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1.

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

Eseguire questi comandi al prompt dei comandi di Windows PowerShell in SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Configurare quindi SQL Server 2014 in modo che usi l'unità F: per i nuovi database e per le autorizzazioni dell'account utente.

1.	Dalla schermata Start digitare **SQL Server Management**, quindi fare clic su **SQL Server 2014 Management Studio**.
2.	In **Connetti al Server**, fare clic su **Connetti**.
3.	Nel riquadro dell'albero Esplora oggetti, fare doppio clic su **SQL1**, quindi fare clic su **Proprietà**.
4.	Nella finestra **Proprietà server**, fare clic su **Impostazioni database**.
5.	Individuare **Percorsi predefiniti del Database** e impostare i valori seguenti: 
	- Per **Dati**, digitare il percorso **f:\\Data**.
	- Per **Log**, digitare il percorso **f:\\Log**.
	- Per **Backup**, digitare il percorso **f:\\Backup**.
	- Nota: Solo i nuovi database utilizzeranno questi percorsi.
6.	Fare clic su **OK** per chiudere la finestra.
7.	Nel riquadro dell'albero **Esplora oggetti** aprire **Sicurezza**.
8.	Fare clic con il pulsante destro del mouse su **Account di accesso**, quindi scegliere **Nuovo account di accesso**.
9.	In **Nome account di accesso**, digitare **CORP\\User1**.
10.	Nella pagina **Ruoli Server** fare clic su **sysadmin**, quindi su **OK**.
11.	Chiudere Microsoft SQL Server Management Studio.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_2.png)
 
## Fase 3: Configurare il server LOB (LOB1)

Innanzitutto, creare una macchina virtuale di Azure per LOB1 con questi comandi in un prompt dei comandi di Azure PowerShell nel computer locale.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name LOB1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Successivamente, connettersi alla macchina virtuale LOB1 con le credenziali dell'account CORP\\User1.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in LOB1 eseguire questi comandi.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1.

Configurare LOB1 per IIS e verificare l'accesso da CLIENT1.

1.	Eseguire Server Manager, quindi fare clic su **Aggiungi ruoli e funzionalità**.
2.	Nella pagina Prima di iniziare fare clic su **Avanti**.
3.	Nella pagina Selezione tipo di installazione fare clic su **Avanti**.
4.	Nella pagina Selezione server di destinazione fare clic su **Avanti**.
5.	Nella pagina Ruoli server fare clic su **Server Web (IIS)** nell'elenco dei **Ruoli**.
6.	Quando richiesto, fare clic su **Aggiungi funzionalità**, quindi su **Avanti**.
7.	Nella pagina Selezione funzionalità fare clic su **Avanti**.
8.	Nella pagina Server Web (IIS) fare clic su **Avanti**.
9.	Nella pagina Selezione servizi ruolo selezionare o deselezionare le caselle di controllo per i servizi necessari per i test dell'applicazione LOB, quindi fare clic su**Avanti**.
10.	Nella pagina Conferma selezioni per l'installazione fare clic su **Installa**.
11.	Attendere il completamento dell'installazione dei componenti, quindi fare clic su **Chiudi**.
12.	Accedere al computer CLIENT1 con le credenziali dell'account CORP\\User1, quindi avviare Internet Explorer.
13.	Nella barra degli Indirizzi digitare ****http://lob1/** e quindi premere INVIO. Viene visualizzata la pagina Web IIS 8 predefinita.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)
 
Questo ambiente è pronto per la distribuzione dell'applicazione basata su Web in LOB1 e per i test delle funzionalità e delle prestazioni dalla subnet Corpnet.

## Passaggi successivi

- Configurare l'[ambiente di produzione](../virtual-machines/virtual-machines-windows-lob-overview.md).

<!---HONumber=AcomDC_0323_2016-->