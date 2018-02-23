---
title: Installare controller di dominio di replica per domini Active Directory locali in computer virtuali di Azure | Microsoft Docs
description: Informazioni su come installare altri controller di dominio di replica per un dominio Active Directory locale in macchine virtuali di Azure in una rete virtuale di Azure.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7624d588e958985a73c5b40e8010e18e8879cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure
Questo articolo descrive come installare altri controller di dominio da usare come controller di dominio di replica per un dominio Active Directory locale in macchine virtuali di Azure in una rete virtuale di Azure. È anche possibile [installare una foresta Windows Server Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md). Per informazioni sulle procedure di installazione di Active Directory Domain Services in una rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagramma dello scenario
In questo scenario gli utenti esterni devono accedere alle applicazioni eseguite nei server aggiunti al dominio. Le macchine virtuali che eseguono i server applicazioni e i controller di dominio di replica vengono installati in una rete virtuale di Azure. La rete virtuale può essere connessa alla rete locale mediante [ExpressRoute](../expressroute/expressroute-locations-providers.md) o è possibile usare una connessione [VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) come mostrato nel diagramma seguente: 

![Diagramma di un controller di dominio Active Directory di replica in una rete virtuale di Azure][1]

I server dell'applicazione e i controller di dominio vengono distribuiti all'interno di servizi cloud separati per distribuire l'elaborazione del calcolo e all'interno dei set di disponibilità per una migliore tolleranza di errore.
I controller di dominio eseguono la replica reciproca e con controller di dominio locali usando la replica di Active Directory. Non sono necessari strumenti di sincronizzazione.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Creare un sito di Active Directory locale per la rete virtuale di Azure
È possibile creare in Active Directory un sito che rappresenta l'area di rete corrispondente alla rete virtuale. Ciò contribuisce a ottimizzare l'autenticazione, la replica e altre operazioni del percorso del controller di dominio. La seguente procedura descrive come creare un sito. Per altre informazioni, vedere [ Aggiunta di un nuovo sito](https://technet.microsoft.com/library/cc781496.aspx).

1. Aprire Siti e servizi di Active Directory: **Server Manager** > **Strumenti** > **Siti e servizi di Active Directory**.
2. Creare un sito per rappresentare l'area in cui è stata creata una rete virtuale di Azure: fare clic su **Siti** > **Azione** > **Nuovo sito** > digitare il nome del nuovo sito, ad esempio Azure Stati Uniti occidentali > selezionare un collegamento di sito > **OK**.
3. Creare una subnet e associarla al nuovo sito: fare doppio clic su **Siti** > fare clic con il pulsante destro del mouse su **Subnet** >  **Nuova subnet** > digitare l'intervallo di indirizzi IP della rete virtuale (ad esempio 10.1.0.0/16 nel diagramma dello scenario) > selezionare il nuovo sito di Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Creare una rete virtuale di Azure
Per creare una rete virtuale di Azure e configurare una connessione VPN da sito a sito, eseguire la procedura riportata nell'articolo [Creare una connessione da sito a sito nel portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

È anche possibile configurare il gateway di rete virtuale per creare una connessione VPN da sito a sito sicura. Creare la connessione VPN da sito a sito tra la nuova rete virtuale e un dispositivo VPN locale. Per informazioni, vedere [Configurare un gateway per la rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>Creare macchine virtuali di Azure per i ruoli del controller di dominio
Per creare macchine virtuali per ospitare il ruolo di controller di dominio, ripetere i passaggi descritti in [Creare una macchina virtuale Windows con il portale di Azure](../virtual-machines/windows/quick-create-portal.md) in base alle esigenze. Distribuire almeno due controller di dominio virtuali per garantire ridondanza e tolleranza di errore. Se la rete virtuale di Azure ha almeno due controller di dominio configurati in modo analogo, è possibile posizionare le macchine virtuali che eseguono tali controller di dominio in un set di disponibilità per una migliore tolleranza di errore.

Per creare le macchine virtuali tramite Windows PowerShell anziché tramite il portale di Azure, vedere [Usare Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Riservare un indirizzo IP statico per le macchine virtuali che eseguiranno il ruolo di controller di dominio. Per riservare un indirizzo IP statico, scaricare l'Installazione guidata piattaforma Web Microsoft e [installare Azure PowerShell](/powershell/azure/overview) , quindi eseguire il cmdlet Set-AzureStaticVNetIP. Ad esempio: 

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Per altre informazioni sull'impostazione di un indirizzo IP statico, vedere [Configurare un indirizzo IP interno statico per una macchina virtuale](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installare Servizi di dominio Active Directory in macchine virtuali di Azure
Accedere a una macchina virtuale e verificare che sia disponibile la connettività nella VPN da sito a sito o la connessione ExpressRoute alle risorse sulla rete locale, quindi installare Servizi di dominio Active Directory nelle macchine virtuali di Azure. È possibile usare la stessa procedura che consente di installare un controller di dominio aggiuntivo nella rete locale (interfaccia utente, Windows PowerShell o un file di risposta). Durante l'installazione di Servizi di dominio Active Directory, assicurarsi di specificare il nuovo volume per il percorso del database Active Directory, i log e SYSVOL. Se è necessario un aggiornamento nell'installazione di Active Directory Domain Services, vedere [Installazione di Active Directory Domain Services (Livello 100)](https://technet.microsoft.com/library/hh472162.aspx) o [Installare un Controller di dominio Windows Server 2012 Replica in un dominio esistente (livello 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Riconfigurare il server DNS per la rete virtuale
1. Per ottenere un elenco di nomi di rete virtuale, nel [portale di Azure](https://portal.azure.com) cercare le *reti virtuali* e quindi selezionare **Reti virtuali** per visualizzare l'elenco. 
2. Aprire la rete virtuale che si desidera gestire e quindi [riconfigurare gli indirizzi IP del server DNS per la rete virtuale](../virtual-network/virtual-network-manage-network.md#dns-servers) in modo da usare gli indirizzi IP statici assegnati ai controller di dominio di replica invece degli indirizzi IP di server DNS locali.
3. Per assicurarsi che tutte le macchine virtuali del controller di dominio di replica nella rete virtuale siano configurate in modo da usare i server DNS nella rete virtuale:
  1. Selezionare **Macchine virtuali**.
  2. Selezionare le macchine virtuali e quindi scegliere **Riavvia**. 
  3. Attendere che lo stato della macchina virtuale indichi di nuovo **In esecuzione** e quindi eseguire l'accesso.

## <a name="create-vms-for-application-servers"></a>Creare macchine virtuali per server applicazioni

Per creare macchine virtuali per ospitare il ruolo di server applicazioni, ripetere i passaggi descritti in [Creare una macchina virtuale Windows con il portale di Azure](../virtual-machines/windows/quick-create-portal.md) in base alle esigenze. Per creare le macchine virtuali tramite Microsoft PowerShell anziché tramite il portale di Azure, vedere [Usare Azure PowerShell per creare e configurare macchine virtuali basate su Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). La tabella seguente contiene le impostazioni consigliate.

| Impostazione | Valori |
| --- | --- |
|  **Scegli un'immagine** | Windows Server 2012 R2 Datacenter |
|  **Configurazione macchina virtuale** |<p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AppServer1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/conferma: digitare una password</p> |
|  **Configurazione macchina virtuale** |<p>Servizio cloud: scegliere **Crea un nuovo servizio cloud** per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più VM che ospiteranno l'applicazione.</p><p>Nome DNS del servizio cloud: specificare un nome globalmente univoco</p><p>Regione/Gruppo di affinità/Rete virtuale: specificare un nome di rete virtuale (come WestUSVNet).</p><p>Account di archiviazione: scegliere **Usa un account di archiviazione generato automaticamente** per la prima macchina virtuale e quindi selezionare lo stesso nome account di archiviazione quando si creano più VM che ospiteranno l'applicazione.</p><p>Set di disponibilità: scegliere **Crea set di disponibilità**.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima VM e quindi selezionare lo stesso nome quando si creano più VM.</p> |
|  **Configurazione macchina virtuale** |<p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p> |
  
Dopo avere eseguito il provisioning di ogni macchina virtuale, accedere e aggiungerla al dominio. 
1. In **Server Manager** &gt; **Server locale** &gt; **WORKGROUP** &gt; **Modifica** selezionare **Dominio**.
2. Immettere il nome del dominio locale. 
3. Immettere le credenziali di un utente di dominio.
4. Riavviare la VM.

## <a name="additional-resources"></a>Risorse aggiuntive

* Per altre informazioni su come usare Windows PowerShell, vedere [Iniziare a utilizzare i cmdlet di Azure](/powershell/azure/overview) e [Informazioni di riferimento sui cmdlet di Azure](/powershell/azure/get-started-azureps).
* [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Come caricare i controller di dominio Hyper-V locali in Azure tramite Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Installare una nuova foresta Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md)
* [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS per professionisti IT: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS per professionisti IT: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlet di gestione di Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
