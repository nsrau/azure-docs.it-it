---
title: Guida introduttiva - Creare una macchina virtuale di Azure VMware in un cloud privato - Soluzione VMware di Azure da CloudSimple
description: Descrive come creare una macchina virtuale Di Azure VMware in un cloud privato CloudSimpleDescribes how to create an Azure VMware VM on a CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566149"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Creare macchine virtuali VMware nel cloud privatoCreate VMware virtual machines on your Private Cloud

Per creare macchine virtuali nel cloud privato, iniziare accedendo al portale CloudSimple dal portale di Azure.To create virtual machines on your Private Cloud, start by accessing the CloudSimple portal from the Azure portal.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple Services**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il cloud privato.
4. Nella pagina **Panoramica** fare clic su **Vai al portale CloudSimple** per aprire una nuova scheda del browser per il portale CloudSimple.  Se richiesto, accedere con le credenziali di accesso di Azure.If prompted, sign in with your Azure sign in credentials.  

    ![Avvia il portale CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Avvia l'interfaccia web di vCenter

È ora possibile avviare vCenter per configurare le macchine virtuali e i criteri.

Per accedere a vCenter, iniziare dal portale CloudSimple. Nella home page, in **Attività comuni**, fare clic su Avvia **client vSphere**.  Selezionare il cloud privato e quindi fare clic su **Avvia client vSphere** sul cloud privato.

   ![Avvia vSphere Client](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Caricare un modello ISO o vSphere

  > [!WARNING]
  > Per il caricamento ISO, utilizzare il client HTML5 vSphere.  L'utilizzo di un client Flash potrebbe generare un errore.

1. Ottenere il modello ISO o vSphere che si desidera caricare in vCenter per creare una macchina virtuale e renderlo disponibile nel sistema locale.
2. In vCenter fare clic sull'icona **Disco** e selezionare **vsanDatastore**. Fare clic su **File** e quindi su **Nuova cartella**.
    ![VCenter ISO](media/vciso00.png)

3. Creare una cartella denominata "ISO e modelli".

4. Passare alla cartella ISO in ISO e modelli e fare clic su **Carica file**. Seguire le istruzioni visualizzate per caricare l'ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Creare una macchina virtuale in vCenterCreate a Virtual Machine in vCenter

1. In vCenter, fare clic sull'icona **Host e cluster.**

2. Fare clic con il pulsante destro del mouse su **Carico di lavoro** e selezionare Nuova macchina **virtuale**.
    ![Nuova macchina virtuale](media/vcvm01.png)

3. Selezionare **Crea nuova macchina virtuale** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm02.png)

4. Assegnare un nome alla macchina, selezionare il percorso **della macchina virtuale del carico** di lavoro e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm03.png)

5. Selezionare la risorsa di calcolo **Carico di lavoro** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm04.png)

6. Selezionare **vsanDatastore** e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm05.png)

7. Mantenere la selezione di compatibilità predefinita di ESXi 6.5 e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm06.png)

8. Selezionare il sistema operativo guest dell'ISO per la macchina virtuale che si sta creando e fare clic su **Avanti**.
    ![Nuova macchina virtuale](media/vcvm07.png)

9. Selezionare le opzioni di rete e disco rigido. Per Nuova unità CD/DVD, selezionare **File ISO Datastore**.  Se si desidera consentire il traffico dall'indirizzo IP pubblico a questa macchina virtuale, selezionare la rete come **vm-1**.
    ![Nuova macchina virtuale](media/vcvm08.png)

10. Si apre una finestra di selezione. Selezionare il file caricato in precedenza nella cartella ISO e modelli e fare clic su **OK**.
    ![Nuova macchina virtuale](media/vcvm10.png)

11. Rivedere le impostazioni e fare clic **su OK** per creare la macchina virtuale.
    ![Nuova macchina virtuale](media/vcvm11.png)

La macchina virtuale viene ora aggiunta alle risorse di calcolo del carico di lavoro ed è pronta per l'uso. 
![Nuova macchina virtuale](media/vcvm12.png)

La configurazione di base è stata completata. È possibile iniziare a usare il cloud privato in modo simile a come si usa l'infrastruttura VM locale.

Le sezioni seguenti contengono informazioni facoltative sulla configurazione dei server DNS e DHCP per i carichi di lavoro del cloud privato e sulla modifica della configurazione di rete predefinita.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Aggiungere utenti e origini di identità a vCenter (facoltativo)Add Users and identity sources to vCenter (Optional)

CloudSimple assegna un account utente predefinito `cloudowner@cloudsimple.local`di vCenter con nomeutente . Per iniziare, non è necessaria alcuna configurazione aggiuntiva dell'account.  CloudSimple in genere assegna agli amministratori i privilegi necessari per eseguire le normali operazioni.  Configurare Active Directory locale o Azure AD come origine di [identità aggiuntiva](set-vcenter-identity.md) nel cloud privato.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Creare un server DNS e DHCP (facoltativo)

Le applicazioni e i carichi di lavoro in esecuzione in un ambiente Cloud privato richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e l'assegnazione degli indirizzi IP. Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS appropriata. È possibile configurare una macchina virtuale in vCenter per fornire questi servizi nell'ambiente Cloud privato.

Prerequisiti

* Un gruppo di porte distribuite con VLAN configurato

* Instradare l'installazione a server DNS locali o basati su Internet

* Modello di macchina virtuale o ISO per creare una macchina virtualeVirtual machine template or ISO to create a virtual machine

I collegamenti seguenti forniscono indicazioni sulla configurazione dei server DHCP e DNS in Linux e Windows.

#### <a name="linux-based-dns-server-setup"></a>Configurazione del server DNS basato su Linux

Linux offre vari pacchetti per la configurazione dei server DNS.  Di seguito è riportato un collegamento alle istruzioni per la configurazione di un server DNS BIND open source.

[Configurazione di esempio](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Installazione basata su Windows

In questi argomenti Microsoft viene descritto come configurare un server Windows come server DNS e come server DHCP.

[Windows Server come server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server come server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizzare la configurazione di rete (facoltativo)Customize networking configuration (Optional)

Le pagine Rete nel portale CloudSimple consentono di specificare la configurazione per le tabelle del firewall e gli indirizzi IP pubblici per le macchine virtuali.

### <a name="allocate-public-ips"></a>Allocare indirizzi IP pubblici

1. Passare a Rete > IP pubblico nel portale CloudSimple.Navigate to **Network > Public IP** in the CloudSimple portal.
2. Fare clic su **Alloca IP pubblico**.
3. Immettere un nome per identificare la voce dell'indirizzo IP.
4. Mantenere la posizione predefinita.
5. Utilizzare il dispositivo di scorrimento per modificare il timeout di inattività, se lo si desidera.
6. Immettere l'indirizzo IP locale a cui si desidera assegnare un indirizzo IP pubblico.
7. Se lo si desidera, immettere un nome DNS associato.
8. Fare clic su **Fine**.

    ![IP pubblico](media/quick-create-pc-public-ip.png)

Inizia l'attività di allocazione dell'indirizzo IP pubblico. È possibile controllare lo stato dell'attività nella pagina **Attività > Attività.** Al termine dell'allocazione, la nuova voce viene visualizzata nella pagina IP pubblici.

La macchina virtuale a cui deve essere mappato questo indirizzo IP deve essere configurata con l'indirizzo locale specificato in precedenza. La procedura per configurare un indirizzo IP è specifica del sistema operativo della macchina virtuale. Consultare la documentazione relativa al sistema operativo della macchina virtuale per la procedura corretta.

#### <a name="example"></a>Esempio

Ad esempio, ecco i dettagli per Ubuntu 16.04.

Aggiungere il metodo statico alla configurazione della famiglia di indirizzi inet nel file /etc/network/interfaces. Modificare i valori di indirizzo, netmask e gateway. Per questo esempio vengono usati l'interfaccia eth0, l'indirizzo IP interno 192.168.24.10, l'indirizzo del gateway 192.168.24.1 e la maschera di rete 255.255.255.0. Per l'ambiente, le informazioni sulla subnet disponibili vengono fornite nel messaggio di benvenuto.

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
Disattivare manualmente l'interfaccia.

```
sudo ifdown eth0
```
Abilitare nuovamente l'interfaccia manualmente.

```
sudo ifup eth0
```

Per impostazione predefinita, tutto il traffico in ingresso da Internet viene **negato.** Se si desidera aprire qualsiasi altra porta, creare una [tabella firewall](firewall.md).

Dopo aver configurato un indirizzo IP interno come indirizzo IP statico, verificare che sia possibile raggiungere Internet dall'interno della macchina virtuale.

```
ping 8.8.8.8
```
Verificare inoltre che sia possibile raggiungere la macchina virtuale da Internet usando l'indirizzo IP pubblico.

Verificare che tutte le regole IPtable nella macchina virtuale non blocchino la porta 80 in ingresso.
        
```
netstat -an | grep 80
```

Avviare un server http in ascolto sulla porta 80.Start an http server that listens on port 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

o

```
python3 -m http.server 80
```
Avviare un browser sul desktop e puntare alla porta 80 per l'indirizzo IP pubblico per esplorare i file nella macchina virtuale.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regole firewall CloudSimple predefinite per l'IP pubblico

* Traffico VPN: tutto il traffico tra (da/a) la VPN e tutte le reti e la rete di gestione del carico di lavoro è consentito.
* Traffico interno del cloud privato: tutto il traffico est-ovest tra le reti del carico di lavoro (da/a) e la rete di gestione (mostrata sopra) è consentito.
* Traffico Internet:
  * Tutto il traffico in ingresso da Internet viene negato alle reti del carico di lavoro e alla rete di gestione.
  * Tutto il traffico in uscita verso Internet dalle reti del carico di lavoro o dalla rete di gestione è consentito.

È inoltre possibile modificare la modalità di protezione del traffico, utilizzando la funzionalità Regole firewall. Per ulteriori informazioni, consultate [Configurare tabelle e regole del firewall.](firewall.md)

## <a name="install-solutions-optional"></a>Installare soluzioni (facoltativo)Install solutions (Optional)

È possibile installare soluzioni nel cloud privato CloudSimple per sfruttare appieno l'ambiente vCenter del cloud privato. È possibile configurare il backup, il ripristino di emergenza, la replica e altre funzioni per proteggere le macchine virtuali. Alcuni esempi sono VMware Site Recovery Manager (VMware SRM) e Veeam Backup & replication.

Per installare una soluzione, è necessario richiedere privilegi aggiuntivi per un periodo limitato. Vedere [Riassegnare i privilegi](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale usando Azure ExpressRouteConnect to on-premises network using Azure ExpressRoute](on-premises-connection.md)
* [Configurare i gateway VPN sulla rete CloudSimple](vpn-gateway.md)
