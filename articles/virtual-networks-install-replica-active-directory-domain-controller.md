<properties urlDisplayName="Replica domain controller" pageTitle="Installare un controller di dominio di replica in Azure" metaKeywords="" description="Esercitazione che illustra come installare un controller di dominio da una foresta di Active Directory locale in una macchina virtuale di Azure." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller on an Azure Virtual Network" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="Justinha" />


#Installazione di un controller di dominio Active Directory di replica nelle reti virtuali di Azure

Questo argomento descrive come installare altri controller di dominio (anche noti come controller di dominio di replica) per un dominio Active Directory locale in Macchine virtuali di Azure in una rete virtuale di Azure. 

Altri argomenti di interesse:

- È possibile, se lo si desidera, installare una nuova foresta Active Directory in una rete virtuale di Azure. Per questa procedura, vedere [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/).
-  Per informazioni aggiuntive concettuali sull'installazione di Servizi di dominio Active Directory in Rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Per istruzioni dettagliate sulla creazione di un ambiente di laboratorio di test in Azure che include Servizi di dominio Active Directory, vedere [Guida del laboratorio di testing: Configurazione di base in Azure](http://www.microsoft.com/it-it/download/details.aspx?id=41684).


##Sommario##

* [Diagramma dello scenario](#diagram)
* [Passaggio 1: Creare un sito di Active Directory per la rete virtuale di Azure](#createadsite)
* [Passaggio 2: Creare una rete virtuale di Azure](#createvnet)
* [Passaggio 3: Creare macchine virtuali di Azure per i ruoli del controller di dominio](#createdcvms)
* [Passaggio 4: Installare Servizi di dominio Active Directory in macchine virtuali di Azure](#installadds)
* [Passaggio 5: Creare macchine virtuali per server applicazioni](#createappvms)
* [Risorse aggiuntive](#resources)

<h2><a id="diagram"></a>Diagramma dello scenario</h2>

In questo scenario gli utenti esterni devono accedere alle applicazioni eseguite nei server aggiunti al dominio. Le macchine virtuali che eseguono i server applicazioni e i controller di dominio di replica vengono installati in una rete virtuale di Azure. La rete virtuale può essere connessa alla rete locale mediante una connessione [VPN da sito a sito](http://msdn.microsoft.com/library/azure/dn133795.aspx), come mostrato nel diagramma seguente, oppure è possibile usare [ExpressRoute](http://azure.microsoft.com/services/expressroute/) per una connessione più veloce. 

I server applicazioni e i controller di dominio vengono distribuiti all'interno di [servizi cloud](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/) separati per distribuire l'elaborazione del calcolo e all'interno dei [set di disponibilità](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/) per una tolleranza di errore migliorata. 
I controller di dominio eseguono la replica reciproca e con controller di dominio locali usando la replica di Active Directory. Non sono necessari strumenti di sincronizzazione.

![][1]

<h2><a id="createadsite"></a>Passaggio 1: Creare un sito di Active Directory per la rete virtuale di Azure</h2>

È consigliabile creare in Active Directory un sito che rappresenta l'area di rete corrispondente alla rete virtuale. Ciò contribuisce a ottimizzare l'autenticazione, la replica e altre operazioni del percorso del controller di dominio. La procedura seguente descrive come creare un sito e per altre informazioni vedere [Aggiunta di un nuovo sito](http://technet.microsoft.com/library/cc781496.aspx).

1. Aprire Siti e servizi di Active Directory: **Server Manager** > **Strumenti** > **Siti e servizi di Active Directory**.
2. Creare un sito per rappresentare l'area in cui è stata creata una rete virtuale di Azure: fare clic su **Siti** > **Azione** > **Nuovo sito** > digitare il nome del nuovo sito, ad esempio Azure Stati Uniti occidentali > selezionare un collegamento di sito > **OK**.
3. Creare una subnet e associare il nuovo sito: fare doppio clic su **Siti** > fare clic con il pulsante destro del mouse su **Subnet** > **Nuova subnet** > digitare l'intervallo di indirizzi IP della rete virtuale (ad esempio 10.1.0.0/16 nel diagramma dello scenario) > selezionare il nuovo sito di Azure > **OK**.

<h2><a id="createvnet"></a>Passaggio 2: Creare una rete virtuale di Azure</h2>

<ol><li><p>In the Azure Management Portal, click <b>New</b> > <b>Network Services</b> > <b>Virtual Network</b> > <b>Custom Create</b> and use the following values to complete the wizard.</p>

<table style="width:100%">
<tr>
<td>In questa pagina della procedura guidata</td>
<td>Valori da specificare</td>
</tr>
<tr>
<td><b>Dettagli della rete virtuale</b></td>
<td><ul><li>Nome: digitare un nome per la rete virtuale, ad esempio WestUSVNet.</li><li>Area: questo è il percorso di Azure in cui verrà collocata la rete virtuale, ad esempio Stati Uniti occidentali. Questo percorso non può essere modificato dopo aver creato la rete virtuale.</li></ul>
</td>
</tr>
<tr>
<td><b>Server DNS e connettività VPN</b></td>
<td><ul><li>Server DNS: specificare il nome e l'indirizzo IP di uno o più server DNS locali.</li><li>Connettività: selezionare <b>Configura una VPN Site-to-Site</b>.</li><li>Rete locale: specificare una nuova rete locale.</li></ul>Se si usa ExpressRoute invece di una VPN, vedere <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Configurare una connessione ExpressRoute tramite un Provider di Exchange</a>.</td>
</tr>
<tr>
<td><b>Connettività da sito a sito</b></td>
<td><ul><li>Nome: digitare un nome per la rete locale.</li><li>Indirizzo IP dispositivo VPN: specificare l'indirizzo IP pubblico del dispositivo che si connetterà alla rete virtuale. Il dispositivo VPN non può trovarsi dietro un NAT.</li><li>Indirizzo: specificare gli intervalli di indirizzi della rete locale (ad esempio 192.168.0.0/16 nel diagramma dello scenario).</li></ul></td>
</tr>
<tr>
<td><b>Spazi di indirizzi della rete virtuale</b></td>
<td><ul><li>Address Space: Specificare l'intervallo di indirizzi IP per le macchine virtuali che si desidera eseguire nella rete virtuale di Azure (ad esempio 10.1.0.0/16 nel diagramma dello scenario). Questo intervallo di indirizzi non può sovrapporsi agli intervalli di indirizzi della rete locale.</li><li>Subnet: specificare un nome e un indirizzo per una subnet per i server applicazioni (ad esempio front-end, 10.1.1.0/24) e per i controller di dominio (ad esempio back-end, 10.1.2.0/24).</li><li>Fare clic su <b>Aggiungi subnet gateway</b>.</li></ul></td>
</tr>
</table>
</li>
<li><p>Quindi, si configurerà il gateway di rete virtuale per creare una connessione VPN da sito a sito sicura. Per istruzioni, vedere <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configurare un gateway di rete virtuale nel portale di gestione</a>.</p>
</li>
<li><p>Creare la connessione VPN da sito a sito tra la nuova rete virtuale e un dispositivo VPN locale. Per istruzioni, vedere <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configurare un gateway di rete virtuale nel portale di gestione</a>.</p>
</li>
</ol>

<h2><a id="createdcvms"></a>Passaggio 3: Creare macchine virtuali di Azure per i ruoli del controller di dominio</h2>

<p>Ripetere i passaggi seguenti per creare macchine virtuali per ospitare il ruolo di controller di dominio in base alle esigenze. È consigliabile distribuire almeno due controller di dominio virtuali per fornire ridondanza e tolleranza di errore. </p>


<ol><li><p>In the Azure Management portal, click <b>New</b> > <b>Compute</b> > <b>Virtual Machine</b> > <b>From Gallery</b>. Use the following values to complete the wizard. Accept the default value for a setting unless another value is suggested or required.</p>
<table style="width:100%">
<tr>
<td><b>In questa pagina della procedura guidata</b></td>
<td><b>Valori da specificare</b></td>
</tr>
<tr>
<td><b>Scegli un'immagine</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configurazione macchina virtuale</b></td>
<td><ul><li>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AzureDC2).</li><li>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account integrato denominato Administrator non funzionerà.</li><li>Nuova password/Conferma nuova password: digitare una password</li></ul></td>
</tr>
<tr>
<td><b>Configurazione macchina virtuale</b></td>
<td><ul><li>Servizio cloud: Scegliere <b>Crea un nuovo servizio cloud</b> per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più macchine virtuali che ospiteranno il ruolo di controller di dominio.</li><li>Nome DNS del servizio cloud: specificare un nome univoco globale</li><li>Area/Gruppo di affinità/Rete virtuale: specificare il nome di rete virtuale (ad esempio WestUSVNet).</li><li>Account di archiviazione: Scegliere <b>Usa un account di archiviazione generato automaticamente</b> per la prima macchina virtuale e quindi selezionare lo stesso nome account di archiviazione quando si creano più macchine virtuali che ospiteranno il ruolo di controller di dominio.</li><li>Set di disponibilità: scegliere <b>Crea set di disponibilità</b>.</li><li>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima macchina virtuale e quindi selezionare lo stesso nome quando si creano più macchine virtuali.</li></ul></td>
</tr>
<tr>
<td><b>Configurazione macchina virtuale</b></td>
<td>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</td>
</tr>
</table>
</li>
<li><p>Collegare un disco a ogni macchina virtuale che eseguirà il ruolo del server di controller di dominio. Il disco aggiuntivo necessario per archiviare il database di Active Directory, log e SYSVOL. Specificare una dimensione per il disco (ad es. 10 GB) e lasciare <b>Preferenze cache dell'host</b> impostato su <b>Nessuna</b>. Dopo avere effettuato la prima connessione alla macchina virtuale, aprire <b>Server Manager</b> > <b>Servizi file e archiviazione</b> per creare un volume in questo disco usando NTFS.</p></li>
<li><p>Riservare un indirizzo IP statico per le macchine virtuali che eseguiranno il ruolo di controller di dominio. Per riservare un indirizzo IP statico, scaricare l'Installazione guidata piattaforma Web Microsoft e <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">installare Azure PowerShell</a>, quindi eseguire il cmdlet <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a>.</p></li>
<li><p>Nel portale di gestione di Azure fare clic sul nome della rete virtuale, quindi fare clic sulla scheda <b>Configura</b> per <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">riconfigurare gli indirizzi IP del server DNS per la rete virtuale</a> allo scopo di usare gli indirizzi IP statici assegnati ai controller di dominio di replica invece degli indirizzi IP di server DNS locali. </p>
</li>
<li><p>Per garantire che tutte le macchine virtuali dei controller di dominio di replica sulla rete virtuale siano configurati per usare i server DNS sulla rete virtuale, fare clic su <b>Macchine virtuali</b>, fare clic sulla colonna di stato per ciascuna macchina virtuale, quindi fare clic su <b>Riavvia</b>. Attendere fino a quando la macchina virtuale mostra lo stato <b>In esecuzione</b> prima di provare a effettuare l'accesso. 
</p>
</li>
</ol>

<h2><a id="installadds"></a>Passaggio 4: Installare Servizi di dominio Active Directory in macchine virtuali di Azure</h2>

Accedere a una macchina virtuale e verificare che sia disponibile la connettività nella VPN da sito a sito o la connessione ExpressRoute alle risorse sulla rete locale, quindi installare Servizi di dominio Active Directory nelle macchine virtuali di Azure. È possibile usare la stessa procedura che consente di installare un controller di dominio aggiuntivo nella rete locale (interfaccia utente, Windows PowerShell o un file di risposta). Durante l'installazione di Servizi di dominio Active Directory, assicurarsi di specificare il nuovo volume per il percorso del database Active Directory, i log e SYSVOL. Se è necessario un aggiornamento nell'installazione di Servizi di dominio Active Directory, vedere [Installazione di Servizi di dominio Active Directory (Livello 100)](http://technet.microsoft.com/library/hh472162.aspx) o [Installare un Controller di dominio Windows Server 2012 Replica in un dominio esistente (livello 200)](http://technet.microsoft.com/library/jj574134.aspx).

<h2><a id="x=createappvms"></a>Passaggio 5: Creare macchine virtuali per server applicazioni</h2>

<ol><li><p>Repeat the following steps to create VMs to run as application servers. Accept the default value for a setting unless another value is suggested or required.</p>

<table style="width:100%">
<tr>
<td><b>In questa pagina della procedura guidata</b></td>
<td><b>Valori da specificare</b></td>
</tr>
<tr>
<td><b>Scegli un'immagine</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configurazione macchina virtuale</b></td>
<td><ul><li>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio TreyAppServer1).</li><li>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account integrato denominato Administrator non funzionerà.</li><li>Nuova password/Conferma nuova password:  digitare una password</li></ul></td>
</tr>
<tr>
<td><b>Configurazione macchina virtuale</b></td>
<td><ul><li>Servizio cloud: Scegliere Crea un nuovo servizio cloud per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più macchine virtuali che ospiteranno l'applicazione.</li><li>Nome DNS del servizio cloud: specificare un nome univoco globale</li><li>Area/Gruppo di affinità/Rete virtuale: specificare il nome di rete virtuale (ad esempio WestUSVNet).</li><li>Account di archiviazione: Scegliere <b>Usa un account di archiviazione generato automaticamente</b> per la prima macchina virtuale e quindi selezionare lo stesso nome account di archiviazione quando si creano più macchine virtuali che ospiteranno il ruolo di controller di dominio.</li><li>Set di disponibilità: scegliere <b>Crea set di disponibilità</b>.</li><li>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima macchina virtuale e quindi selezionare lo stesso nome quando si creano più macchine virtuali.</li></ul></td>
</tr>
<tr>
<td><b>Configurazione macchina virtuale</b></td>
<td>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</td>
</tr>
</table>


</li>
<li><p>Dopo avere eseguito il provisioning di ogni macchina virtuale, accedere e aggiungerla al dominio. In <b>Server Manager</b> fare clic su <b>Server locale</b> > <b>WORKGROUP</b> > <b>Modifica...</b> e quindi selezionare <b>Dominio</b> e digitare il nome del dominio locale. Specificare le credenziali di un utente di dominio e quindi riavviare la macchina virtuale per completare l'aggiunta al dominio.
</p>
</li>
</ol>
<p>
In alternativa all'uso del portale di gestione per effettuare il provisioning delle macchine virtuali, è possibile usare Windows PowerShell per Microsoft Azure. Usare <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> e <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig</a> per effettuare il provisioning di una macchina virtuale come macchina aggiunta al dominio quando viene avviata per la prima volta e usare <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM</a> per creare la macchina virtuale stessa. 
</p>

Per altre informazioni su come usare Windows PowerShell, vedere gli argomenti relativi ad [Azure PowerShell](http://msdn.microsoft.com/it-it/library/windowsazure/jj156055.aspx) e ai [cmdlet di gestione di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj152841)


<h2><a id="resources"></a>Risorse aggiuntive</h2>

-  [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Come caricare i controller di dominio locale Hyper-V di Azure tramite Azure PowerShell](http://support.microsoft.com/kb/2904015)

-  [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Azure IT Pro IaaS: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Azure IT Pro IaaS: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Cmdlet di gestione di Azure](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=35.2-->
