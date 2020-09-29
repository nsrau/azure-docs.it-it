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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449655"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurare un client NFS per Azure NetApp Files

La configurazione del client NFS descritta in questo articolo fa parte del programma di installazione quando si [Configura la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md) o si [Crea un volume a doppio protocollo](create-volumes-dual-protocol.md). È disponibile un'ampia gamma di distribuzioni Linux da usare con Azure NetApp Files. Questo articolo descrive le configurazioni per due degli ambienti usati più di frequente: RHEL 8 e Ubuntu 18,04. 

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
Questa sezione descrive la configurazione di Ubuntu per i client NFS.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Se si usa la crittografia Kerberos NFSv 4.1 

1. Installare i pacchetti:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configurare il client NTP.  
    Ubuntu 18,04 USA `chrony` per impostazione predefinita.  Seguendo le linee guida per la configurazione di [Ubuntu Bionic: uso di Chrony per configurare NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Aggiungere la Dominio di Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Se si usa il protocollo duale  

1. Eseguire il comando seguente per aggiornare i pacchetti installati:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Esempio:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Eseguire il comando seguente per riavviare e abilitare il servizio:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

Nell'esempio seguente viene eseguita una query sul server AD LDAP dal client LDAP Ubuntu per un utente LDAP `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creare un volume con doppio protocollo per Azure NetApp Files](create-volumes-dual-protocol.md)

