<properties
    pageTitle="Installazione di una foresta Active Directory in una rete virtuale di Azure | Microsoft Azure"
    description="Un'esercitazione che illustra come creare una nuova foresta di Active Directory in una macchina virtuale (VM) in una rete virtuale di Azure."
    services="active-directory, virtual-network"
    keywords="macchina virtuale active directory, installazione di una foresta active directory, video su azure active directory  "
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>



# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installazione di una nuova foresta Active Directory in una rete virtuale di Azure

In questo argomento viene illustrato come creare un nuovo ambiente Windows Server Active Directory su una macchina virtuale (VM) in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). In questo caso, la rete virtuale di Azure non è connessa a una rete locale.

Altri argomenti di interesse:

- Per un video che illustra questa procedura, vedere [Come installare una nuova foresta Active Directory in una rete virtuale di Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Facoltativamente, è possibile [configurare una VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) e quindi installare una nuova foresta, oppure estendere una foresta locale a una rete virtuale di Azure. Per la procedura, vedere [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Per le linee guida concettuali sull'installazione di Servizi di dominio Active Directory in una rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagramma dello scenario

In questo scenario gli utenti esterni devono accedere alle applicazioni eseguite nei server aggiunti al dominio. Le macchine virtuali che eseguono i server applicazioni e le macchine virtuali che eseguono i controller di dominio sono installate nel proprio servizio cloud in una rete virtuale di Azure. Sono anche incluse in un set di disponibilità per una migliore tolleranza di errore.

![Foresta Active Directory in una macchina virtuale in Rete virtuale di Azure ][1] 7
## <a name="how-does-this-differ-from-onpremises"></a>Differenze rispetto all'installazione locale

Non esistono molte differenze tra l'installazione locale di un controller di dominio e l'installazione in Azure. Le differenze principali sono elencate nella tabella seguente.

Configurazione  | Locale  | rete virtuale di Azure
------------- | -------------  | ------------
**Indirizzo IP del controller di dominio**  | Assegnare un indirizzo IP statico nelle proprietà della scheda di rete   | Eseguire il cmdlet Set-AzureStaticVNetIP per assegnare un indirizzo IP statico
**Resolver del client DNS**  | Impostare l'indirizzo del server DNS preferito e quello alternativo nelle proprietà della scheda di rete dei membri del dominio   | Impostare l'indirizzo del server DNS nelle proprietà della rete virtuale
**Archiviazione del database di Active Directory**  | Modificare facoltativamente il percorso di archiviazione predefinito da C:\  | È necessario modificare il percorso di archiviazione predefinito da C:\



## <a name="create-an-azure-virtual-network"></a>Creare una rete virtuale di Azure

1. Accedere al portale di Microsoft Azure classico.
2. Creare una rete virtuale. Fare clic su **Reti** > **Crea rete virtuale**. Usare i valori nella tabella seguente per completare la procedura guidata.

    Pagina della procedura guidata  | Valori da specificare
    ------------- | -------------
    **Dettagli della rete virtuale**  | <p>Nome: immettere un nome per la rete virtuale</p><p>Area: scegliere l'area più vicina</p>
    **DNS e VPN**  | <p>Lasciare il campo relativo al server DNS vuoto</p><p>Non selezionare alcuna opzione VPN</p>
    **Spazi degli indirizzi della rete virtuale**  | <p>Nome subnet: immettere un nome per la subnet</p><p>IP iniziale: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS

Ripetere i passaggi seguenti per creare macchine virtuali per ospitare il ruolo di controller di dominio in base alle esigenze. È consigliabile distribuire almeno due controller di dominio virtuali per fornire ridondanza e tolleranza di errore. Se la rete virtuale di Azure include almeno due controller di dominio configurati in modo analogo (vale a dire se sono entrambi cataloghi globali, eseguono server DNS, non contengono alcun ruolo FSMO e così via), posizionare le macchine virtuali che eseguono tali controller di dominio in un gruppo di disponibilità per una tolleranza di errore migliorata.

Per creare le macchine virtuali tramite Windows PowerShell anziché tramite l'interfaccia utente, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Nel portale classico fare clic su **Nuovo** > **Calcolo** > **Macchina virtuale** > **Da raccolta**. Usare i valori nella seguente tabella per completare la procedura guidata. Accettare il valore predefinito per tutte le impostazioni a meno che non venga suggerito o richiesto un altro valore.

    Pagina della procedura guidata  | Valori da specificare
    ------------- | -------------
    **Scegli un'immagine**  | Windows Server 2012 R2 Datacenter
    **Configurazione macchina virtuale**  | <p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AzureDC1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/conferma: digitare una password</p>
    **Configurazione macchina virtuale**  | <p>Servizio cloud: scegliere <b>Crea un nuovo servizio cloud</b> per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più VM che ospiteranno il ruolo di controller di dominio.</p><p>Nome DNS del servizio cloud: specificare un nome globalmente univoco</p><p>Regione/Gruppo di affinità/Rete virtuale: specificare un nome di rete virtuale (come WestUSVNet).</p><p>Account di archiviazione: scegliere <b>Usa un account di archiviazione generato automaticamente</b> per la prima macchina virtuale e quindi selezionare lo stesso nome di account di archiviazione quando si creano più VM che ospiteranno il ruolo di controller di dominio.</p><p>Set di disponibilità: scegliere <b>Crea set di disponibilità</b>.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima VM e quindi selezionare lo stesso nome quando si creano più VM.</p>
    **Configurazione macchina virtuale**  | <p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p>
2. Collegare un disco a ogni macchina virtuale che eseguirà il ruolo del server di controller di dominio. Il disco aggiuntivo necessario per archiviare il database di Active Directory, log e SYSVOL. Specificare le dimensioni per il disco (ad esempio 10 GB) e lasciare l'opzione **Preferenze cache dell'host** impostata su **Nessuna**. Per la procedura, vedere [Come collegare un disco dati a una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Dopo avere effettuato la prima connessione alla VM, aprire **Server Manager** > **Servizi file e archiviazione** per creare un volume in questo disco usando NTFS.
4. Riservare un indirizzo IP statico per le macchine virtuali che eseguiranno il ruolo di controller di dominio. Per riservare un indirizzo IP statico, scaricare l'Installazione guidata piattaforma Web Microsoft e [installare Azure PowerShell](../powershell-install-configure.md) , quindi eseguire il cmdlet Set-AzureStaticVNetIP. Ad esempio:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Per altre informazioni sull'impostazione di un indirizzo IP statico, vedere [Configurare un indirizzo IP interno statico per una macchina virtuale](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Installare Windows Server Active Directory

[Installare Servizi di dominio Active Directory](https://technet.microsoft.com/library/jj574166.aspx) usando la procedura per l'installazione locale, ovvero usando l'interfaccia utente, un file di risposte o Windows PowerShell. Per installare una nuova foresta è necessario fornire le credenziali di amministratore. Per specificare il percorso del database di Active Directory, dei log e di SYSVOL, modificare il percorso di archiviazione predefinito dall'unità del sistema operativo al disco dati aggiuntivo precedentemente collegato alla VM.

Al termine dell'installazione del controller di dominio, connettersi nuovamente alla VM e accedere al controller di dominio. Ricordare di specificare le credenziali del dominio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Reimpostare il server DNS per la rete virtuale di Azure

1. Reimpostare le impostazioni del server di inoltro DNS nel nuovo controller di dominio o nel server DNS.
  1. In Server Manager fare clic su **Strumenti ** > **DNS**.
  2. In **Gestore DNS** fare clic con il pulsante destro del mouse sul nome del server DNS e scegliere **Proprietà**.
  3. Nella scheda **Server di inoltro** fare clic sull'indirizzo IP del server di inoltro, quindi scegliere **Modifica**.  Selezionare l'indirizzo IP e fare clic su **Elimina**.
  4. Fare clic su **OK** per chiudere l'editor, quindi nuovamente su **OK** per chiudere le proprietà del server DNS.
2. Aggiornare l'impostazione del server DNS per la rete virtuale.
  1. Fare clic su **Reti virtuali** > fare doppio clic sulla rete virtuale creata > **Configura** > **Server DNS**, quindi digitare il nome e il DIP di una delle macchine virtuali che eseguono il ruolo del controller di dominio o del server DNS e fare clic su **Salva**.
  2. Selezionare la VM e fare clic su **Restart** per fare in modo che la VM configuri le impostazioni del resolver DNS con l'indirizzo IP del nuovo server DNS.


## <a name="create-vms-for-domain-members"></a>Creare macchine virtuali per i membri del dominio

1. Ripetere i seguenti passaggi per creare macchine virtuali che vengano eseguite come server applicazioni. Accettare il valore predefinito per tutte le impostazioni a meno che non venga suggerito o richiesto un altro valore.

    Pagina della procedura guidata  | Valori da specificare
    ------------- | -------------
    **Scegli un'immagine**  | Windows Server 2012 R2 Datacenter
    **Configurazione macchina virtuale**  | <p>Nome macchina virtuale: digitare un nome con etichetta singola (ad esempio AppServer1).</p><p>Nuovo nome utente: digitare il nome di un utente. Questo utente sarà membro del gruppo Administrators locale nella macchina virtuale. Questo nome sarà necessario per accedere alla macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova password/conferma: digitare una password</p>
    **Configurazione macchina virtuale**  | <p>Servizio cloud: scegliere **Crea un nuovo servizio cloud** per la prima macchina virtuale e selezionare lo stesso nome di servizio cloud quando si creano più VM che ospiteranno l'applicazione.</p><p>Nome DNS del servizio cloud: specificare un nome globalmente univoco</p><p>Regione/Gruppo di affinità/Rete virtuale: specificare un nome di rete virtuale (come WestUSVNet).</p><p>Account di archiviazione: scegliere **Usa un account di archiviazione generato automaticamente** per la prima macchina virtuale e quindi selezionare lo stesso nome account di archiviazione quando si creano più VM che ospiteranno l'applicazione.</p><p>Set di disponibilità: scegliere **Crea set di disponibilità**.</p><p>Nome set di disponibilità: digitare un nome per il set di disponibilità impostato quando si crea la prima VM e quindi selezionare lo stesso nome quando si creano più VM.</p>
    **Configurazione macchina virtuale**  | <p>Selezionare <b>Installa l'agente di macchine virtuali</b> ed eventuali altre estensioni necessarie.</p>
2. Dopo avere eseguito il provisioning di ogni macchina virtuale, accedere e aggiungerla al dominio. In **Server Manager** fare clic su **Server locale** > **GRUPPO DI LAVORO** > **Modifica...** e quindi selezionare **Dominio** e digitare il nome del dominio locale. Specificare le credenziali di un utente di dominio e quindi riavviare la macchina virtuale per completare l'aggiunta al dominio.

Per creare le macchine virtuali tramite Windows PowerShell anziché tramite l'interfaccia utente, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Per altre informazioni su come usare Windows PowerShell, vedere [Iniziare a utilizzare i cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) e [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## <a name="see-also"></a>Vedere anche

-  [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configura una VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IaaS per professionisti IT: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS per professionisti IT: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Panoramica di Rete virtuale.](../virtual-network/virtual-networks-overview.md)
-  [Come installare e configurare Azure PowerShell](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Impostare un indirizzo IP statico per una macchina virtuale di Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Come assegnare un IP statico a una macchina virtuale di Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installare una nuova foresta Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introduzione alla virtualizzazione di Servizi di dominio Active Directory (livello 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png



<!--HONumber=Oct16_HO2-->


