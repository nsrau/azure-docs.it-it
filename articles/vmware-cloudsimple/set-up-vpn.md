---
title: "Soluzione VMware di Azure di CloudSimple: configurare la VPN tra l'ambiente locale e il cloud privato"
description: Viene descritto come configurare una connessione VPN da sito a sito o da punto a sito tra la rete locale e il cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972387"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Configurare una connessione VPN al cloud privato CloudSimple

I gateway VPN consentono di connettersi alla rete CloudSimple dalla rete locale e da un computer client in remoto.  In questo articolo sono disponibili informazioni sulla configurazione dei gateway VPN dal portale di CloudSimple.  Una connessione VPN tra la rete locale e la rete CloudSimple fornisce l'accesso a vCenter e ai carichi di lavoro nel cloud privato. CloudSimple supporta sia la VPN da punto a sito che i gateway VPN da sito a sito.

## <a name="vpn-gateway-types"></a>Tipi di gateway VPN

* La connessione **VPN da punto a sito** è il modo più semplice per connettersi al cloud privato dal computer. Usare la connettività VPN da punto a sito per la connessione remota al cloud privato.
* La connessione **VPN da sito a sito** consente di configurare i carichi di lavoro del cloud privato per accedere ai servizi locali. È anche possibile usare Active Directory locali come origine di identità per l'autenticazione nel cloud privato vCenter.  Attualmente, il tipo di **VPN basato su criteri** è supportato.

In un'area è possibile creare un gateway VPN da sito a sito e un gateway VPN da punto a sito.

## <a name="point-to-site-vpn"></a>VPN da punto a sito

Per creare un gateway VPN da punto a sito, vedere [creare un gateway VPN da punto a sito](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Connettersi a CloudSimple tramite una VPN da punto a sito

Il client VPN è necessario per la connessione a CloudSimple dal computer.  Scaricare il [client OpenVPN](https://openvpn.net/community-downloads/) per Windows o [la viscosità](https://www.sparklabs.com/viscosity/download/) per MacOS e OS X.

1. Avviare il portale di CloudSimple e selezionare **rete**.
2. Selezionare **gateway VPN**.
3. Dall'elenco dei gateway VPN fare clic sul gateway VPN da punto a sito.
4. Selezionare **Utenti**.
5. Fare clic su **Scarica configurazione VPN**

    ![Scarica configurazione VPN](media/download-p2s-vpn-configuration.png)

6. Importare la configurazione nel client VPN

    * Istruzioni per l' [importazione della configurazione nel client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Istruzioni per l' [importazione della configurazione in MacOS o OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connettersi al gateway VPN CloudSimple.

Nell'esempio seguente viene illustrata l'importazione della connessione tramite il **client**di viscosità.

#### <a name="import-connection-on-viscosity-client"></a>Importa connessione nel client di viscosità

1. Estrarre il contenuto della configurazione VPN dal file zip scaricato.

2. Apertura della viscosità nel computer.

3. Fare clic sull' > icona e selezionare Importa connessione dal file **+** .

    ![Importa configurazione VPN da file](media/import-p2s-vpn-config.png)

4. Selezionare il file di configurazione OpenVPN (con estensione ovpn) per il protocollo che si vuole usare e fare clic su **Apri**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

La connessione viene ora visualizzata nel menu di viscosità.

#### <a name="connect-to-the-vpn"></a>Connettersi alla VPN

Per connettersi alla VPN usando il client di viscosità OpenVPN, selezionare la connessione dal menu. L'icona di menu viene aggiornata per indicare che la connessione è stata stabilita.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Connessione a più cloud privati

Una connessione VPN da punto a sito risolve i nomi DNS del primo cloud privato creato. Quando si desidera accedere ad altri cloud privati, è necessario aggiornare il server DNS nel client VPN.

1. Avviare il [portale di CloudSimple](access-cloudsimple-portal.md).

2. Passare a **risorse** > Cloud**privati** e selezionare il cloud privato a cui si vuole connettersi.

3. Nella pagina **Riepilogo** del cloud privato copiare l'indirizzo IP del server DNS del cloud privato in **informazioni di base**.

    ![Server DNS del cloud privato](media/private-cloud-dns-server.png)

4. Fare clic con il pulsante destro del mouse sull'icona di viscosità nella barra delle applicazioni del computer e selezionare **Preferenze**.

    ![VPN](media/vis00.png)

5. Selezionare la connessione VPN CloudSimple.

    ![Connessione VPN:](media/viscosity-client.png)

6. Fare clic su **modifica** per modificare le proprietà di connessione.

    ![Modifica connessione VPN](media/viscosity-edit-connection.png)

7. Fare clic sulla scheda **rete** e immettere gli indirizzi IP del server DNS del cloud privato separati da una virgola o da ```cloudsimple.io```uno spazio e dal dominio come.  Selezionare **Ignora impostazioni DNS inviate dal server VPN**.

    ![Rete VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Per connettersi al primo cloud privato, rimuovere queste impostazioni e connettersi al server VPN.

## <a name="site-to-site-vpn"></a>VPN da sito a sito

Per creare un gateway VPN da sito a sito, vedere creare un gateway [VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  La connessione VPN da sito a sito dalla rete locale al cloud privato offre questi vantaggi.  

* Accessibilità del cloud privato vCenter da qualsiasi workstation nella rete locale
* Uso del Active Directory locale come origine di identità vCenter
* Trasferimento pratico di modelli di VM, ISOs e altri file dalle risorse locali al cloud privato vCenter
* Accessibilità dei carichi di lavoro in esecuzione nel cloud privato dalla rete locale

Per configurare il gateway VPN locale in modalità a disponibilità elevata, vedere [configurare una connessione VPN a disponibilità elevata](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Impostare la pressione TCP MSS su 1200 sul dispositivo VPN. In alternativa, se i dispositivi VPN non supportano il blocco MSS, è invece possibile impostare il MTU sull'interfaccia del tunnel su 1240 byte.
> 2. Dopo aver configurato la VPN da sito a sito, inviare le richieste DNS per *. cloudsimple.io ai server DNS del cloud privato.  Seguire le istruzioni in [configurazione DNS locale](on-premises-dns-setup.md).
