---
title: Azure VMware Solution by CloudSimple - Configurare VPN tra il cloud locale e privato
description: Descrive come configurare una connessione VPN da sito a sito o da punto a sito tra la rete locale e il cloud Simple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087135"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Configurare una connessione VPN al cloud privato CloudSimple

I gateway VPN consentono di connettersi alla rete CloudSimple dalla rete locale e da un computer client in remoto.  In questo articolo sono disponibili informazioni sulla configurazione dei gateway VPN dal portale CloudSimple.In this article, you can find information on setting up VPN gateways from the CloudSimple portal.  Una connessione VPN tra la rete locale e la rete CloudSimple fornisce l'accesso al vCenter e ai carichi di lavoro nel cloud privato. CloudSimple supporta sia la VPN da punto a sito che i gateway VPN da sito a sito.

## <a name="vpn-gateway-types"></a>Tipi di gateway VPN

* **La** connessione VPN da punto a sito è il modo più semplice per connettersi al cloud privato dal computer. Utilizzare la connettività VPN da punto a sito per connettersi al cloud privato in remoto.
* **La** connessione VPN da sito a sito consente di configurare i carichi di lavoro del cloud privato per accedere ai servizi locali. È inoltre possibile utilizzare Active Directory locale come origine di identità per l'autenticazione nel server virtuale del cloud privato.  Attualmente è supportato il tipo di **VPN basata su criteri.**

In un'area è possibile creare un gateway VPN da sito a sito e un gateway VPN da punto a sito.

## <a name="point-to-site-vpn"></a>VPN da punto a sito

Per creare un gateway VPN da punto a sito, vedere Creare un gateway VPN da punto [a sito.](vpn-gateway.md#create-point-to-site-vpn-gateway)

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Connettersi a CloudSimple tramite VPN da punto a sito

Il client VPN è necessario per la connessione a CloudSimple dal computer.  Scarica il [client OpenVPN](https://openvpn.net/community-downloads/) per Windows o [Viscosity](https://www.sparklabs.com/viscosity/download/) per macOS e OS X.

1. Avviare il portale CloudSimple e selezionare **Rete**.
2. Selezionare **Gateway VPN**.
3. Nell'elenco dei gateway VPN fare clic sul gateway VPN da punto a sito.
4. Selezionare **Utenti**.
5. Clicca su **Scarica la mia configurazione VPN**

    ![Scaricare la configurazione della VPN](media/download-p2s-vpn-configuration.png)

6. Importare la configurazione nel client VPN

    * Istruzioni per [l'importazione della configurazione nel client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Istruzioni per [l'importazione della configurazione su macOS o OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connettersi al gateway VPN CloudSimple.

Nell'esempio seguente viene illustrata l'importazione della connessione mediante **Viscosity Client**.

#### <a name="import-connection-on-viscosity-client"></a>Importa connessione sul client Viscosità

1. Estrarre il contenuto della configurazione VPN dal file .zip scaricato.

2. Apri Viscosity sul tuo computer.

3. Fare **+** clic sull'icona e selezionare **Importa connessione** > **da file**.

    ![Importa configurazione VPN da file](media/import-p2s-vpn-config.png)

4. Selezionare il file di configurazione OpenVPN (o.ovpn) per il protocollo che si desidera utilizzare e fare clic su **Apri**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

La connessione viene ora visualizzata nel menu Viscosità.

#### <a name="connect-to-the-vpn"></a>Connettersi alla VPN

Per connettersi a VPN utilizzando il client Viscosity OpenVPN, selezionare la connessione dal menu. L'icona del menu viene aggiornata per indicare che la connessione è stata stabilita.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Connessione a più cloud privati

Una connessione VPN da punto a sito risolve i nomi DNS del primo cloud privato creato. Quando si desidera accedere ad altri cloud privati, è necessario aggiornare il server DNS sul client VPN.

1. Avviare [il portale CloudSimple](access-cloudsimple-portal.md).

2. Passare a **Risorse** > **cloud privati** e selezionare il cloud privato a cui si desidera connettersi.

3. Nella pagina **Riepilogo** del cloud privato copiare l'indirizzo IP del server DNS Private Cloud in **Informazioni di base**.

    ![Server DNS cloud privato](media/private-cloud-dns-server.png)

4. Fare clic con il pulsante destro del mouse sull'icona Viscosità nella barra delle applicazioni del computer e selezionare **Preferenze**.

    ![VPN](media/vis00.png)

5. Selezionare la connessione VPN CloudSimple.

    ![Connessione VPN](media/viscosity-client.png)

6. Fare clic su **Modifica** per modificare le proprietà di connessione.

    ![Modifica connessione VPN](media/viscosity-edit-connection.png)

7. Fare clic sulla scheda **Rete** e immettere gli indirizzi IP del server ```cloudsimple.io```DNS Private Cloud separati da una virgola o uno spazio e il dominio come .  Selezionare **Ignora impostazioni DNS inviate dal server VPN**.

    ![Rete VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Per connettersi al primo cloud privato, rimuovere queste impostazioni e connettersi al server VPN.

## <a name="site-to-site-vpn"></a>VPN da sito a sito

Per creare un gateway VPN da sito a sito, vedere Creare un gateway VPN da [sito a sito.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)  La connessione VPN da sito a sito dalla rete locale al cloud privato offre questi vantaggi.  

* Accessibilità del tuo private Cloud vCenter da qualsiasi workstation nella rete locale
* Utilizzo di Active Directory locale come origine di identità vCenter
* Trasferimento pratico di modelli di VM, ISO e altri file dalle risorse locali al vCenter del cloud privato
* Accessibilità dei carichi di lavoro in esecuzione nel cloud privato dalla rete locale

Per configurare il gateway VPN locale in modalità a disponibilità elevata, vedere [Configurare una connessione VPN](high-availability-vpn-connection.md)a disponibilità elevata.

> [!IMPORTANT]
>    1. Impostare TCP MSS Clamping su 1200 sul dispositivo VPN. In alternativa, se i dispositivi VPN non supportano il bloccaggio MSS, in alternativa è possibile impostare l'MTU sull'interfaccia del tunnel su 1240 byte.
> 2. Dopo aver configurato la VPN da sito a sito, inoltrare le richieste DNS per il cloudsimple.io ai server DNS Private Cloud.  Seguire le istruzioni in [Configurazione DNS locale](on-premises-dns-setup.md).
