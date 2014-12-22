<properties urlDisplayName="Replica domain controller" pageTitle="Installare un controller di dominio di replica in Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#Installazione di un controller di dominio Active Directory di replica nelle reti virtuali di Azure

In questa esercitazione viene descritto come installare un controller di dominio aggiuntivo dalla foresta Active Directory aziendale in una macchina virtuale in [Rete virtuale di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj156007.aspx). In questa esercitazione la rete virtuale per la macchina virtuale è connessa alla rete aziendale. Per le linee guida concettuali sull'installazione di Servizi di dominio Active Directory in Rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in Macchine virtuali di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj156090.aspx).

##Sommario##

* [Prerequisiti](#Prerequisites)
* [Passaggio 1: Verificare l'indirizzo IP statico per YourPrimaryDC](#verifystaticip)
* [Passaggio 2: Installare la foresta aziendale](#installforest)
* [Passaggio 3: Creare le subnet e i siti](#subnets)
* [Passaggio 4: Installare un controller di dominio aggiuntivo nell'oggetto del sito CloudSite](#cloudsite)
* [Passaggio 5: Convalidare l'installazione](#validate)
* [Passaggio 6: Eseguire il provisioning di una macchina virtuale aggiunta a un dominio all'avvio](#provisionvm)
* [Passaggio 7: Eseguire il backup del controller di dominio](#backup)
* [Passaggio 8: Verificare l'autenticazione e l'autorizzazione](#test)


<h2><a id="Prerequisites"></a>Prerequisiti</h2>

-	[Configurare una VPN da sito a sito nel portale di gestione](http://msdn.microsoft.com/it-it/library/dn133795.aspx) tra la rete virtuale di Azure e la rete aziendale.
-	Creare un servizio cloud nella rete virtuale.
-	Distribuire due macchine virtuali nel servizio cloud che fanno parte della rete virtuale (specificare la subnet in cui si desidera collocare la macchina virtuale). Per altre informazioni, vedere [Aggiungere una macchina virtuale a Rete virtuale](http://azure.microsoft.com/it-it/documentation/articles/virtual-networks-add-virtual-machine/). Una macchina virtuale deve essere di dimensione L o superiore per consentire il collegamento di due dischi dati. I dischi dati sono necessari per l'archiviazione di:
	- Database e log di Active Directory.
	- Backup dello stato del sistema.
-	Rete aziendale con due macchine virtuali (YourPrimaryDC e FileServer).
-	Infrastruttura DNS (Domain Name System) distribuita se è necessaria la risoluzione dei nomi per gli account in Active Directory da parte di utenti esterni. In questo caso, sarà necessario creare la delega per una zona DNS prima di installare il server DNS nel controller di dominio o consentire a Installazione guidata Servizi di dominio Active Directory di creare la delega. Per altre informazioni sulla creazione di una delega per la zona DNS, vedere [Creare la delega per una zona](http://technet.microsoft.com/library/cc753500.aspx).
-	Nel controller di dominio installato nella macchina virtuale di Azure, configurare le impostazioni del resolver del client DNS come descritto di seguito:
	- Server DNS preferito: server DNS locale 
	- Server DNS alternativo: indirizzo di loopback o, se possibile, un altro server DNS in esecuzione in un controller di dominio sulla stessa rete virtuale.

<div class="dev-callout"> 
<b>Nota</b>
<p>È necessario fornire la propria infrastruttura DNS per supportare Servizi di dominio Active Directory in Rete virtuale di Azure. L'infrastruttura DNS fornita da Azure per questa versione non supporta alcune funzionalità richieste da Servizi di dominio Active Directory, ad esempio la registrazione dei record di risorse SRV dinamica. </p>
</div>

<div class="dev-callout"> 
<b>Nota</b>
<p>Se è già stata completata la procedura descritta in <a href="/it-it/manage/services/networking/active-directory-forest/">Installare una nuova foresta Active Directory in Azure</a>, potrebbe essere necessario rimuovere Servizi di dominio Active Directory dal controller di dominio nella rete virtuale di Azure prima di iniziare l'esercitazion. Per altre informazioni su Servizi di dominio Active Directory, vedere l'argomento relativo alla  <a href="http://technet.microsoft.com/it-it/library/cc771844(v=WS.10).aspx">rimozione di un controller di dominio da un dominio</a>.</p>
</div>


<h2><a id="verifystaticip"></a>Passaggio 1: Verificare l'indirizzo IP statico per YourPrimaryDC</h2>

1. Accedere a YourPrimaryDC nella rete aziendale.

2. In Server Manager fare clic su Visualizza connessioni di rete.

3. Fare clic con il pulsante destro del mouse sulla connessione LAN (Local Area Network) e scegliere Proprietà.

4. Fare clic su Protocollo Internet versione 4 (TCP/IPv4) e quindi su Proprietà.

5. Verificare che al server sia assegnato un indirizzo IP statico. 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>Passaggio 2: Installare la foresta aziendale</h2>

1. Nella sessione RDP per la macchina virtuale fare clic su **Start**, digitare**dcpromo** e premere INVIO.

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. Nella pagina iniziale fare clic su **Avanti**.

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. Nella pagina Compatibilità sistema operativo fare clic su **Avanti**.

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. Nella pagina Selezione configurazione di distribuzione fare clic su **Crea un nuovo dominio in una nuova foresta** e quindi fare clic su**Avanti**. 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. Nella pagina Nome dominio radice foresta digitare **corp.contoso.com**, ovvero il nome di dominio completo del dominio radice della foresta e fare clic su **Avanti** 

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. Nella pagina Impostazione livello funzionalità foresta fare clic su **Windows Server 2008 R2** e quindi su **Avanti**.

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. Nella pagina Opzioni controller di dominio aggiuntivo fare clic su **Server DNS** e quindi su **Avanti**.

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. Se viene visualizzato l'avviso di delega DNS seguente, fare clic su **Sì**.

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. Nella pagina relativa al percorso per il database, i file di registro e le informazioni SYSVOL per il controller di dominio Active Directory, digitare o selezionare il percorso per i file e fare clic su **Avanti**.

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. Nella pagina relativa all'amministratore per la modalità ripristino servizi directory digitare e confermare la password DSRM e fare clic su **Avanti**.

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. Nella pagina Riepilogo confermare le selezioni e fare clic su **Avanti**. 

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. Al termine dell'Installazione guidata Servizi di dominio Active Directory, fare clic su **Fine** e quindi su **Riavvia** per completare l'installazione. 

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>Passaggio 3: Creare le subnet e i siti</h2>

1. In YourPrimaryDC fare clic su Start, quindi su Strumenti di amministrazione e infine su Siti e servizi di Active Directory.
2. Fare clic su **Siti**, quindi fare clic con il pulsante destro del mouse su **Subnet** e scegliere **Nuova subnet**.

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. In **Prefisso** digitare **10.1.0.0/24**, selezionare l'oggetto del sito **Nome-Predefinito-Primo-Sito** e fare clic su **OK**.

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. Fare clic con il pulsante destro del mouse su **Siti** e scegliere **Nuovo sito**.

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. In Nome digitare **CloudSite**, selezionare **DEFAULTIPSITELINK** e fare clic su **OK**. 

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. Fare clic su **OK** per confermare che il sito è stato creato. 

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. Fare clic con il pulsante destro del mouse su **Subnet** e quindi scegliere **Nuova subnet**.

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. In **Prefisso** digitare **10.4.2.0/24**, selezionare l'oggetto del sito **CloudSite** e fare clic su **OK**.

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>Passaggio 4: Installare un controller di dominio aggiuntivo nell'oggetto del sito CloudSite</h2>

1. Accedere alla macchina virtuale, fare clic su **Start**, digitare **dcpromo** e premere INVIO.

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. Nella pagina iniziale fare clic su **Avanti**.

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. Nella pagina Compatibilità sistema operativo fare clic su **Avanti**.

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. Nella pagina Selezione configurazione di distribuzione fare clic su **Foresta esistente**, quindi su **Aggiungi un controller di dominio a un dominio esistente** e infine fare clic su **Avanti**.

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. Nella pagina Credenziali di rete verificare che il controller di dominio venga installato nel dominio **corp.contoso.com** e digitare le credenziali di un membro del gruppo Domain Admins (o usare le credenziali corp\administrator). 

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. Nella pagina Selezione dominio fare clic su **Avanti**. 

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. Nella pagina Selezione sito verificare che sia selezionato CloudSite e fare clic su **Avanti**.

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. Nella pagina Opzioni controller di dominio aggiuntivo fare clic su **Avanti**. 

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. Nell'avviso relativo all'assegnazione dell'indirizzo IP statico, fare clic su **Sì, il computer utilizzerà un indirizzo IP assegnato automaticamente da un server DHCP (scelta non consigliata)**

	**Importante** 

	Sebbene l'indirizzo IP nella rete virtuale di Azure sia dinamico, la durata del lease corrisponde alla durata della macchina virtuale. Non è pertanto necessario impostare un indirizzo IP statico nel controller di dominio installato nella rete virtuale. L'impostazione di un indirizzo IP statico nella macchina virtuale genera errori di comunicazione.


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. Se viene visualizzato l'avviso di delega DNS, fare clic su **Sì**.

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. Nella pagina relativa al percorso per il database, i file di registro e le informazioni SYSVOL per il controller di dominio Active Directory, fare clic su Sfoglia e digitare o selezionare il percorso nel disco dati per i file di Active Directory e fare clic su **Avanti**. 

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. Nella pagina relativa all'amministratore per la modalità ripristino servizi directory digitare e confermare la password DSRM e fare clic su **Avanti**.

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. Nella pagina Riepilogo fare clic su **Avanti**.

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. Al termine dell'Installazione guidata Servizi di dominio Active Directory, fare clic su **Fine** e quindi su **Riavvia** per completare l'installazione. 

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>Passaggio 5: Convalidare l'installazione</h2>

1. Riconnettersi alla macchina virtuale.

2. Fare clic su **Start**, fare clic con il pulsante destro del mouse su **Prompt dei comandi**, quindi scegliere **Esegui come amministratore**. 

3. Digitare il comando seguente e premere INVIO:  'Dcdiag /c /v'

4. Verificare che l'esecuzione dei test abbia avuto esito positivo. 

Dopo avere configurato il controller di dominio, eseguire il cmdlet di Windows PowerShell per eseguire il provisioning di altre macchine virtuali e aggiungerle automaticamente al dominio una volta eseguito il provisioning. È necessario configurare le impostazioni del resolver del client DNS per le macchine virtuali quando viene effettuato il provisioning. Sostituire i nomi corretti per il dominio, la macchina virtuale e così via. 

Per altre informazioni su come usare Windows PowerShell, vedere gli argomenti relativi ad [Azure PowerShell](http://msdn.microsoft.com/it-it/library/windowsazure/jj156055.aspx) e ai [cmdlet di gestione di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj152841)


<h2><a id="provisionvm"></a>Passaggio 6: Eseguire il provisioning di una macchina virtuale aggiunta a un dominio all'avvio</h2>

1. Per creare un'altra macchina virtuale aggiunta al dominio quando viene avviata per la prima volta, aprire Azure PowerShell ISE, incollare lo script riportato di seguito, sostituire i segnaposto con valori personalizzati ed eseguirlo. 

	Per determinare l'indirizzo IP interno del controller di dominio, fare clic sul nome della rete virtuale in cui è in esecuzione. 

	Nell'esempio seguente l'indirizzo IP interno del controller di dominio è 10.4.3.1. La configurazione Add-AzureProvisioningConfig accetta inoltre un parametro -MachineObjectOU che se viene specificato (richiede il nome distinto completo in Active Directory) consente l'impostazione di Criteri di gruppo su tutte le macchine virtuali in quel contenitore.

	Dopo avere effettuato il provisioning delle macchine virtuali, eseguire l'accesso specificando un account di dominio usando il formato del nome dell'entità utente, ad esempio administrator@corp.contoso.com. 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-it-it-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>Passaggio 7: Eseguire il backup del controller di dominio</h2>


1. Connettersi alla macchina virtuale YourVMachine.

2. Fare clic su **Start**, su **Server Manager**, su **Aggiungi funzionalità** e quindi selezionare **Funzionalità di Windows Server Backup**. Seguire le istruzioni per installare Windows Server Backup.

3. Fare clic su **Start**, su **Windows Server Backup** e quindi su **Backup unico**.
 
4. Fare clic su **Opzioni diverse** e quindi su **Avanti**.

5. Fare clic su **Server completo** e quindi su **Avanti**.

6. Fare clic su **Unità locali** e quindi su **Avanti**.

7. Selezionare un'unità di destinazione in cui non siano ospitati i file del sistema operativo o il database di Active Directory, quindi fare clic su Avanti.

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. Confermare le impostazioni di backup selezionate e quindi fare clic su **Backup**.

<h2><a id="test"></a>Passaggio 8: Verificare l'autenticazione e l'autorizzazione</h2>

1. Per verificare l'autenticazione e l'autorizzazione, creare un account utente di dominio in Active Directory. 
Accedere alla macchina virtuale client in ogni sito e creare una cartella condivisa nella macchina virtuale

2. Verificare l'accesso alla cartella condivisa usando account, gruppi e autorizzazioni diversi. 

## Vedere anche

-  [Rete virtuale di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj156007.aspx)

-  [Azure IT Pro IaaS: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Azure IT Pro IaaS: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/it-it/library/windowsazure/jj156055.aspx)

-  [Cmdlet di gestione di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj152841)
