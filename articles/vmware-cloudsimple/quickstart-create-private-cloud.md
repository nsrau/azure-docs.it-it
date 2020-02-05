---
title: Guida introduttiva alle soluzioni VMware di Azure (AVS)-creare un cloud privato AVS
description: Informazioni su come creare e configurare un cloud privato AVS con soluzioni VMware di Azure (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018568"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>Guida introduttiva-configurare un ambiente cloud privato AVS

Questo articolo illustra come creare un cloud privato AVS e come configurare l'ambiente di cloud privato AVS.

## <a name="before-you-begin"></a>Prima di iniziare

Verificare i [prerequisiti di rete](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-avs-private-cloud"></a>Creare un cloud privato AVS

Un cloud privato AVS è uno stack VMware isolato che supporta host ESXi, vCenter, rete VSAN e NSX.

I cloud privati AVS vengono gestiti tramite il portale AVS. Hanno il proprio server vCenter nel proprio dominio di gestione. Lo stack viene eseguito su nodi dedicati e nodi hardware bare metal isolati.

1. Selezionare **Tutti i servizi**.
2. Cercare **AVS Services**.
3. Selezionare il servizio AVS in cui si vuole creare il cloud privato AVS.
4. Da **Panoramica**fare clic su **Crea AVS private cloud** per aprire una nuova scheda del browser per AVS Portal. Se richiesto, accedere con le credenziali di accesso di Azure. 

    ![Creare un cloud privato AVS da Azure](media/create-private-cloud-from-azure.png)

5. Nel portale AVS specificare un nome per il cloud privato AVS.
6. Selezionare il **percorso** del cloud privato AVS.
7. Selezionare il **tipo di nodo**, coerente con quello di cui è stato effettuato il provisioning in Azure.
8. Specificare il **numero di nodi**. Per creare un cloud privato AVS sono necessari almeno tre nodi.

    ![Creazione di un cloud privato AVS-informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Avanti: opzioni avanzate**.
10. Immettere l'intervallo CIDR per le subnet vSphere/rete VSAN. Verificare che l'intervallo CIDR non si sovrappongano con le subnet di Azure locali o altre (reti virtuali) o con la subnet del gateway.

    **Opzioni di intervallo CIDR:** /24,/23,/22 o/21. Un intervallo CIDR/24 supporta fino a 26 nodi, un intervallo CIDR/23 supporta fino a 58 nodi e un intervallo CIDR/22 e/21 supporta 64 nodi (il numero massimo di nodi in un cloud privato AVS). Per altre informazioni e VLAN e subnet, vedere [Panoramica di VLAN e subnet](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Gli indirizzi IP nell'intervallo CIDR vSphere/rete VSAN sono riservati per l'uso da parte dell'infrastruttura di cloud privato AVS. Non usare l'indirizzo IP in questo intervallo in una macchina virtuale.

11. Fare clic su **Avanti: esaminare e creare**.
12. Esaminare le impostazioni. Se è necessario modificare le impostazioni, fare clic su **indietro**.
13. Fare clic su **Crea**.

Viene avviato il processo di provisioning del cloud privato AVS. Il provisioning del cloud privato AVS può richiedere fino a due ore.

## <a name="launch-avs-portal"></a>Avviare il portale AVS

È possibile accedere al portale AVS da portale di Azure. Il portale AVS verrà avviato con le credenziali di accesso di Azure tramite Single Sign-on (SSO). Per accedere al portale AVS è necessario autorizzare l'applicazione di **autorizzazione del servizio AVS** . Per altre informazioni sulla concessione di autorizzazioni, vedere il [consenso all'applicazione di autorizzazione del servizio AVS](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application).

1. Selezionare **Tutti i servizi**.
2. Cercare **AVS Services**.
3. Selezionare il servizio AVS in cui si vuole creare il cloud privato AVS.
4. Da Panoramica fare clic su **Vai al portale AVS** per aprire una nuova scheda del browser per AVS Portal. Se richiesto, accedere con le credenziali di accesso di Azure. 

    ![Avviare il portale AVS](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Creare una VPN da punto a sito

Una connessione VPN da punto a sito è il modo più semplice per connettersi al cloud privato AVS dal computer. Usare la connessione VPN da punto a sito se ci si connette al cloud privato AVS in remoto. Per accedere rapidamente al cloud privato AVS, seguire questa procedura. L'accesso all'area AVS dalla rete locale può essere eseguito usando la VPN da [sito a sito](vpn-gateway.md) o [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Creare il gateway

1. Avviare AVS Portal e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Fare clic su **nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per la **configurazione del gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da punto a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare la località di Azure in cui viene distribuito il servizio AVS.
    * Specificare la subnet client per il gateway da punto a sito. Gli indirizzi DHCP verranno assegnati da questa subnet quando ci si connette.

5. Per **connessione/utente**specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Per consentire automaticamente a tutti gli utenti correnti e futuri di accedere al cloud privato AVS tramite questo gateway da punto a sito, selezionare **Aggiungi automaticamente tutti gli utenti**. Quando si seleziona questa opzione, tutti gli utenti nell'elenco utenti vengono selezionati automaticamente. È possibile sostituire l'opzione automatica deselezionando i singoli utenti nell'elenco.
    * Per selezionare solo singoli utenti, fare clic sulle caselle di controllo nell'elenco degli utenti.

6. La sezione VLAN/subnet consente di specificare la gestione e le VLAN utente/subnet per il gateway e le connessioni.

    * Le opzioni **Aggiungi automaticamente** impostano i criteri globali per questo gateway. Le impostazioni si applicano al gateway corrente. È possibile eseguire l'override delle impostazioni nell'area **Select** .
    * Selezionare **Aggiungi gestione VLAN/subnet dei cloud privati AVS**.
    * Per aggiungere tutte le VLAN e le subnet definite dall'utente, fare clic su **Aggiungi VLAN/subnet definite dall'utente**.
    * Le impostazioni di **selezione** eseguono l'override delle impostazioni globali in **Aggiungi automaticamente**.

7. Fare clic su **Avanti** per esaminare le impostazioni. Fare clic su modifica icone per apportare le modifiche.
8. Fare clic su **Crea** per creare il gateway VPN.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Connettersi a AVS usando la VPN da punto a sito

Il client VPN è necessario per la connessione ad AVS dal computer. Scaricare il [client OpenVPN](https://openvpn.net/community-downloads/) per Windows o [la viscosità](https://www.sparklabs.com/viscosity/download/) per MacOS e OS X.

1. Avviare AVS Portal e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Dall'elenco dei gateway VPN fare clic sul gateway VPN da punto a sito.
4. Selezionare **Utenti**.
5. Fare clic su **Scarica configurazione VPN**.

    ![Scaricare la configurazione della VPN](media/download-p2s-vpn-configuration.png)

6. Importare la configurazione nel client VPN.

    * Istruzioni per l' [importazione della configurazione nel client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Istruzioni per l' [importazione della configurazione in MacOS o OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connettersi a AVS.

## <a name="create-a-vlan-for-your-workload-vms"></a>Creare una VLAN per le macchine virtuali del carico di lavoro

Dopo aver creato un cloud privato AVS, creare una VLAN in cui verranno distribuiti i carichi di lavoro e le macchine virtuali dell'applicazione.

1. Nel portale AVS selezionare **rete**.
2. Fare clic su **VLAN/subnet**.
3. Fare clic su **Crea VLAN/subnet**.

    ![Crea VLAN/subnet](media/create-new-vlan-subnet.png)

4. Selezionare il **cloud privato AVS** per la nuova VLAN/subnet.
5. Selezionare un ID VLAN nell'elenco. 
6. Immettere il nome di una subnet per identificare la subnet.
7. Specificare l'intervallo CIDR della subnet e la maschera. Questo intervallo non deve sovrapporsi ad alcuna subnet esistente.
8. Fare clic su **Submit** (Invia).

    ![Crea dettagli VLAN/subnet](media/create-new-vlan-subnet-details.png)

La VLAN/subnet verrà creata. È ora possibile usare questo ID VLAN per creare un gruppo di porte distribuite in AVS private cloud vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Connettere l'ambiente a una rete virtuale di Azure

AVS fornisce un circuito ExpressRoute per il cloud privato AVS. È possibile connettere la rete virtuale in Azure al circuito ExpressRoute. Per informazioni dettagliate sull'impostazione della connessione, seguire i passaggi in [connessione di rete virtuale di Azure con ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Accedere a vCenter

È ora possibile accedere a vCenter per configurare le macchine virtuali e i criteri.

1. Per accedere a vCenter, iniziare dal portale AVS. Nella Home page, in **attività comuni**, fare clic su **avvia client vSphere**. Selezionare AVS private cloud e quindi fare clic su **Launch vSphere client** on the AVS private cloud.

    ![Avviare vSphere client](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selezionare il client vSphere preferito per accedere a vCenter e accedere con il nome utente e la password. Le impostazioni predefinite sono:
    * Nome utente: **CloudOwner@AVS.local**
    * Password: **AVS123!**  

Le schermate di vCenter nelle procedure successive sono riportate nel client vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Modificare la password di vCenter

AVS consiglia di modificare la password la prima volta che si accede a vCenter. La password impostata deve soddisfare i requisiti seguenti:

* Durata massima: la password deve essere modificata ogni 365 giorni
* Limita riutilizzo: gli utenti non possono riutilizzare una delle cinque password precedenti
* Lunghezza: 8-20 caratteri
* Carattere speciale: almeno un carattere speciale
* Caratteri alfabetici: almeno un carattere maiuscolo, A-Z e almeno un carattere minuscolo, a-z
* Numeri: almeno un carattere numerico, 0-9
* Numero massimo di caratteri adiacenti identici: tre

    Esempio: CC o CCC è accettabile come parte della password, ma ciari non è.

Se si imposta una password che non soddisfa i requisiti:

* Se si usa il client vSphere Flash, viene segnalato un errore
* Se si usa il client HTML5, non viene segnalato alcun errore. Il client non accetta la modifica e la vecchia password continua a funzionare.

## <a name="access-nsx-manager"></a>Accedi a gestione NSX

Gestione NSX viene distribuito con una password predefinita. 

* Nome utente: **admin**
* Password: **AVS123!**

È possibile trovare il nome di dominio completo (FQDN) e l'indirizzo IP di gestione NSX in AVS Portal.

1. Avviare AVS Portal e selezionare **risorse**.
2. Fare clic sul cloud privato AVS che si vuole usare.
3. Selezionare **vSphere Management Network**
4. Usare il nome FQDN o l'indirizzo IP di **gestione NSX** e connettersi usando un Web browser.

    ![Trovare il nome di dominio completo di gestione NSX](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Creazione di un gruppo di porte

Per creare un gruppo di porte distribuite in vSphere:

1. Seguire le istruzioni riportate in "aggiungere un gruppo di porte distribuite" in [vSphere Networking Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Quando si configura il gruppo di porte distribuite, fornire l'ID VLAN creato in [creare una VLAN per le macchine virtuali del carico di lavoro](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale usando Azure ExpressRoute](on-premises-connection.md)
* [Configurare una VPN da sito a sito da locale](vpn-gateway.md)
