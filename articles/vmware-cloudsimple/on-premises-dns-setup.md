---
title: 'Soluzione VMware di Azure di CloudSimple: configurare DNS per il cloud privato CloudSimple'
description: Viene descritto come configurare la risoluzione dei nomi DNS per l'accesso al server vCenter in un cloud privato CloudSimple da workstation locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ed20dd9068ccdea1fb9cd04801eb0096fed2c922
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972438"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configurare DNS per la risoluzione dei nomi per il cloud privato accesso vCenter da workstation locali

Per accedere al server vCenter in un cloud privato CloudSimple dalle workstation locali, è necessario configurare la risoluzione degli indirizzi DNS in modo che il server vCenter possa essere indirizzato in base al nome host e all'indirizzo IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Ottenere l'indirizzo IP del server DNS per il cloud privato

1. Accedere al portale di [CloudSimple](access-cloudsimple-portal.md).

2. Passare a **risorse** > Cloud**privati** e selezionare il cloud privato a cui si vuole connettersi.

3. Nella pagina **Riepilogo** del cloud privato in informazioni di **base**copiare l'indirizzo IP del server DNS del cloud privato.

    ![Server DNS del cloud privato](media/private-cloud-dns-server.png)


Usare una di queste opzioni per la configurazione DNS.

* [Creare una zona nel server DNS per *. cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Creare un server d'istruzione locale nel server DNS locale per risolvere *. cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Creare una zona nel server DNS per *. cloudsimple.io

È possibile configurare un'area come area di stub e puntare ai server DNS nel cloud privato per la risoluzione dei nomi. In questa sezione vengono fornite informazioni sull'utilizzo di un server DNS di binding o di un server DNS di Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Creare una zona in un server DNS di binding

Il file e i parametri specifici da configurare possono variare in base alla configurazione del singolo DNS.

Per la configurazione predefinita del server di binding, ad esempio, modificare il file/etc/named.conf nel server DNS e aggiungere le seguenti informazioni sulla zona.

```
zone “cloudsimple.io”
{
    type stub;
    masters { IP address of DNS servers; };
    file “slaves/cloudsimple.io.db”;
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Creare una zona in un server DNS Microsoft Windows

1. Fare clic con il pulsante destro del mouse sul server DNS e scegliere **nuova zona**.  
![Nuova zona](media/DNS01.png)
2. Selezionare **zona Stub** e fare clic su **Avanti**.
![Nuova zona](media/DNS02.png)
3. Selezionare l'opzione appropriata in base all'ambiente in uso e fare clic su **Avanti**.
![Nuova zona](media/DNS03.png)
4. Selezionare **zona** di ricerca diretta e fare clic su **Avanti**.
![Nuova zona](media/DNS01.png)
5. Immettere il nome della zona e fare clic su **Avanti**.
![Nuova zona](media/DNS05.png)
6. Immettere gli indirizzi IP dei server DNS per il cloud privato ottenuti dal portale di CloudSimple.
![Nuova zona](media/DNS06.png)
7. Fare clic su **Avanti** se necessario per completare l'installazione della procedura guidata.

## <a name="create-a-conditional-forwarder"></a>Creazione di un server d'avanzamento condizionale

Un server d'avanzamento condizionale Invia tutte le richieste di risoluzione dei nomi DNS al server designato. Con questa configurazione, qualsiasi richiesta a *. cloudsimple.io viene trasmessa ai server DNS che si trovano nel cloud privato. Gli esempi seguenti illustrano come configurare i server d'avanzamento su diversi tipi di server DNS.

### <a name="create-a-conditional-forwarded-on-a-bind-dns-server"></a>Creare un'associazione condizionale in un server DNS di binding

Il file e i parametri specifici da configurare possono variare in base alla configurazione del singolo DNS.

Per la configurazione predefinita del server di binding, ad esempio, modificare il file/etc/named.conf nel server DNS e aggiungere le seguenti informazioni di invio condizionale.

```
zone “cloudsimple.io” {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Creazione di un server d'invio condizionale in un server DNS Microsoft Windows

1. Aprire il gestore DNS nel server DNS.
2. Fare clic con il pulsante destro del mouse su **Server** d'inoltri condizionali e selezionare l'opzione per aggiungere un nuovo server d'istruzione.
![Server di invio condizionale 1 DNS Windows](media/DNS08.png)
3. Immettere il dominio DNS e l'indirizzo IP dei server DNS nel cloud privato e fare clic su **OK**.
