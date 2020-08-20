---
title: Configurare un client NFS per Azure NetApp Files | Microsoft Docs
description: Viene descritto come configurare i client NFS per l'utilizzo con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 4a54858f4a09cd62bb555a6df2c12efa2290dd69
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653172"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurare un client NFS per Azure NetApp Files

È disponibile un'ampia gamma di distribuzioni Linux da usare con Azure NetApp Files. Questo articolo descrive le configurazioni per due degli ambienti usati più di frequente: RHEL 8 e Ubuntu 18,04. La configurazione del client NFS descritta in questo articolo fa parte del programma di installazione quando si [Configura la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md) o si [Crea un volume a doppio protocollo](create-volumes-dual-protocol.md).  

Indipendentemente dal sapore di Linux usato, sono necessarie le configurazioni seguenti:
* Configurare un client NTP per evitare problemi con sfasamento dell'ora.
* Configurare le voci DNS del client Linux per la risoluzione dei nomi.  
    Questa configurazione include il record "A" (in poi) e il record PTR (inverso). 
* Per l'aggiunta a un dominio, creare un account computer nel Active Directory di destinazione, che viene creato durante il comando Realm join. 
    > [!NOTE] 
    > La `$SERVICEACCOUNT` variabile usata nei comandi seguenti deve essere un account utente con autorizzazioni o delega per creare un account computer nell'unità organizzativa di destinazione.
* Consentire al client di montare volumi NFS e altri strumenti di monitoraggio pertinenti.

## <a name="rhel-8-configuration"></a>Configurazione di RHEL 8 

1. Installare i pacchetti:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Configurare il client NTP:  
    RHEL 8 USA `chrony` per impostazione predefinita.  Seguire le linee guida per la configurazione in [uso di Chrony Suite per configurare NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Aggiungere il dominio Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Configurazione di Ubuntu 

1. Installare i pacchetti:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configurare il client NTP.  
    Ubuntu 18,04 USA `chrony` per impostazione predefinita.  Seguendo le linee guida per la configurazione di [Ubuntu Bionic: uso di Chrony per configurare NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Aggiungere la Dominio di Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creare un volume con doppio protocollo per Azure NetApp Files](create-volumes-dual-protocol.md)

