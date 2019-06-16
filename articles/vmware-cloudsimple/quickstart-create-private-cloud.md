---
title: Soluzione Azure di VMware da CloudSimple Quickstart - creare un cloud privato
description: Informazioni su come creare e configurare un cloud privato con Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e1fc7809ad94d589483b87c638d027a39098164e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209547"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Guida introduttiva - configurare un ambiente cloud privato

In questo articolo, informazioni su come creare un cloud privato CloudSimple e configurare l'ambiente cloud privato.

## <a name="sign-in-to-azure"></a>Accedi ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Creare un cloud privato

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple servizi**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il Cloud privato.
4. Dal dashboard panoramica, fare clic su **creare Cloud privati** per aprire una nuova scheda del browser per CloudSimple portale.  Se richiesto, accedere con Azure le credenziali di accesso.  

    ![Creare un Cloud privato di Azure](media/create-private-cloud-from-azure.png)

5. Nel portale CloudSimple, specificare un nome per il Cloud privato
6. Selezionare il **posizione** della Cloud privata
7. Selezionare il **tipo di nodo** è stata acquistata in Azure.  È possibile scegliere il [opzione CS28 o CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). L'opzione di quest'ultima include la capacità di calcolo e memoria massima.
8. Specificare il **conteggio dei nodi**.  Per creare un Cloud privato sono necessari nodi di almeno tre

    ![Creazione di Cloud privato - le informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Avanti: Opzioni avanzate**.
10. Immettere l'intervallo CIDR per la subnet di vSphere/rete vSAN. Assicurarsi che l'intervallo CIDR non si sovrapponga con uno qualsiasi di on-premises o ad altre subnet di Azure.

    ![Creare Cloud privati - opzioni avanzate](media/create-private-cloud-advanced-options.png)

11. Selezionare **Avanti: Esaminare e creare**.
12. Rivedere le impostazioni. Se è necessario modificare le impostazioni, fare clic su **Previous**.
13. Fare clic su **Create**(Crea).

Verrà avviato il Cloud privato processo di provisioning.  Potrebbero occorrere fino a due ore per il Cloud privato da sottoporre a provisioning.

## <a name="launch-cloudsimple-portal"></a>Avviare il portale CloudSimple

È possibile accedere portale CloudSimple dal portale di Azure.  Verrà avviata CloudSimple portale di Azure tramite il Single sign-on (SSO) le credenziali di accesso.  L'accesso al portale CloudSimple richiede all'utente di autorizzare il **autorizzazione del servizio CloudSimple** dell'applicazione.  Per altre informazioni sulla concessione di autorizzazioni, vedere [fornire il consenso all'applicazione l'autorizzazione del servizio CloudSimple](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple servizi**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il Cloud privato.
4. Dal dashboard panoramica, fare clic su **passare al portale CloudSimple** per aprire una nuova scheda del browser per CloudSimple portale.  Se richiesto, accedere con Azure le credenziali di accesso.  

    ![Avviare il portale CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Creare una VPN point-to-site

Una connessione VPN da punto a sito è il modo più semplice per connettersi al Cloud privato da un computer. Se ci si connette al Cloud privato in modalità remota, usare connessione VPN point-to-site.  Per accedere rapidamente al Cloud privato, attenersi alla procedura seguente.  Accesso all'area CloudSimple dalla rete locale può essere eseguita tramite [VPN Site-to-Site](https://docs.azure.cloudsimple.com/vpn-gateway/) oppure [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Creare il gateway

1. Avviare CloudSimple portale e selezionare **rete**.
2. Selezionare **Gateway VPN**.
3. Fare clic su **nuovo Gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per la **configurazione Gateway**, specificare le impostazioni seguenti e fare clic su **successivo**.

    * Selezionare **Point-to-Site VPN** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare la località di Azure in cui viene distribuito il servizio CloudSimple.
    * Specificare la subnet del client per il gateway da punto a sito.  Gli indirizzi DHCP verranno assegnati da questa subnet quando ci si connette.

5. Per la **/utente di connessione**, specificare le impostazioni seguenti e fare clic su **successivo**.

    * Per consentire automaticamente tutti gli utenti attuali e futuri di accedere a Cloud privati tramite il gateway da punto a sito, selezionare **aggiunge automaticamente tutti gli utenti**. Quando si seleziona questa opzione, vengono selezionati automaticamente tutti gli utenti nell'elenco degli utenti. È possibile ignorare l'opzione automatica si deselezionano singoli utenti nell'elenco.
    * Per selezionare solo singoli utenti, selezionare le caselle di controllo nell'elenco degli utenti.

6. La sezione VLAN/subnet consente di specificare management e l'utente VLAN/subnet per il gateway e connessioni.

    * Il **aggiunge automaticamente** opzioni impostare i criteri globali per il gateway. Le impostazioni applicabili per il gateway corrente. Le impostazioni possono essere sostituite nel **seleziona** area.
    * Selezionare **aggiungere la gestione delle VLAN/subnet dei cloud privati**. 
    * Per aggiungere subnet/VLAN definito dall'utente, fare clic su **aggiungere le subnet/VLAN definite dall'utente**. 
    * Il **selezionate** impostazioni sostituiscono le impostazioni globali sotto **aggiungono automaticamente**. 

7. Fare clic su **successivo** per esaminare le impostazioni. Scegliere le icone di modifica per apportare modifiche.
8. Fare clic su **Create** per creare il gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Connettersi a CloudSimple tramite VPN da punto a sito

Client VPN è necessaria per la connessione a CloudSimple dal computer.  Scaricare [client OpenVPN](https://openvpn.net/community-downloads/) per Windows o [viscosità](https://www.sparklabs.com/viscosity/download/) per macOS e OS X.

1. Avviare CloudSimple portale e selezionare **rete**.
2. Selezionare **Gateway VPN**.
3. Dall'elenco di gateway VPN, fare clic sul gateway VPN da punto a sito.
4. Selezionare **Utenti**.
5. Fare clic su **scaricare la configurazione VPN**

    ![Scaricare la configurazione della VPN](media/download-p2s-vpn-configuration.png)

6. Importare la configurazione del client VPN

    * Le istruzioni per [importazione della configurazione nel client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Le istruzioni per [importazione della configurazione in macOS o OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connettersi a CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Creare una rete VLAN per il carico di lavoro macchine virtuali

Dopo aver creato un cloud privato, creare una VLAN in cui si distribuiranno le macchine virtuali del carico di lavoro/applicazione.

1. Nel portale di CloudSimple, selezionare **rete**.
2. Fare clic su **VLAN/subnet**.
3. Fare clic su **creare Subnet/VLAN**

    ![Creare una Subnet/VLAN](media/create-new-vlan-subnet.png)

4. Selezionare il **Cloud privato** per la VLAN/subnet nuove.
5. Selezionare un ID VLAN dall'elenco.  
6. Immettere un nome di subnet per identificare la subnet.
7. Specificare l'intervallo CIDR della subnet e la maschera.  Questo intervallo non deve sovrapporsi con qualsiasi subnet esistente.
8. Fare clic su **Submit**.

    ![Creare i dettagli della Subnet/VLAN](media/create-new-vlan-subnet-details.png)

Verrà creata la subnet/VLAN.  È ora possibile usare questo ID VLAN per creare un gruppo di porte distribuite in vCenter del Cloud privato. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Connettere l'ambiente per una rete virtuale di Azure

CloudSimple offre un circuito ExpressRoute per cloud privato. È possibile connettere la rete virtuale in Azure al circuito ExpressRoute. Per informazioni dettagliate sull'impostazione della connessione, seguire i passaggi in [connessione rete virtuale di Azure tramite ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>Accesso a vCenter

È possibile ora accedere a vCenter per configurare le macchine virtuali e i criteri.

1. Per accedere a vCenter, iniziare dal portale CloudSimple. Nella Home page, sotto **attività comuni**, fare clic su **avviare vSphere Client**.  Selezionare il Cloud privato e quindi fare clic su **avvio veloce di vSphere Client** sul Cloud privato.

    ![Avviare Client vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selezionare il client di vSphere preferito per accedere a vCenter e accedere con il nome utente e password.  I valori predefiniti sono:
    * Nome utente: **CloudOwner@cloudsimple.local**
    * Password: **CloudSimple123!**  

Le schermate di vCenter nelle procedure successive sono compresi tra il client di vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Modificare la password di vCenter

CloudSimple consiglia di modificare la password al primo che accesso a vCenter.  
La password impostata deve soddisfare i requisiti seguenti:

* Durata massima: La password deve essere cambiata ogni 365 giorni
* Limitare il riutilizzo: Gli utenti non possono usare uno qualsiasi dei cinque password precedente
* Durata: 8 - 20 caratteri
* Caratteri speciali: Almeno un carattere speciale
* Caratteri alfabetici: Almeno un carattere maiuscolo, A-Z e almeno un carattere minuscolo, a-z
* Numeri: Almeno un carattere numerico, da 0 a 9
* Numero massimo di caratteri adiacente identico: Tre

    Esempio: CC o CCC è accettabile come parte della password, ma non è cccc è la.

Se è impostata una password che non soddisfa i requisiti seguenti:

* Se si usa il Client Flash vSphere, viene segnalato un errore
* Se si usa il client HTML5, non segnala un errore. Il client non accetta la modifica e la password precedente continuerà a funzionare.

## <a name="change-nsx-administrator-password"></a>Modificare la password amministratore NSX

NSX manager viene distribuito con una password predefinita.  Si consiglia di che modificare la password dopo la creazione del Cloud privato.

   * Nome utente: **admin**
   * Password: **CloudSimple123!**

È possibile trovare il nome di dominio completo (FQDN) e l'indirizzo IP del manager NSX sul portale CloudSimple.

1. Avviare CloudSimple portale e selezionare **risorse**.
2. Fare clic sul Cloud privato, a cui si desidera utilizzare.
3. Selezionare **rete di gestione di vSphere**
4. Usare il nome di dominio completo o indirizzo IP del **NSX Manager** e connettersi usando un web browser. 

    ![Trovare NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

Per modificare la password, seguire le istruzioni in [installazione di gestione NSX](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Creare un gruppo di porte

Per creare un gruppo di porte distribuita di vSphere:

1. Seguire le istruzioni in "Aggiungere un gruppo di porte distribuite" di [vSphere Guida alla rete](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Quando si configura il gruppo di porte distribuita, specificare l'ID VLAN creato nella [creare una rete VLAN per le VM del carico di lavoro](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare le macchine virtuali VMware in Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale tramite Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurare la rete VPN Site-to-Site in locale](https://docs.azure.cloudsimple.com/vpn-gateway/)
