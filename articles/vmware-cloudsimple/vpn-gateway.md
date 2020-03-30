---
title: Soluzione Azure VMware di CloudSimple - Configurare un gateway VPN
description: Descrive come configurare il gateway VPN da punto a sito e il gateway VPN da sito a sito e a creare connessioni tra la rete locale e il cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279494"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Configurare i gateway VPN sulla rete CloudSimple

I gateway VPN consentono di connettersi alla rete CloudSimple dalla rete locale e da un computer client in remoto. Una connessione VPN tra la rete locale e la rete CloudSimple fornisce l'accesso al vCenter e ai carichi di lavoro nel cloud privato. CloudSimple supporta sia la VPN da sito a sito che i gateway VPN da punto a sito.

## <a name="vpn-gateway-types"></a>Tipi di gateway VPN

* **La** connessione VPN da sito a sito consente di configurare i carichi di lavoro del cloud privato per accedere ai servizi locali. È inoltre possibile utilizzare Active Directory locale come origine di identità per l'autenticazione nel server virtuale del cloud privato.  Attualmente è supportato solo il tipo di **VPN basata su criteri.**
* **La** connessione VPN da punto a sito è il modo più semplice per connettersi al cloud privato dal computer. Utilizzare la connettività VPN da punto a sito per connettersi al cloud privato in remoto. Per informazioni sull'installazione di un client per una connessione VPN da punto a sito, vedere [Configurare una connessione VPN al cloud privato.](set-up-vpn.md)

In un'area è possibile creare un gateway VPN da punto a sito e un gateway VPN da sito a sito.

## <a name="automatic-addition-of-vlansubnets"></a>Aggiunta automatica di VLAN/subnet

I gateway VPN CloudSimple forniscono criteri per l'aggiunta di VLAN/subnet ai gateway VPN.  I criteri consentono di specificare regole diverse per la gestione VLAN/subnet e VLAN/subnet definite dall'utente.  Le regole per la gestione della VLAN/subnet si applicano a tutti i nuovi cloud privati creati.  Le regole per le VLAN/subnet definite dall'utente consentono di aggiungere automaticamente qualsiasi nuova VLAN/subnet ai cloud privati esistenti o nuovi Per un gateway VPN da sito a sito, definire i criteri per ogni connessione.

I criteri per l'aggiunta di VLAN/subnet ai gateway VPN si applicano sia alla VPN da sito a sito che ai gateway VPN da punto a sito.

## <a name="automatic-addition-of-users"></a>Aggiunta automatica di utenti

Un gateway VPN da punto a sito consente di definire criteri di aggiunta automatica per i nuovi utenti. Per impostazione predefinita, tutti i proprietari e i collaboratori della sottoscrizione hanno accesso al portale CloudSimple.By default, all owners and contributors of the subscription have access to CloudSimple portal.  Gli utenti vengono creati solo quando il portale CloudSimple viene avviato per la prima volta.  La selezione **di Aggiungi regole** consente a qualsiasi nuovo utente di accedere alla rete CloudSimple utilizzando la connessione VPN da punto a sito.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Configurare un gateway VPN da sito a sito

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md) e selezionare **Rete**.
2. Selezionare **Gateway VPN**.
3. Fare clic su **Nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per **Configurazione gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da sito a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare il percorso di Azure in cui viene distribuito il servizio CloudSimple.Select the Azure location where your CloudSimple service is deployed.
    * Facoltativamente, abilitare la disponibilità elevata.

    ![Creare un gateway VPN da sito a sito](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > L'abilitazione della disponibilità elevata richiede che il dispositivo VPN locale supporti la connessione a due indirizzi IP. Questa opzione non può essere disabilitata dopo la distribuzione del gateway VPN.

5. Creare la prima connessione dalla rete locale e fare clic su **Avanti**.

    * Immettere un nome per identificare la connessione.
    * Per l'IP peer immettere l'indirizzo IP pubblico del gateway VPN locale.
    * Immettere l'identificatore peer del gateway VPN locale.  L'identificatore peer è in genere l'indirizzo IP pubblico del gateway VPN locale.  Se è stato configurato un identificatore specifico nel gateway, immettere l'identificatore.
    * Copiare la chiave condivisa da usare per la connessione dal gateway VPN locale.  Per modificare la chiave di condivisione predefinita e specificarne una nuova, fare clic sull'icona di modifica.
    * Per **Prefissi locali**, immettere i prefissi CIDR locali che accederanno alla rete CloudSimple.  È possibile aggiungere più prefissi CIDR quando si crea la connessione.

    ![Creare una connessione gateway VPN da sito a sito](media/create-vpn-gateway-s2s-connection.png)

6. Abilitare la VLAN/subnet nella rete Private Cloud a cui si accederà dalla rete locale e fare clic su **Avanti**.

    * Per aggiungere una VLAN/subnet di gestione, abilitare **Aggiungi VLAN/Subnet di gestione di cloud privati**.  La subnet di gestione è necessaria per le subnet vMotion e vSAN.
    * Per aggiungere subnet vMotion, abilitare **Aggiungi rete vMotion di cloud privati**.
    * Per aggiungere subnet vSAN, abilitare **Aggiungi subnet vSAN di cloud privati**.
    * Selezionare o deselezionare VLAN specifiche.

    ![Creare la connessione](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Rivedere le impostazioni e fare clic su **Invia**.

    ![Revisione e creazione del gateway VPN da sito a sito](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Creare un gateway VPN da punto a sito

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md) e selezionare **Rete**.
2. Selezionare **Gateway VPN**.
3. Fare clic su **Nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per **Configurazione gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da punto a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare il percorso di Azure in cui viene distribuito il servizio CloudSimple.Select the Azure location where your CloudSimple service is deployed.
    * Specificare la subnet client per il gateway da punto a sito.  Gli indirizzi DHCP verranno forniti dalla subnet client quando ci si connette.

5. In **Connessione/Utente**, specificare le seguenti impostazioni e fare clic su **Avanti**.

    * Per consentire automaticamente a tutti gli utenti attuali e futuri di accedere al cloud privato tramite il gateway da punto a sito, selezionare **Aggiungi automaticamente tutti gli utenti**. Quando si seleziona l'opzione, tutti gli utenti nell'elenco utenti vengono selezionati automaticamente. È possibile ignorare l'opzione automatica deselezionando singoli utenti nell'elenco.
    * Per selezionare singoli utenti, fare clic sulle caselle di controllo nell'elenco utenti.

6. La sezione VLAN/subnet consente di specificare le VLAN/subnet di gestione e utente per il gateway e le connessioni.

    * Le opzioni **aggiungi automaticamente** impostano i criteri globali per il gateway. Le impostazioni si applicano al gateway corrente. Le impostazioni possono essere sostituite nell'area **Seleziona.**
    * Selezionare **Aggiungi VLAN/subnet di gestione di cloud privati**. 
    * Per aggiungere tutte le VLAN/subnet definite dall'utente, fare clic su **Aggiungi VLAN/subnet definite dall'utente**.
    * Le impostazioni **di selezione** sostituiscono le impostazioni globali in **Aggiungi automaticamente**.

7. Fare clic su **Avanti** per rivedere le impostazioni. Fare clic sulle icone Modifica per apportare eventuali modifiche.
8. Fare clic su **Crea** per creare il gateway VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Subnet client e protocolli per il gateway VPN da punto a sito

Il gateway VPN da punto a sito consente connessioni TCP e UDP.  Scegliere il protocollo da utilizzare quando ci si connette dal computer selezionando la configurazione TCP o UDP.

La subnet client configurata viene utilizzata sia per i client TCP che UDP.  Il prefisso CIDR è diviso in due subnet, una per TCP e una per i client UDP. Scegliere la maschera prefisso in base al numero di utenti VPN che si connetteranno contemporaneamente.  

Nella tabella seguente è elencato il numero di connessioni client simultanee per la maschera di prefisso.

| Maschera prefisso | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Numero di connessioni TCP simultanee | 124 | 60 | 28 | 12 | 4 |
| Numero di connessioni UDP simultanee | 124 | 60 | 28 | 12 | 4 |

Per connettersi utilizzando la VPN da punto a sito, vedere [Connettersi a CloudSimple utilizzando la VPN da punto a sito.](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)
