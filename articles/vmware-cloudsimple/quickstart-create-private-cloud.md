---
title: 'Guida introduttiva alla soluzione VMware di Azure di CloudSimple: creare un cloud privato'
description: Informazioni su come creare e configurare un cloud privato con la soluzione VMware di Azure di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fdf1fc14eb4ab1458c25b484bae6cd84ecec6d7f
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575500"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Guida introduttiva-configurare un ambiente cloud privato

Questo articolo illustra come creare un cloud privato CloudSimple e come configurare l'ambiente cloud privato.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Creare un cloud privato

Un cloud privato è uno stack VMware isolato che supporta host ESXi, vCenter, rete VSAN e NSX.

I cloud privati vengono gestiti tramite il portale CloudSimple. Hanno il proprio server vCenter nel proprio dominio di gestione. Lo stack viene eseguito su nodi dedicati e nodi hardware bare metal isolati.

1. Selezionare **Tutti i servizi**.
2. Cercare i **Servizi CloudSimple**.
3. Selezionare il servizio CloudSimple in cui si vuole creare il cloud privato.
4. Da **Panoramica**fare clic su **Crea cloud privato** per aprire una nuova scheda del browser per il portale di CloudSimple.  Se richiesto, accedere con le credenziali di accesso di Azure.  

    ![Creare un cloud privato da Azure](media/create-private-cloud-from-azure.png)

5. Nel portale di CloudSimple specificare un nome per il cloud privato.
6. Selezionare la **località** del cloud privato.
7. Selezionare il **tipo di nodo**, coerente con quello acquistato in Azure. È possibile scegliere l' [opzione CS28 o CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). La seconda opzione include la capacità di calcolo e di memoria massima.
8. Specificare il **numero di nodi**.  Per creare un cloud privato sono necessari almeno tre nodi.

    ![Creare un cloud privato-informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Avanti: Opzioni**avanzate.
10. Immettere l'intervallo CIDR per le subnet vSphere/rete VSAN. Verificare che l'intervallo CIDR non si sovrappongano con le subnet di Azure locali o altre (reti virtuali) o con la subnet del gateway.

    **Opzioni di intervallo CIDR:** /24,/23,/22 o/21. Un intervallo CIDR/24 supporta fino a nove nodi, un intervallo CIDR/23 supporta fino a 41 nodi e un intervallo CIDR/22 e/21 supporta fino a 64 nodi (il numero massimo di nodi in un cloud privato).

      > [!IMPORTANT]
      > Gli indirizzi IP nell'intervallo CIDR vSphere/rete VSAN sono riservati per l'uso da parte dell'infrastruttura di cloud privato.  Non usare l'indirizzo IP in questo intervallo in una macchina virtuale.

11. Fare clic su **Avanti: Esaminare e creare**.
12. Esaminare le impostazioni. Se è necessario modificare le impostazioni, fare clic su **indietro**.
13. Fare clic su **Create**(Crea).

Viene avviato il processo di provisioning del cloud privato.  Il provisioning del cloud privato può richiedere fino a due ore.

## <a name="launch-cloudsimple-portal"></a>Avviare il portale di CloudSimple

È possibile accedere al portale di CloudSimple da portale di Azure.  Il portale di CloudSimple verrà avviato con le credenziali di accesso di Azure tramite Single Sign-on (SSO).  Per accedere al portale di CloudSimple è necessario autorizzare l'applicazione di **autorizzazione del servizio CloudSimple** .  Per ulteriori informazioni sulla concessione di autorizzazioni, vedere il [consenso all'applicazione di autorizzazione del servizio CloudSimple](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Selezionare **Tutti i servizi**.
2. Cercare i **Servizi CloudSimple**.
3. Selezionare il servizio CloudSimple in cui si vuole creare il cloud privato.
4. Da Panoramica fare clic su **Vai al portale di CloudSimple** per aprire una nuova scheda del browser per il portale di CloudSimple.  Se richiesto, accedere con le credenziali di accesso di Azure.  

    ![Avviare il portale di CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Creare una VPN da punto a sito

Una connessione VPN da punto a sito è il modo più semplice per connettersi al cloud privato dal computer. Usare la connessione VPN da punto a sito se ci si connette al cloud privato in remoto.  Per accedere rapidamente al cloud privato, attenersi alla procedura riportata di seguito.  L'accesso all'area CloudSimple dalla rete locale può essere eseguito usando la VPN da [sito a sito](vpn-gateway.md) o [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Creare il gateway

1. Avviare il portale di CloudSimple e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Fare clic su **nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per la **configurazione del gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da punto a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare la località di Azure in cui viene distribuito il servizio CloudSimple.
    * Specificare la subnet client per il gateway da punto a sito.  Gli indirizzi DHCP verranno assegnati da questa subnet quando ci si connette.

5. Per **connessione/utente**specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Per consentire automaticamente a tutti gli utenti correnti e futuri di accedere al cloud privato tramite questo gateway da punto a sito, selezionare **Aggiungi automaticamente tutti gli utenti**. Quando si seleziona questa opzione, tutti gli utenti nell'elenco utenti vengono selezionati automaticamente. È possibile sostituire l'opzione automatica deselezionando i singoli utenti nell'elenco.
    * Per selezionare solo singoli utenti, fare clic sulle caselle di controllo nell'elenco degli utenti.

6. La sezione VLAN/subnet consente di specificare la gestione e le VLAN utente/subnet per il gateway e le connessioni.

    * Le opzioni **Aggiungi automaticamente** impostano i criteri globali per questo gateway. Le impostazioni si applicano al gateway corrente. È possibile eseguire l'override delle impostazioni nell'area **Select** .
    * Selezionare **Aggiungi gestione VLAN/subnet di cloud privati**.
    * Per aggiungere tutte le VLAN e le subnet definite dall'utente, fare clic su **Aggiungi VLAN/subnet definite dall'utente**.
    * Le impostazioni di **selezione** eseguono l'override delle impostazioni globali in **Aggiungi automaticamente**.

7. Fare clic su **Avanti** per esaminare le impostazioni. Fare clic su modifica icone per apportare le modifiche.
8. Fare clic su **Crea** per creare il gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Connettersi a CloudSimple tramite una VPN da punto a sito

Il client VPN è necessario per la connessione a CloudSimple dal computer.  Scaricare il [client OpenVPN](https://openvpn.net/community-downloads/) per Windows o [la viscosità](https://www.sparklabs.com/viscosity/download/) per MacOS e OS X.

1. Avviare il portale di CloudSimple e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Dall'elenco dei gateway VPN fare clic sul gateway VPN da punto a sito.
4. Selezionare **Utenti**.
5. Fare clic su **Scarica configurazione VPN**.

    ![Scarica configurazione VPN](media/download-p2s-vpn-configuration.png)

6. Importare la configurazione nel client VPN.

    * Istruzioni per l' [importazione della configurazione nel client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Istruzioni per l' [importazione della configurazione in MacOS o OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connettersi a CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Creare una VLAN per le macchine virtuali del carico di lavoro

Dopo aver creato un cloud privato, creare una VLAN in cui distribuire il carico di lavoro/le macchine virtuali dell'applicazione.

1. Nel portale di CloudSimple selezionare **rete**.
2. Fare clic su **VLAN/subnet**.
3. Fare clic su **Crea VLAN/subnet**.

    ![Crea VLAN/subnet](media/create-new-vlan-subnet.png)

4. Selezionare il **cloud privato** per la nuova VLAN/subnet.
5. Selezionare un ID VLAN nell'elenco.  
6. Immettere il nome di una subnet per identificare la subnet.
7. Specificare l'intervallo CIDR della subnet e la maschera.  Questo intervallo non deve sovrapporsi ad alcuna subnet esistente.
8. Fare clic su **Invia**.

    ![Crea dettagli VLAN/subnet](media/create-new-vlan-subnet-details.png)

La VLAN/subnet verrà creata.  È ora possibile usare questo ID VLAN per creare un gruppo di porte distribuite nel cloud privato vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Connettere l'ambiente a una rete virtuale di Azure

CloudSimple fornisce un circuito ExpressRoute per il cloud privato. È possibile connettere la rete virtuale in Azure al circuito ExpressRoute. Per informazioni dettagliate sull'impostazione della connessione, seguire i passaggi in [connessione di rete virtuale di Azure con ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Accedere a vCenter

È ora possibile accedere a vCenter per configurare le macchine virtuali e i criteri.

1. Per accedere a vCenter, iniziare dal portale di CloudSimple. Nella Home page, in **attività comuni**, fare clic su **avvia client vSphere**.  Selezionare il cloud privato e quindi fare clic su **avvia client vSphere** nel cloud privato.

    ![Avviare vSphere client](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selezionare il client vSphere preferito per accedere a vCenter e accedere con il nome utente e la password.  Le impostazioni predefinite sono:
    * Nome utente: **CloudOwner@cloudsimple.local**
    * Password: **CloudSimple123!**  

Le schermate di vCenter nelle procedure successive sono riportate nel client vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Modificare la password di vCenter

CloudSimple consiglia di modificare la password la prima volta che si accede a vCenter.  
La password impostata deve soddisfare i requisiti seguenti:

* Durata massima: La password deve essere modificata ogni 365 giorni
* Limita riutilizzo: Gli utenti non possono riutilizzare una delle cinque password precedenti
* Durata: 8-20 caratteri
* Carattere speciale: Almeno un carattere speciale
* Caratteri alfabetici: Almeno un carattere maiuscolo, A-Z e almeno un carattere minuscolo, a-z
* Numeri Almeno un carattere numerico, 0-9
* Numero massimo di caratteri adiacenti identici: Tre

    Esempio: CC o CCC è accettabile come parte della password, ma ciari non è.

Se si imposta una password che non soddisfa i requisiti:

* Se si usa il client vSphere Flash, viene segnalato un errore
* Se si usa il client HTML5, non viene segnalato alcun errore. Il client non accetta la modifica e la vecchia password continua a funzionare.

## <a name="change-nsx-administrator-password"></a>Modificare la password dell'amministratore di NSX

Gestione NSX viene distribuito con una password predefinita.  Si consiglia di modificare la password dopo avere creato il cloud privato.

* Nome utente: **admin**
* Password: **CloudSimple123!**

È possibile trovare il nome di dominio completo (FQDN) e l'indirizzo IP di NSX Manager nel portale di CloudSimple.

1. Avviare il portale di CloudSimple e selezionare **risorse**.
2. Fare clic sul cloud privato che si vuole usare.
3. Selezionare **vSphere Management Network**
4. Usare il nome FQDN o l'indirizzo IP di **gestione NSX** e connettersi usando un Web browser.

    ![Trovare il nome di dominio completo di gestione NSX](media/private-cloud-nsx-manager-fqdn.png)

Per modificare la password, seguire le istruzioni riportate nell' [installazione di gestione NSX](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Creazione di un gruppo di porte

Per creare un gruppo di porte distribuite in vSphere:

1. Seguire le istruzioni riportate in "aggiungere un gruppo di porte distribuite" in [vSphere Networking Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Quando si configura il gruppo di porte distribuite, fornire l'ID VLAN creato in [creare una VLAN per le macchine virtuali del carico di lavoro](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale usando Azure ExpressRoute](on-premises-connection.md)
* [Configurare una VPN da sito a sito da locale](vpn-gateway.md)
