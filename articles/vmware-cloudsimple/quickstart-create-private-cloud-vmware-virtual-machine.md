---
title: Guida introduttiva-creare una macchina virtuale VMware in un cloud privato
description: Viene descritto come creare e una macchina virtuale VMware in un cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8d4712ca57801c15510ffcaf54852ce9287d343b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972408"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Creazione di macchine virtuali VMware nel cloud privato

Per creare macchine virtuali nel cloud privato, iniziare accedendo al portale di CloudSimple dalla portale di Azure.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

1. Selezionare **Tutti i servizi**.
2. Cercare i **Servizi CloudSimple**.
3. Selezionare il servizio CloudSimple in cui si vuole creare il cloud privato.
4. Nella pagina **Panoramica** fare clic su **Vai al portale di CloudSimple** per aprire una nuova scheda del browser per il portale di CloudSimple.  Se richiesto, accedere con le credenziali di accesso di Azure.  

    ![Avviare il portale di CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Avviare vCenter Web-interfaccia utente

È ora possibile avviare vCenter per configurare le macchine virtuali e i criteri.

Per accedere a vCenter, iniziare dal portale di CloudSimple. Nella Home page, in **attività comuni**, fare clic su **avvia client vSphere**.  Selezionare il cloud privato e quindi fare clic su **avvia client vSphere** nel cloud privato.

   ![Avviare vSphere client](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Caricare un modello ISO o vSphere

  > [!WARNING]
  > Per il caricamento ISO, usare il client di vSphere HTML5.  L'uso di Flash client può causare un errore.

1. Ottenere il modello ISO o vSphere che si vuole caricare in vCenter per creare una macchina virtuale e renderla disponibile nel sistema locale.
2. In vCenter fare clic sull'icona del **disco** e selezionare **vsanDatastore**. Fare clic su **file** e quindi su **nuova cartella**.
    ![ISO vCenter](media/vciso00.png)

3. Creare una cartella denominata "ISOs e modelli".

4. Passare alla cartella ISOs in ISOs e modelli e fare clic su **Carica file**. Seguire le istruzioni visualizzate per caricare l'immagine ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Creare una macchina virtuale in vCenter

1. In vCenter fare clic sull'icona **host e cluster** .

2. Fare clic con il pulsante destro del mouse su **carico di lavoro** e selezionare **nuova macchina virtuale**
    ![Nuova macchina virtuale](media/vcvm01.png)

3. Selezionare **Crea nuova macchina virtuale** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm02.png)

4. Assegnare un nome al computer, selezionare il percorso della macchina **virtuale del carico di lavoro** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm03.png)

5. Selezionare la risorsa di calcolo del **carico di lavoro** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm04.png)

6. Selezionare **vsanDatastore** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm05.png)

7. Mantieni la selezione di compatibilità predefinita ESXi 6,5 e fai clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm06.png)

8. Selezionare il sistema operativo guest dell'ISO per la macchina virtuale che si sta creando e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm07.png)

9. Selezionare disco rigido e opzioni di rete. Per la nuova unità CD/DVD selezionare **archivio dati file ISO**.  Se si vuole consentire il traffico dall'indirizzo IP pubblico alla macchina virtuale, selezionare la rete come **VM-1**.
    ![Nuova macchina virtuale](media/vcvm08.png)

10. Verrà visualizzata una finestra di selezione. Selezionare il file caricato in precedenza nella cartella ISO e modelli e fare clic su **OK**.
    ![Nuova macchina virtuale](media/vcvm10.png)

11. Verificare le impostazioni e fare clic su **OK** per creare la macchina virtuale.
    ![Nuova macchina virtuale](media/vcvm11.png)

La VM viene ora aggiunta alle risorse di calcolo del carico di lavoro ed è pronta per l'uso. 
![Nuova macchina virtuale](media/vcvm12.png)

Il programma di installazione di base è ora completo. È possibile iniziare a usare il cloud privato in modo analogo a come si usa l'infrastruttura della macchina virtuale locale.

Le sezioni seguenti contengono informazioni facoltative sulla configurazione dei server DNS e DHCP per i carichi di lavoro del cloud privato e la modifica della configurazione di rete predefinita.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Aggiungere utenti e origini identità a vCenter (facoltativo)

CloudSimple assegna un account utente vCenter predefinito con username `cloudowner@cloudsimple.local`. Per iniziare, non è necessaria alcuna configurazione di account aggiuntiva.  CloudSimple assegna normalmente agli amministratori i privilegi necessari per eseguire le normali operazioni.  Configurare Active Directory locale o Azure AD come [origine di identità aggiuntiva](set-vcenter-identity.md) nel cloud privato.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Creazione di un server DNS e DHCP (facoltativo)

Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e l'assegnazione di indirizzi IP. Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS corretta. È possibile configurare una macchina virtuale in vCenter per fornire questi servizi nell'ambiente cloud privato.

Prerequisiti

* Un gruppo di porte distribuite con VLAN configurata

* Instrada il programma di installazione a server DNS locali o basati su Internet

* Modello di macchina virtuale o ISO per creare una macchina virtuale

I collegamenti seguenti forniscono indicazioni sulla configurazione dei server DHCP e DNS in Linux e Windows.

#### <a name="linux-based-dns-server-setup"></a>Configurazione del server DNS basato su Linux

Linux offre diversi pacchetti per la configurazione dei server DNS.  Di seguito è riportato un collegamento alle istruzioni per la configurazione di un server DNS di binding open source.

[Configurazione di esempio](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Installazione basata su Windows

Negli argomenti Microsoft viene descritto come configurare un server Windows come server DNS e come server DHCP.

[Windows Server come server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server come server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizzare la configurazione di rete (facoltativo)

Le pagine di rete nel portale di CloudSimple consentono di specificare la configurazione per le tabelle del firewall e gli indirizzi IP pubblici per le macchine virtuali.

### <a name="allocate-public-ips"></a>Allocare indirizzi IP pubblici

1. Passare a **rete > IP pubblico** nel portale di CloudSimple.
2. Fare clic su **ALLOCA IP pubblico**.
3. Immettere un nome per identificare la voce dell'indirizzo IP.
4. Mantieni il percorso predefinito.
5. Se lo si desidera, utilizzare il dispositivo di scorrimento per modificare il timeout di inattività.
6. Immettere l'indirizzo IP locale per cui si vuole assegnare un indirizzo IP pubblico.
7. Se lo si desidera, immettere un nome DNS associato.
8. Fare clic su **Done**.

    ![IP pubblico](media/quick-create-pc-public-ip.png)

Viene avviata l'attività di allocazione dell'indirizzo IP pubblico. È possibile controllare lo stato dell'attività nella pagina **attività >** attività. Al termine dell'allocazione, la nuova voce viene visualizzata nella pagina indirizzi IP pubblici.

La macchina virtuale a cui deve essere eseguito il mapping di questo indirizzo IP deve essere configurata con l'indirizzo locale specificato in precedenza. La procedura per configurare un indirizzo IP è specifica del sistema operativo della macchina virtuale. Per la procedura corretta, consultare la documentazione relativa al sistema operativo della VM.

#### <a name="example"></a>Esempio

Ad esempio, di seguito sono riportati i dettagli per Ubuntu 16,04.

Aggiungere il metodo statico alla configurazione della famiglia di indirizzi inet nel file/etc/network/interfaces. Modificare i valori di indirizzo, netmask e gateway. Per questo esempio si usa l'interfaccia eth0, l'indirizzo IP interno 192.168.24.10, l'indirizzo del gateway 192.168.24.1 e netmask 255.255.255.0. Per l'ambiente in uso, le informazioni sulla subnet disponibili vengono fornite nel messaggio di posta elettronica di benvenuto.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Disabilitare manualmente l'interfaccia.

```
sudo ifdown eth0
```
Abilitare di nuovo l'interfaccia manualmente.

```
sudo ifup eth0
```

Per impostazione predefinita, tutto il traffico in ingresso proveniente da Internet viene **negato**. Se si desidera aprire qualsiasi altra porta, creare una tabella del [Firewall](firewall.md).

Dopo aver configurato un indirizzo IP interno come indirizzo IP statico, verificare che sia possibile raggiungere Internet dalla macchina virtuale.

```
ping 8.8.8.8
```
Verificare anche che sia possibile raggiungere la macchina virtuale da Internet usando l'indirizzo IP pubblico.

Verificare che le regole iptable nella macchina virtuale non blocchino la porta 80 in ingresso.
        
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
Avviare un browser sul desktop e puntare alla porta 80 per l'indirizzo IP pubblico per esplorare i file nella macchina virtuale.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regole del firewall CloudSimple predefinite per l'indirizzo IP pubblico

* Traffico VPN: Tutto il traffico tra (da/a) la VPN e tutte le reti del carico di lavoro e la rete di gestione è consentita.
* Traffico interno del cloud privato: È consentito tutto il traffico est-ovest tra le reti del carico di lavoro (da/a) e la rete di gestione (mostrata in precedenza).
* Traffico Internet:
  * Tutto il traffico in ingresso proveniente da Internet viene negato alle reti del carico di lavoro e alla rete di gestione.
  * Tutto il traffico in uscita verso Internet dalle reti del carico di lavoro o dalla rete di gestione è consentito.

È anche possibile modificare il modo in cui il traffico viene protetto usando la funzionalità regole del firewall. Per altre informazioni, vedere [configurare le regole e le tabelle del firewall](firewall.md).

## <a name="install-solutions-optional"></a>Installa soluzioni (facoltativo)

È possibile installare le soluzioni nel cloud privato di CloudSimple per sfruttare appieno l'ambiente vCenter del cloud privato. Per proteggere le macchine virtuali, è possibile configurare il backup, il ripristino di emergenza, la replica e altre funzioni. Gli esempi includono VMware Site Recovery Manager (VMware SRM) e Veeam Backup & Replication.

Per installare una soluzione, è necessario richiedere privilegi aggiuntivi per un periodo di tempo limitato. Vedere [privilegi](escalate-private-cloud-privileges.md)di escalation.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale usando Azure ExpressRoute](on-premises-connection.md)
* [Configurare i gateway VPN nella rete CloudSimple](vpn-gateway.md)
