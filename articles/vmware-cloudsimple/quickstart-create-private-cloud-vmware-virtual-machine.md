---
title: Guida introduttiva - creare una VM VMware in un Cloud privato
description: Descrive come creare e una VM VMware in un Cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5837fd615b8dbf32ff289a2e0d09da9db51a908d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481505"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Creare macchine virtuali VMware nel Cloud privato

Per creare macchine virtuali sul Cloud privato, iniziare è necessario l'accesso al portale CloudSimple dal portale di Azure.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple servizi**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il Cloud privato.
4. Dal **Overview** pagina, fare clic su **passare al portale CloudSimple** per aprire una nuova scheda del browser per CloudSimple portale.  Se richiesto, accedere con Azure le credenziali di accesso.  

    ![Avviare il portale CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Avviare l'interfaccia utente web di vCenter

È ora possibile avviare vCenter per configurare le macchine virtuali e i criteri.

Per accedere a vCenter, iniziare dal portale CloudSimple. Nella Home page, sotto **attività comuni**, fare clic su **avviare vSphere Client**.  Selezionare il Cloud privato e quindi fare clic su **avvio veloce di vSphere Client** sul Cloud privato.

   ![Avviare Client vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Caricare un modello di ISO o vSphere

> [!WARNING]
> Per i caricamenti ISO, usare il client di vSphere HTML5.  Utilizzo di Flash client può comportare un errore.

1. Ottenere il modello di ISO o vSphere che si desidera caricare in vCenter per creare una macchina virtuale e abilitarlo a disponibili nel sistema locale.

2. In vCenter, scegliere il **disco** icona e selezionare **vsanDatastore**. Fare clic su **file** e quindi fare clic su **nuova cartella**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Creare una cartella per archiviare i file ISO.

4. Passare alla nuova cartella creata e fare clic su **Carica file**. Seguire il le istruzioni per caricare l'immagine ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Creare una macchina virtuale in vCenter

1. In vCenter, scegliere il **host e cluster** icona.

2. Fare doppio clic su **carico di lavoro** e selezionare **nuova macchina virtuale**.
    
    ![Crea macchina virtuale](media/create-vcenter-virtual-machine-01.png)

3. Selezionare **creare una nuova macchina virtuale** e fare clic su **successivo**.

    ![Creazione guidata macchina virtuale](media/create-vcenter-virtual-machine-02.png)

4. Nome del computer, selezionare la **carico di lavoro della macchina virtuale** cartella e fare clic su **successivo**.

    ![Selezione nome e cartella](media/create-vcenter-virtual-machine-03.png)

5. Selezionare il **carico di lavoro** risorsa di calcolo e fare clic su **successivo**.

    ![Seleziona risorsa di calcolo](media/create-vcenter-virtual-machine-04.png)

6. Selezionare **vsanDatastore** e fare clic su **successivo**.

    ![Selezionare Archiviazione](media/create-vcenter-virtual-machine-05.png)

7. Mantenere la selezione di compatibilità ESXi 6.5 predefinita e fare clic su **successivo**.

    ![Selezionare la compatibilità](media/create-vcenter-virtual-machine-06.png)

8. Selezionare il sistema operativo guest di ISO per la macchina virtuale e fare clic su **successivo**.

    ![Personalizzazione del sistema operativo guest](media/create-vcenter-virtual-machine-07.png)

9. Selezionare le opzioni di rete e disco rigido. Per unità CD/DVD nuovo, selezionare **file ISO Datastore**.  Se si vuole consentire il traffico dall'indirizzo IP pubblico alla macchina virtuale, selezionare la rete come **vm-1**.

    ![Personalizzazione di selezione dell'hardware](media/create-vcenter-virtual-machine-08.png)

10. Verrà visualizzata una finestra di selezione. Selezionare il file caricato in precedenza nella cartella modelli e le immagini ISO e fare clic su **OK**.

    ![Selezione ISO](media/create-vcenter-virtual-machine-10.png)

11. Rivedere le impostazioni e fare clic su **OK** per creare la macchina virtuale.

    ![Verifica opzioni](media/create-vcenter-virtual-machine-11.png)

La macchina virtuale ora viene aggiunto per le risorse di calcolo del carico di lavoro ed è pronta per l'uso. 

![Nuova macchina virtuale in vCenter](media/create-vcenter-virtual-machine-12.png)

La configurazione di base a questo punto è stata completata. È possibile iniziare a usare il Cloud privato simile al modo in cui si utilizzerebbe l'infrastruttura di macchine virtuali in locale.

Nelle sezioni successive contengono informazioni facoltative sull'impostazione DNS e DHCP Server per il Cloud privato i carichi di lavoro e la modifica della configurazione di rete predefinito.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Aggiungere gli utenti e le origini di identità a vCenter (facoltativo)

CloudSimple assegna un account utente di vCenter predefinito con il nome utente **cloudowner@cloudsimple.local** . Non è necessaria per poter iniziare a usare alcuna impostazione altri account.  In genere CloudSimple Assegna amministratori i privilegi di cui che hanno bisogno per svolgere le normali operazioni.  Configurare l'in active directory locale o Azure AD come una [origine di identità aggiuntive](https://docs.azure.cloudsimple.com/set-vcenter-identity/) sul Cloud privato.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Creare un server DHCP e DNS (facoltativo)

Le applicazioni e carichi di lavoro in esecuzione in un ambiente di Cloud privati richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e assegnazione di indirizzi IP. Un'infrastruttura DNS e DHCP appropriata deve fornire questi servizi. È possibile configurare una macchina virtuale in vCenter per fornire questi servizi Cloud privato nell'ambiente in uso.

### <a name="prerequisites"></a>Prerequisiti

* Un gruppo di porte distribuita con VLAN configurata

* Expressroute per on-premises o i server DNS basato su Internet

* Modello di macchina virtuale o ISO per creare una macchina virtuale

I collegamenti seguenti forniscono indicazioni su come configurare i server DHCP e DNS in Linux e Windows.

### <a name="linux-based-dns-server-setup"></a>Configurazione del Server DNS basato su Linux

Linux sono disponibili diversi pacchetti per la configurazione di server DNS.  Ecco un collegamento alle istruzioni per la configurazione di un server DNS BIND open source.

[Programma di installazione di esempio](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Installazione basata su Windows

Questi articoli Microsoft descrivono come configurare un server Windows come server DNS e un server DHCP.
<br>
[Windows Server come Server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server come Server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizzare la configurazione di rete (facoltativa)

Le pagine di rete nel portale di CloudSimple consentono di specificare la configurazione per le tabelle di firewall e indirizzi IP pubblici per macchine virtuali.

### <a name="allocate-public-ips"></a>Allocare gli indirizzi IP pubblici

1. Passare a **rete > indirizzo IP pubblico** nel portale di CloudSimple.
2. Fare clic su **alloca indirizzo IP pubblico**.
3. Immettere un nome per identificare la voce di indirizzi IP.
4. Selezionare il percorso della Cloud privata.
5. Usare il dispositivo di scorrimento per modificare il timeout di inattività, se necessario.
6. Immettere l'indirizzo IP locale per il quale si desidera assegnare un indirizzo IP pubblico.
7. Se lo si desidera, immettere un nome DNS associato.
8. Fare clic su **Done**.

    ![IP pubblico](media/quick-create-pc-public-ip.png)

Inizia l'attività di allocazione dell'indirizzo IP pubblico. È possibile controllare lo stato dell'attività sul **attività > attività** pagina. Quando l'allocazione è stata completata, la nuova voce viene visualizzata nella pagina indirizzi IP pubblici.

La macchina virtuale a cui è necessario eseguire il mapping di questo indirizzo IP deve essere configurato con l'indirizzo locale specificato in precedenza. La procedura per configurare un indirizzo IP è specifica per il sistema operativo della macchina virtuale. Consultare la documentazione per il sistema operativo di macchina virtuale per la procedura corretta.

#### <a name="example"></a>Esempio

Ad esempio, ecco i dettagli per Ubuntu 16.04.

Aggiungere il metodo statico alla configurazione della famiglia Indirizzo inet nel file ```/etc/network/interfaces```. Modificare i valori di indirizzo, subnet mask e gateway. Per questo esempio, utilizziamo l'interfaccia eth0, indirizzo IP interno 192.168.24.10, l'indirizzo gateway 192.168.24.1 e netmask 255.255.255.0. 

Modificare il file ```interfaces``` .

```
sudo vi /etc/network/interfaces
```

Aggiornare la sezione seguente in ```interfaces``` file.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Disabilitare l'interfaccia.

```
sudo ifdown eth0
```

Abilitare nuovamente l'interfaccia.

```
sudo ifup eth0
```

Per impostazione predefinita, tutto il traffico in ingresso da Internet viene **negato**. Se si desidera aprire qualsiasi altra porta, creare un [tabella firewall](https://docs.azure.cloudsimple.com/firewall/).

Dopo aver configurato un indirizzo IP interno come indirizzo IP statico, verificare che è possibile raggiungere Internet dall'interno della macchina virtuale.

```
ping 8.8.8.8
```

Verificare che è possibile raggiungere la macchina virtuale da Internet usando l'indirizzo IP pubblico.

Assicurarsi che tutte le regole del firewall (iptables) nella macchina virtuale non bloccano la porta 80 in ingresso.

```
netstat -an | grep 80
```

Avviare un server http che è in ascolto sulla porta 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

oppure

```
python3 -m http.server 80
```

Avviare un browser sul desktop e indirizzarlo alla porta 80 per l'indirizzo IP pubblico cercare i file nella macchina virtuale. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regole del firewall CloudSimple predefinito per indirizzo IP pubblico

* Traffico VPN: Tutto il traffico tra (da/a) la connessione VPN e tutte le reti del carico di lavoro e rete di gestione è consentito.
* Traffico interno di cloud privato: Tutto il traffico east-west tra (da/a) carico di lavoro reti e la rete di gestione (illustrato in precedenza) è consentito.
* Traffico Internet:
    * Tutto il traffico in ingresso da Internet viene negato a reti di carico di lavoro e la rete di gestione.
    * Tutto il traffico in uscita a Internet da reti del carico di lavoro o la rete di gestione è consentito.

È inoltre possibile modificare il modo in cui che il traffico viene protetto, utilizzando la funzionalità di regole del Firewall. Per altre informazioni, vedere [configurare le tabelle di firewall e regole](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Installare le soluzioni (facoltative)
È possibile installare le soluzioni nel Cloud privato CloudSimple per sfruttare appieno del Cloud privato ambiente vCenter. È possibile configurare backup, ripristino di emergenza, la replica e altre funzioni per proteggere le macchine virtuali. Sono esempi di gestione del ripristino sito VMware (VMware SRM) e Veeam Backup & Replication.

Per installare una soluzione, è necessario richiedere privilegi aggiuntivi per un periodo limitato. Visualizzare [escalation dei privilegi](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale tramite Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Configurare i gateway VPN nella rete CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
