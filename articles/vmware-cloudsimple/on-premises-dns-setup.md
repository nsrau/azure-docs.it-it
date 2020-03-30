---
title: Azure VMware Soluzione di CloudSimple - Configurare DNS per CloudSimple Private Cloud
description: Descrive come configurare la risoluzione dei nomi DNS per l'accesso al server vCenter in un cloud privato CloudSimple da workstation locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246110"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configurare DNS per la risoluzione dei nomi per l'accesso al Private Cloud vCenter dalle workstation locali

Per accedere al server vCenter su un cloud privato CloudSimple dalle workstation locali, è necessario configurare la risoluzione degli indirizzi DNS in modo che il server vCenter possa essere indirizzato in base al nome host e all'indirizzo IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Ottenere l'indirizzo IP del server DNS per il cloud privato

1. Accedere al [portale CloudSimple](access-cloudsimple-portal.md).

2. Passare a **Risorse** > **cloud privati** e selezionare il cloud privato a cui si desidera connettersi.

3. Nella pagina **Riepilogo** del cloud privato in **Informazioni di base**copiare l'indirizzo IP del server DNS Private Cloud.

    ![Server DNS cloud privato](media/private-cloud-dns-server.png)


Utilizzare una di queste opzioni per la configurazione DNS.

* [Creare una zona sul server DNS per il cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Creare un server d'inoltro condizionale nel server DNS locale per risolvere i file con estensione cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Creare una zona sul server DNS per il cloudsimple.io

È possibile configurare una zona come zona di stub e puntare ai server DNS nel cloud privato per la risoluzione dei nomi. In questa sezione vengono fornite informazioni sull'utilizzo di un server DNS BIND o di un server DNS di Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Creare una zona in un server DNS BIND

Il file e i parametri specifici da configurare possono variare in base alla configurazione DNS individuale.

Ad esempio, per la configurazione predefinita del server BIND, modificare il file /etc/named.conf sul server DNS e aggiungere le seguenti informazioni di zona.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Creare una zona in un server DNS Di Microsoft Windows

1. Fare clic con il pulsante destro del mouse sul server DNS e scegliere **Nuova zona**. 
  
    ![Nuova zona](media/DNS01.png)
2. Selezionare **Zona di stub** e fare clic su **Avanti**.

    ![Nuova zona](media/DNS02.png)
3. Selezionare l'opzione appropriata a seconda dell'ambiente e fare clic su **Avanti**.

    ![Nuova zona](media/DNS03.png)
4. Selezionare **Inoltra zona di ricerca** e fare clic su **Avanti**.

    ![Nuova zona](media/DNS01.png)
5. Immettere il nome dell'area e fare clic su **Avanti**.

    ![Nuova zona](media/DNS05.png)
6. Immettere gli indirizzi IP dei server DNS per il cloud privato ottenuti dal portale CloudSimple.

    ![Nuova zona](media/DNS06.png)
7. Fare clic su **Avanti** in base alle esigenze per completare la configurazione della procedura guidata.

## <a name="create-a-conditional-forwarder"></a>Creare un server d'inoltro condizionaleCreate a conditional forwarder

Un server d'inoltro condizionale inoltra tutte le richieste di risoluzione dei nomi DNS al server designato. Con questa configurazione, qualsiasi richiesta a cloudsimple.io viene inoltrata ai server DNS che si trovano nel cloud privato. Negli esempi seguenti viene illustrato come configurare i server d'inoltro su diversi tipi di server DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Creare un server d'inoltro condizionale in un server DNS BIND

Il file e i parametri specifici da configurare possono variare in base alla configurazione DNS individuale.

Ad esempio, per la configurazione predefinita del server BIND, modificare il file /etc/named.conf sul server DNS e aggiungere le seguenti informazioni di inoltro condizionale.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Creare un server d'inoltro condizionale in un server DNS di Microsoft WindowsCreate a conditional forwarder on a Microsoft Windows DNS server

1. Aprire Il gestore DNS sul server DNS.
2. Fare clic con il pulsante destro del mouse su **Server d'inoltro condizionale** e selezionare l'opzione per aggiungere un nuovo server d'inoltro condizionale.

    ![Server d'inoltro condizionale 1 Windows DNS](media/DNS08.png)
3. Immettere il dominio DNS e l'indirizzo IP dei server DNS nel cloud privato, quindi fare clic su **OK**.
