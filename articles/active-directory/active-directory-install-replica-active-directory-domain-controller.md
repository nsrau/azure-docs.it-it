---
title: Installare un dominio di Active Directory di replica in Azure | Documentazione Microsoft
description: Esercitazione che illustra come installare un controller di dominio da una foresta Active Directory locale in una macchina virtuale di Azure.
services: virtual-network
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: virtual-network
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 21adeddc5896a4c9123bf3525e9b9ab4abbf6fa5


---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure
Questo argomento descrive come installare altri controller di dominio (anche noti come controller di dominio di replica) per un dominio Active Directory locale in Macchine virtuali di Azure in una rete virtuale di Azure.

Altri argomenti di interesse:

* È possibile, se lo si desidera, installare una nuova foresta Active Directory in una rete virtuale di Azure. Per questa procedura, vedere [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md).
* Per le linee guida concettuali sull'installazione di Servizi di dominio Active Directory in una rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagramma dello scenario
In questo scenario gli utenti esterni devono accedere alle applicazioni eseguite nei server aggiunti al dominio. Le macchine virtuali che eseguono i server applicazioni e i controller di dominio di replica vengono installati in una rete virtuale di Azure. La rete virtuale può essere connessa alla rete locale mediante una connessione [VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md), come mostrato nel diagramma seguente, oppure è possibile usare [ExpressRoute](../expressroute/expressroute-locations-providers.md) per una connessione più veloce.

I server dell'applicazione e i controller di dominio vengono distribuiti all'interno di servizi cloud separati per distribuire l'elaborazione del calcolo e all'interno dei [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per una migliore tolleranza di errore.
I controller di dominio eseguono la replica reciproca e con controller di dominio locali usando la replica di Active Directory. Non sono necessari strumenti di sincronizzazione.

![Diagramma di un controller di dominio Active Directory di replica in una rete virtuale di Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Creare un sito di Active Directory per la rete virtuale di Azure
È consigliabile creare in Active Directory un sito che rappresenta l'area di rete corrispondente alla rete virtuale. Ciò contribuisce a ottimizzare l'autenticazione, la replica e altre operazioni del percorso del controller di dominio. La seguente procedura descrive come creare un sito. Per altre informazioni, vedere [ Aggiunta di un nuovo sito](https://technet.microsoft.com/library/cc781496.aspx).

1. Aprire Siti e servizi di Active Directory: **Server Manager** > **Strumenti** > **Siti e servizi di Active Directory**.
2. Creare un sito per rappresentare l'area in cui è stata creata una rete virtuale di Azure: fare clic su **Siti** > **Azione** > **Nuovo sito** > digitare il nome del nuovo sito, ad esempio Azure Stati Uniti occidentali > selezionare un collegamento di sito > **OK**.
3. Creare una subnet e associarla al nuovo sito: fare doppio clic su **Siti** > fare clic con il pulsante destro del mouse su **Subnet** > ** Nuova subnet** > digitare l'intervallo di indirizzi IP della rete virtuale (ad esempio 10.1.0.0/16 nel diagramma dello scenario) > selezionare il nuovo sito di Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Creare una rete virtuale di Azure
1. Nel [portale di Azure classico](https://manage.windowsazure.com) fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale** > **Creazione personalizzata** e usare i valori seguenti per completare la procedura guidata.
   
   | Pagina della procedura guidata | Valori da specificare |
   | --- | --- |
   |  **Dettagli della rete virtuale** |<p>Nome: digitare un nome per la rete virtuale, ad esempio WestUSVNet.</p><p>Area: scegliere l'area più vicina.</p> |
   |  **Server DNS e connettività VPN** |<p>Server DNS: specificare il nome e l'indirizzo IP di uno o più server DNS locali.</p><p>Connettività: selezionare **Configura una VPN Site-to-Site**.</p><p>Rete locale: specificare una nuova rete locale.</p><p>Se si usa ExpressRoute invece di una VPN, vedere [Configurare una connessione ExpressRoute tramite un provider di Exchange](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Connettività da sito a sito** |<p>Nome: digitare un nome per la rete locale.</p><p>Indirizzo IP dispositivo VPN: specificare l'indirizzo IP pubblico del dispositivo che si connetterà alla rete virtuale. Il dispositivo VPN non può trovarsi dietro un NAT.</p><p>Indirizzo: specificare gli intervalli di indirizzi della rete locale (ad esempio 192.168.0.0/16 nel diagramma dello scenario).</p> |
   |  **Spazi degli indirizzi della rete virtuale** |<p>Spazio degli indirizzi: Specificare l'intervallo di indirizzi IP per le macchine virtuali che si desidera eseguire nella rete virtuale di Azure (ad esempio 10.1.0.0/16 nel diagramma dello scenario). Questo intervallo di indirizzi non può sovrapporsi agli intervalli di indirizzi della rete locale.</p><p>Subnet: specificare un nome e un indirizzo per una subnet per i server applicazioni (ad esempio front-end, 10.1.1.0/24) e per i controller di dominio (ad esempio back-end, 10.1.2.0/24).</p><p>Fare clic su **Aggiungi subnet gateway**.</p> |
2. Quindi, si configurerà il gateway di rete virtuale per creare una connessione VPN da sito a sito sicura. Vedere [Configurare un gateway per la rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) per informazioni.
3. Creare la connessione VPN da sito a sito tra la nuova rete virtuale e un dispositivo VPN locale. Vedere [Configurare un gateway per la rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) per informazioni.

## <a name="create-azure-vms-for-the-dc-roles"></a>Creare macchine virtuali di Azure per i ruoli del controller di dominio
Ripetere i passaggi seguenti per creare macchine virtuali per ospitare il ruolo di controller di dominio in base alle esigenze. È consigliabile distribuire almeno due controller di dominio virtuali per fornire ridondanza e tolleranza di errore. Se la rete virtuale di Azure include almeno due controller di dominio configurati in modo analogo (vale a dire se sono entrambi cataloghi globali, eseguono server DNS, non contengono alcun ruolo FSMO e così via), posizionare le macchine virtuali che eseguono tali controller di dominio in un gruppo di disponibilità per una tolleranza di errore migliorata.
Per creare le macchine virtuali tramite Windows PowerShell anziché tramite l'interfaccia utente, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. Nel [portale di Azure classico](https://manage.windowsazure.com) fare clic su **Nuovo** > **Calcolo** > **Macchina virtuale** > **Da raccolta**. Usare i valori nella seguente tabella per completare la procedura guidata. Accettare il valore predefinito per tutte le impostazioni a meno che non venga suggerito o richiesto un altro valore.
   
   | Pagina della procedura guidata | Valori da specificare |
   | --- | --- |
   |  **Scegli un'immagine** |Windows Server 2012 R2 Datacenter |
   |  **Configurazione macchina virtuale** |<p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AzureDC1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/conferma: digitare una password</p> |
   |  **Configurazione macchina virtuale** |<p>Servizio cloud: scegliere <b>Crea un nuovo servizio cloud</b> per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più VM che ospiteranno il ruolo di controller di dominio.</p><p>Nome DNS del servizio cloud: specificare un nome globalmente univoco</p><p>Regione/Gruppo di affinità/Rete virtuale: specificare un nome di rete virtuale (come WestUSVNet).</p><p>Account di archiviazione: scegliere <b>Usa un account di archiviazione generato automaticamente</b> per la prima macchina virtuale e quindi selezionare lo stesso nome di account di archiviazione quando si creano più VM che ospiteranno il ruolo di controller di dominio.</p><p>Set di disponibilità: scegliere <b>Crea set di disponibilità</b>.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima VM e quindi selezionare lo stesso nome quando si creano più VM.</p> |
   |  **Configurazione macchina virtuale** |<p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p> |
2. Collegare un disco a ogni macchina virtuale che eseguirà il ruolo del server di controller di dominio. Il disco aggiuntivo necessario per archiviare il database di Active Directory, log e SYSVOL. Specificare le dimensioni per il disco (ad esempio 10 GB) e lasciare l'opzione **Preferenze cache dell'host** impostata su **Nessuna**. Per la procedura, vedere [Come collegare un disco dati a una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Dopo avere effettuato la prima connessione alla VM, aprire **Server Manager** > **Servizi file e archiviazione** per creare un volume in questo disco usando NTFS.
4. Riservare un indirizzo IP statico per le macchine virtuali che eseguiranno il ruolo di controller di dominio. Per riservare un indirizzo IP statico, scaricare l'Installazione guidata piattaforma Web Microsoft e [installare Azure PowerShell](../powershell-install-configure.md) , quindi eseguire il cmdlet Set-AzureStaticVNetIP. Ad esempio:
   
    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Per altre informazioni sull'impostazione di un indirizzo IP statico, vedere [Configurare un indirizzo IP interno statico per una macchina virtuale](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installare Servizi di dominio Active Directory in macchine virtuali di Azure
Accedere a una macchina virtuale e verificare che sia disponibile la connettività nella VPN da sito a sito o la connessione ExpressRoute alle risorse sulla rete locale, quindi installare Servizi di dominio Active Directory nelle macchine virtuali di Azure. È possibile usare la stessa procedura che consente di installare un controller di dominio aggiuntivo nella rete locale (interfaccia utente, Windows PowerShell o un file di risposta). Durante l'installazione di Servizi di dominio Active Directory, assicurarsi di specificare il nuovo volume per il percorso del database Active Directory, i log e SYSVOL. Se è necessario un aggiornamento nell'installazione di Active Directory Domain Services, vedere [Installazione di Active Directory Domain Services (Livello 100)](https://technet.microsoft.com/library/hh472162.aspx) o [Installare un Controller di dominio Windows Server 2012 Replica in un dominio esistente (livello 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Riconfigurare il server DNS per la rete virtuale
1. Nel [portale di Azure classico](https://manage.windowsazure.com) fare clic sul nome della rete virtuale e quindi fare clic sulla scheda **Configura** per [riconfigurare gli indirizzi IP del server DNS per la rete virtuale](../virtual-network/virtual-networks-manage-dns-in-vnet.md) allo scopo di usare gli indirizzi IP statici assegnati ai controller di dominio di replica invece degli indirizzi IP di server DNS locali.
2. Per garantire che tutte le macchine virtuali dei controller di dominio di replica sulla rete virtuale siano configurate per usare i server DNS sulla rete virtuale, fare clic su **Macchine virtuali**, fare clic sulla colonna di stato per ogni macchina virtuale e quindi fare clic su **Riavvia**. Attendere fino a quando la macchina virtuale mostra lo stato **In esecuzione** prima di provare a effettuare l'accesso.

## <a name="create-vms-for-application-servers"></a>Creare macchine virtuali per server applicazioni
1. Ripetere i seguenti passaggi per creare macchine virtuali che vengano eseguite come server applicazioni. Accettare il valore predefinito per tutte le impostazioni a meno che non venga suggerito o richiesto un altro valore.
   
   | Pagina della procedura guidata | Valori da specificare |
   | --- | --- |
   |  **Scegli un'immagine** |Windows Server 2012 R2 Datacenter |
   |  **Configurazione macchina virtuale** |<p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AppServer1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/conferma: digitare una password</p> |
   |  **Configurazione macchina virtuale** |<p>Servizio cloud: scegliere **Crea un nuovo servizio cloud** per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più VM che ospiteranno l'applicazione.</p><p>Nome DNS del servizio cloud: specificare un nome globalmente univoco</p><p>Regione/Gruppo di affinità/Rete virtuale: specificare un nome di rete virtuale (come WestUSVNet).</p><p>Account di archiviazione: scegliere **Usa un account di archiviazione generato automaticamente** per la prima macchina virtuale e quindi selezionare lo stesso nome account di archiviazione quando si creano più VM che ospiteranno l'applicazione.</p><p>Set di disponibilità: scegliere **Crea set di disponibilità**.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima VM e quindi selezionare lo stesso nome quando si creano più VM.</p> |
   |  **Configurazione macchina virtuale** |<p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p> |
2. Dopo avere eseguito il provisioning di ogni macchina virtuale, accedere e aggiungerla al dominio. In **Server Manager** fare clic su **Server locale** > **GRUPPO DI LAVORO** > **Modifica...** e quindi selezionare **Dominio** e digitare il nome del dominio locale. Specificare le credenziali di un utente di dominio e quindi riavviare la macchina virtuale per completare l'aggiunta al dominio.

Per creare le macchine virtuali tramite Windows PowerShell anziché tramite l'interfaccia utente, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Per altre informazioni su come usare Windows PowerShell, vedere [Iniziare a utilizzare i cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) e [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Come caricare i controller di dominio Hyper-V locali in Azure tramite Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Installare una nuova foresta Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md)
* [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS per professionisti IT: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS per professionisti IT: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Cmdlet di gestione di Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png



<!--HONumber=Nov16_HO3-->


