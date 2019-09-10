---
title: Soluzione VMware di Azure di CloudSimple-configurare un gateway VPN
description: Viene descritto come configurare un gateway VPN da punto a sito e un gateway VPN da sito a sito e creare connessioni tra la rete locale e il cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845354"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Configurare i gateway VPN nella rete CloudSimple

I gateway VPN consentono di connettersi alla rete CloudSimple dalla rete locale e da un computer client in remoto. Una connessione VPN tra la rete locale e la rete CloudSimple fornisce l'accesso a vCenter e ai carichi di lavoro nel cloud privato. CloudSimple supporta VPN da sito a sito e gateway VPN da punto a sito.

## <a name="vpn-gateway-types"></a>Tipi di gateway VPN

* La connessione **VPN da sito a sito** consente di configurare i carichi di lavoro del cloud privato per accedere ai servizi locali. È anche possibile usare Active Directory locali come origine di identità per l'autenticazione nel cloud privato vCenter.  Attualmente, è supportato solo il tipo **VPN basato su criteri** .
* La connessione **VPN da punto a sito** è il modo più semplice per connettersi al cloud privato dal computer. Usare la connettività VPN da punto a sito per connettersi in remoto al cloud privato. Per informazioni sull'installazione di un client per una connessione VPN da punto a sito, vedere [configurare una connessione VPN al cloud privato](set-up-vpn.md).

In un'area è possibile creare un gateway VPN da punto a sito e un gateway VPN da sito a sito.

## <a name="automatic-addition-of-vlansubnets"></a>Aggiunta automatica di VLAN/subnet

I gateway VPN CloudSimple forniscono criteri per l'aggiunta di VLAN/subnet ai gateway VPN.  I criteri consentono di specificare regole diverse per la VLAN di gestione/le subnet e le VLAN/subnet definite dall'utente.  Le regole per le subnet/VLAN di gestione si applicano a tutti i nuovi cloud privati creati.  Le regole per le VLAN e le subnet definite dall'utente consentono di aggiungere automaticamente nuove VLAN/subnet a cloud privati nuovi o esistenti per un gateway VPN da sito a sito, definendo i criteri per ogni connessione.

I criteri per l'aggiunta di VLAN/subnet ai gateway VPN si applicano sia alla VPN da sito a sito sia ai gateway VPN da punto a sito.

## <a name="automatic-addition-of-users"></a>Aggiunta automatica di utenti

Un gateway VPN da punto a sito consente di definire un criterio di aggiunta automatica per i nuovi utenti. Per impostazione predefinita, tutti i proprietari e i collaboratori della sottoscrizione possono accedere al portale di CloudSimple.  Gli utenti vengono creati solo quando il portale di CloudSimple viene avviato per la prima volta.  Selezionando **Aggiungi regole automaticamente** si consente a qualsiasi nuovo utente di accedere alla rete CloudSimple usando la connessione VPN da punto a sito.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Configurare un gateway VPN da sito a sito

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md) e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Fare clic su **nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per la **configurazione del gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da sito a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare la località di Azure in cui viene distribuito il servizio CloudSimple.
    * Facoltativamente, abilitare la disponibilità elevata.

    ![Creare un gateway VPN da sito a sito](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Per abilitare la disponibilità elevata, è necessario che il dispositivo VPN locale supporti la connessione a due indirizzi IP. Questa opzione non può essere disabilitata dopo la distribuzione del gateway VPN.

5. Creare la prima connessione dalla rete locale e fare clic su **Avanti**.

    * Immettere un nome per identificare la connessione.
    * Per l'IP peer, immettere l'indirizzo IP pubblico del gateway VPN locale.
    * Immettere l'identificatore peer del gateway VPN locale.  L'identificatore peer è in genere l'indirizzo IP pubblico del gateway VPN locale.  Se è stato configurato un identificatore specifico nel gateway, immettere l'identificatore.
    * Copiare la chiave condivisa da usare per la connessione dal gateway VPN locale.  Per modificare la chiave condivisa predefinita e specificarne una nuova, fare clic sull'icona modifica.
    * Per i **prefissi locali**, immettere i prefissi CIDR locali che accedono alla rete CloudSimple.  Quando si crea la connessione, è possibile aggiungere più prefissi CIDR.

    ![Creare una connessione gateway VPN da sito a sito](media/create-vpn-gateway-s2s-connection.png)

6. Abilitare la VLAN/subnet nella rete cloud privata a cui sarà possibile accedere dalla rete locale e fare clic su **Avanti**.

    * Per aggiungere una subnet/VLAN di gestione, abilitare **Aggiungi VLAN di gestione/subnet di cloud privati**.  La subnet di gestione è obbligatoria per le subnet vMotion e rete VSAN.
    * Per aggiungere le subnet vMotion, abilitare **Aggiungi rete di cloud privati**.
    * Per aggiungere subnet rete VSAN, abilitare **Aggiungi subnet rete VSAN di cloud privati**.
    * Selezionare o deselezionare VLAN specifiche.

    ![Creare la connessione](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Verificare le impostazioni e fare clic su **Invia**.

    ![Revisione e creazione del gateway VPN da sito a sito](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Creare un gateway VPN da punto a sito

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md) e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Fare clic su **nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per la **configurazione del gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da punto a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare la località di Azure in cui viene distribuito il servizio CloudSimple.
    * Specificare la subnet client per il gateway da punto a sito.  Gli indirizzi DHCP verranno assegnati dalla subnet client quando ci si connette.

5. Per **connessione/utente**specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Per consentire automaticamente a tutti gli utenti correnti e futuri di accedere al cloud privato tramite il gateway da punto a sito, selezionare **Aggiungi automaticamente tutti gli utenti**. Quando si seleziona l'opzione, tutti gli utenti nell'elenco utenti vengono selezionati automaticamente. È possibile sostituire l'opzione automatica deselezionando i singoli utenti nell'elenco.
    * Per selezionare singoli utenti, fare clic sulle caselle di controllo nell'elenco degli utenti.

6. La sezione VLAN/subnet consente di specificare la gestione e le VLAN utente/subnet per il gateway e le connessioni.

    * Le opzioni **Aggiungi automaticamente** impostano i criteri globali per il gateway. Le impostazioni si applicano al gateway corrente. È possibile eseguire l'override delle impostazioni nell'area **Select** .
    * Selezionare **Aggiungi gestione VLAN/subnet di cloud privati**. 
    * Per aggiungere tutte le VLAN e le subnet definite dall'utente, fare clic su **Aggiungi VLAN/subnet definite dall'utente**.
    * Le impostazioni di **selezione** eseguono l'override delle impostazioni globali in **Aggiungi automaticamente**.

7. Fare clic su **Avanti** per esaminare le impostazioni. Fare clic su modifica icone per apportare le modifiche.
8. Fare clic su **Crea** per creare il gateway VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Subnet e protocolli client per gateway VPN da punto a sito

Il gateway VPN da punto a sito consente le connessioni TCP e UDP.  Scegliere il protocollo da usare quando ci si connette dal computer selezionando la configurazione TCP o UDP.

La subnet client configurata viene utilizzata per i client TCP e UDP.  Il prefisso CIDR è suddiviso in due subnet, una per TCP e una per i client UDP. Scegliere la maschera di prefisso in base al numero di utenti VPN che si connetteranno simultaneamente.  

La tabella seguente elenca il numero di connessioni client simultanee per la maschera di prefisso.

| Maschera di prefisso | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Numero di connessioni TCP simultanee | 124 | 60 | 28 | 12 | 4 |
| Numero di connessioni UDP simultanee | 124 | 60 | 28 | 12 | 4 |

Per connettersi usando la VPN da punto a sito, vedere [connettersi a CloudSimple con la VPN da punto a sito](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
