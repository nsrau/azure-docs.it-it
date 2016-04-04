<properties
	pageTitle="Installare un controller di dominio di replica in Azure | Microsoft Azure"
	description="Esercitazione che illustra come installare un controller di dominio da una foresta Active Directory locale in una macchina virtuale di Azure."
	services="virtual-network"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="virtual-network"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="curtand"/>


# Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure

Questo argomento descrive come installare altri controller di dominio (anche noti come controller di dominio di replica) per un dominio Active Directory locale in Macchine virtuali di Azure in una rete virtuale di Azure.

Altri argomenti di interesse:

-  È possibile, se lo si desidera, installare una nuova foresta Active Directory in una rete virtuale di Azure. Per questa procedura, vedere [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Per le linee guida concettuali sull'installazione di Servizi di dominio Active Directory in una rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## Diagramma dello scenario

In questo scenario gli utenti esterni devono accedere alle applicazioni eseguite nei server aggiunti al dominio. Le macchine virtuali che eseguono i server applicazioni e i controller di dominio di replica vengono installati in una rete virtuale di Azure. La rete virtuale può essere connessa alla rete locale mediante una [connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md), come mostrato nel diagramma seguente, oppure è possibile usare [ExpressRoute](../../services/expressroute/) per una connessione più veloce.

I server dell'applicazione e i controller di dominio vengono distribuiti all'interno di servizi cloud separati per distribuire l'elaborazione del calcolo e all'interno dei [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md) per una migliore tolleranza di errore. I controller di dominio eseguono la replica reciproca e con controller di dominio locali usando la replica di Active Directory. Non sono necessari strumenti di sincronizzazione.

![][1]

## Creare un sito di Active Directory per la rete virtuale di Azure

È consigliabile creare in Active Directory un sito che rappresenta l'area di rete corrispondente alla rete virtuale. Ciò contribuisce a ottimizzare l'autenticazione, la replica e altre operazioni del percorso del controller di dominio. La seguente procedura descrive come creare un sito. Per altre informazioni, vedere [Aggiungere un nuovo sito](https://technet.microsoft.com/library/cc781496.aspx).

1. Aprire siti e servizi Active Directory: **Server Manager** > **Strumenti** > **Siti e servizi Active Directory**.
2. Creare un sito per rappresentare l'area in cui è stata creata una rete virtuale di Azure: fare clic su **Siti** > **Azione** > **Nuovo sito** > digitare il nome del nuovo sito, ad esempio Azure Stati Uniti occidentali > selezionare un collegamento di sito > **OK**.
3. Creare una subnet e associarla al nuovo sito: fare doppio clic su **Siti** > fare clic con il pulsante destro del mouse su **Subnet** > **Nuova subnet** > digitare l'intervallo di indirizzi IP della rete virtuale (ad esempio 10.1.0.0/16 nel diagramma dello scenario) > selezionare il nuovo sito di Azure > **OK**.

## Creare una rete virtuale di Azure

1. Nel portale di Azure classico, fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale**e > **Creazione personalizzata** e usare i seguenti valori per completare la procedura guidata.

    Pagina della procedura guidata | Valori da specificare
	------------- | -------------
	**Dettagli della rete virtuale** | <p>Nome: Digitare un nome per la rete virtuale, ad esempio WestUSVNet.</p><p>Regione: Scegliere l'area più vicina.</p>
	**Server DNS e connettività VPN** | <p>Server DNS: specificare il nome e l'indirizzo IP di uno o più server DNS locali.</p><p>Connettività: selezionare **Configurare una VPN da sito a sito**.</p><p>Rete locale: specificare una nuova rete locale.</p><p>Se si utilizza ExpressRoute anziché una connessione VPN, vedere [Configurare una connessione ExpressRoute tramite un Provider di Exchange](../expressroute/expressroute-configuring-exps.md).</p>
	**Connettività da sito a sito** | <p>Nome: Digitare un nome per la rete locale.</p><p>Indirizzo IP dispositivo VPN: specificare l'indirizzo IP pubblico del dispositivo che si connetterà alla rete virtuale. Il dispositivo VPN non può trovarsi dietro un NAT.</p><p>Indirizzo: Specificare gli intervalli di indirizzi della rete locale (ad esempio 192.168.0.0/16 nel diagramma scenario).</p>
	**Spazi degli indirizzi della rete virtuale** | <p>Spazio degli indirizzi: Specificare l'intervallo di indirizzi IP per le macchine virtuali che si desidera eseguire nella rete virtuale di Azure (ad esempio 10.1.0.0/16 nel diagramma dello scenario). Questo intervallo di indirizzi non può sovrapporsi agli intervalli di indirizzi della rete locale.</p><p>Subnet: Specificare un nome e indirizzo per una subnet per i server dell’applicazione (ad esempio front-end, 10.1.1.0/24) e per i controller di dominio (ad esempio back-end, 10.1.2.0/24).</p><p>Fare clic su **Aggiungi subnet gateway**.</p>

2. Quindi, si configurerà il gateway di rete virtuale per creare una connessione VPN da sito a sito sicura. Vedere [Configurare un gateway per la rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) per informazioni.
3. Creare la connessione VPN da sito a sito tra la nuova rete virtuale e un dispositivo VPN locale. Vedere [Configurare un gateway per la rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) per informazioni.



## Creare macchine virtuali di Azure per i ruoli del controller di dominio

Ripetere i passaggi seguenti per creare macchine virtuali per ospitare il ruolo di controller di dominio in base alle esigenze. È consigliabile distribuire almeno due controller di dominio virtuali per fornire ridondanza e tolleranza di errore. Se la rete virtuale di Azure include almeno due controller di dominio configurati in modo analogo (vale a dire se sono entrambi cataloghi globali, eseguono server DNS, non contengono alcun ruolo FSMO e così via), posizionare le macchine virtuali che eseguono tali controller di dominio in un gruppo di disponibilità per una tolleranza di errore migliorata. Per creare le macchine virtuali tramite Windows PowerShell anziché tramite l'interfaccia utente, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Nel portale di Azure classico, fare clic su **Nuovo** > **Calcolo** > **Macchina virtuale** > **Da raccolta**. Usare i valori nella seguente tabella per completare la procedura guidata. Accettare il valore predefinito per tutte le impostazioni a meno che non venga suggerito o richiesto un altro valore.

    Pagina della procedura guidata | Valori da specificare
	------------- | -------------
	**Scegli un'immagine** | Windows Server 2012 R2 Datacenter
	**Configurazione macchina virtuale** | <p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AzureDC1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/Conferma: digitare una password</p>
	**Configurazione macchina virtuale** | <p>Servizio cloud: scegliere <b>Crea un nuovo servizio cloud</b> per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano altre macchine virtuali che ospiteranno il ruolo Controller di dominio.</p><p>Nome DNS del servizio cloud: specificare un nome univoco a livello globale</p><p>Area/Gruppo di affinità/Rete virtuale: specificare il nome della rete virtuale (ad esempio WestUSVNet).</p><p>Account di archiviazione: scegliere <b>Usa un account di archiviazione generato automaticamente</b> per la prima macchina virtuale e quindi selezionare lo stesso nome di account di archiviazione quando si creano altre macchine virtuali che ospiteranno il ruolo Controller di dominio.</p><p>Set di disponibilità: scegliere <b>Crea set di disponibilità</b>.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità per la prima macchina virtuale e quindi selezionare lo stesso nome quando si creano altre macchine virtuali.</p>
	**Configurazione macchina virtuale** | <p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p>
2. Collegare un disco a ogni macchina virtuale che eseguirà il ruolo del server di controller di dominio. Il disco aggiuntivo necessario per archiviare il database di Active Directory, log e SYSVOL. Specificare le dimensioni per il disco (ad esempio 10 GB) e lasciare l'opzione **Preferenze cache dell'host** impostata su **Nessuna**. Per la procedura, vedere [Come collegare un disco dati a una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Dopo avere effettuato la prima connessione alla macchina virtuale, aprire **Server Manager** > **Servizi file e archiviazione** per creare un volume in questo disco usando NTFS.
4. Riservare un indirizzo IP statico per le macchine virtuali che eseguiranno il ruolo di controller di dominio. Per riservare un indirizzo IP statico, scaricare l'Installazione guidata piattaforma Web Microsoft e [installare Azure PowerShell](../powershell-install-configure.md), quindi eseguire il cmdlet Set-AzureStaticVNetIP. Ad esempio:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Per altre informazioni sull'impostazione di un indirizzo IP statico, vedere [Configurare un indirizzo IP interno statico per una macchina virtuale](../virtual-network/virtual-networks-reserved-private-ip.md).

## Installare Servizi di dominio Active Directory in macchine virtuali di Azure

Accedere a una macchina virtuale e verificare che sia disponibile la connettività nella VPN da sito a sito o la connessione ExpressRoute alle risorse sulla rete locale, quindi installare Servizi di dominio Active Directory nelle macchine virtuali di Azure. È possibile usare la stessa procedura che consente di installare un controller di dominio aggiuntivo nella rete locale (interfaccia utente, Windows PowerShell o un file di risposta). Durante l'installazione di Servizi di dominio Active Directory, assicurarsi di specificare il nuovo volume per il percorso del database Active Directory, i log e SYSVOL. Se è necessario un aggiornamento nell'installazione di Servizi di dominio di Active Directory, vedere [Installazione di Servizi di dominio di Active Directory (Livello 100)](https://technet.microsoft.com/library/hh472162.aspx) o [Installazione di un Controller di dominio Windows Server 2012 Replica in un dominio esistente (livello 200)](https://technet.microsoft.com/library/jj574134.aspx).

## Riconfigurare il server DNS per la rete virtuale

1. Nel portale di Azure classico, fare clic sul nome della rete virtuale, quindi fare clic sulla scheda **Configura** per [riconfigurare gli indirizzi IP del server DNS per la rete virtuale](virtual-networks-manage-dns-in-vnet.md) allo scopo di usare gli indirizzi IP statici assegnati ai controller di dominio di replica invece degli indirizzi IP di server DNS locali.

2. Per garantire che tutte le macchine virtuali dei controller di dominio di replica sulla rete virtuale siano configurati per usare i server DNS sulla rete virtuale, fare clic su **Macchine virtuali**, fare clic sulla colonna di stato per ciascuna macchina virtuale, quindi fare clic su **Riavvia**. Attendere fino a quando la macchina virtuale mostra lo stato **In esecuzione** prima di provare a effettuare l'accesso.

## Creare macchine virtuali per server applicazioni

1. Ripetere i seguenti passaggi per creare macchine virtuali che vengano eseguite come server applicazioni. Accettare il valore predefinito per tutte le impostazioni a meno che non venga suggerito o richiesto un altro valore.


	Pagina della procedura guidata | Valori da specificare
	------------- | -------------
	**Scegli un'immagine** | Windows Server 2012 R2 Datacenter
	**Configurazione macchina virtuale** | <p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AppServer1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/Conferma: digitare una password</p>
	**Configurazione macchina virtuale** | <p>Servizio cloud: scegliere **Crea un nuovo servizio cloud** per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano altre macchine virtuali che ospiteranno l'applicazione.</p><p>Nome DNS del servizio cloud: specificare un nome univoco a livello globale</p><p>Area/Gruppo di affinità/Rete virtuale: specificare il nome della rete virtuale (ad esempio WestUSVNet).</p><p>Account di archiviazione: scegliere **Usa un account di archiviazione generato automaticamente** per la prima macchina virtuale e quindi selezionare lo stesso nome di account di archiviazione quando si creano altre macchine virtuali che ospiteranno l'applicazione.</p><p>Set di disponibilità: scegliere **Crea set di disponibilità**.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità per la prima macchina virtuale e quindi selezionare lo stesso nome quando si creano altre macchine virtuali.</p>
	**Configurazione macchina virtuale** | <p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p>

2. Dopo avere eseguito il provisioning di ogni macchina virtuale, accedere e aggiungerla al dominio. In **Server Manager** fare clic su **Server locale** > **WORKGROUP** > **Cambia…**, quindi selezionare **Dominio** e digitare il nome del dominio locale. Specificare le credenziali di un utente di dominio e quindi riavviare la macchina virtuale per completare l'aggiunta al dominio.

Per creare le macchine virtuali tramite Windows PowerShell anziché tramite l'interfaccia utente, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Per altre informazioni su come usare Windows PowerShell, vedere [Iniziare a utilizzare i cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) e [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## Risorse aggiuntive

-  [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Come caricare i controller di dominio Hyper-V locali in Azure tramite Azure PowerShell](http://support.microsoft.com/kb/2904015)
-  [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](../active-directory-new-forest-virtual-machine.md)
-  [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IaaS per professionisti IT: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS per professionisti IT: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlet di gestione di Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!---HONumber=AcomDC_0323_2016-->