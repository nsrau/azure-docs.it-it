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
ms.date: 10/19/2020
ms.author: b-juche
ms.openlocfilehash: f4b8b4b56693023ede2ccf8ae7eeac7ed5e16824
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216862"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurare un client NFS per Azure NetApp Files

La configurazione del client NFS descritta in questo articolo fa parte del programma di installazione quando si [Configura la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md) o si [Crea un volume a doppio protocollo](create-volumes-dual-protocol.md). È disponibile un'ampia gamma di distribuzioni Linux da usare con Azure NetApp Files. Questo articolo descrive le configurazioni per due degli ambienti usati più di frequente: RHEL 8 e Ubuntu 18,04. 

Indipendentemente dal sapore di Linux usato, sono necessarie le configurazioni seguenti:
* Configurare un client NTP per evitare problemi con sfasamento dell'ora.
* Configurare le voci DNS del client Linux per la risoluzione dei nomi.  
    Questa configurazione deve includere il record "A" (in poi) e il record PTR (inverso). 
* Per l'aggiunta a un dominio, creare un account computer per il client Linux nel Active Directory di destinazione, che viene creato durante il comando Realm join. 
    > [!NOTE] 
    > La `$SERVICEACCOUNT` variabile usata nei comandi seguenti deve essere un account utente con autorizzazioni o delega per creare un account computer nell'unità organizzativa di destinazione.

## <a name="rhel-8-configuration"></a>Configurazione di RHEL 8 

Questa sezione descrive le configurazioni RHEL necessarie per la crittografia Kerberos NFSv 4.1 e il protocollo duale.  

Gli esempi in questa sezione usano il nome di dominio e l'indirizzo IP seguenti:  

* Nome di dominio: `contoso.com`
* IP privato: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>Configurazione di RHEL 8 se si usa la crittografia Kerberos NFSv 4.1

1. Configurare `/etc/resolv.conf` con il server DNS appropriato.  

    Ad esempio:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Aggiungere il record client NFS nel server DNS per la zona DNS di ricerca diretta e inversa.

3. Per verificare DNS, usare i comandi seguenti dal client NFS:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Installare i pacchetti:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Configurare il client NTP.  

    RHEL 8 USA Chrony per impostazione predefinita. Seguendo le linee guida per la configurazione in [uso del `Chrony` gruppo per configurare NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Aggiungere il dominio Active Directory:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Ad esempio: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`


7. Riavvia tutti i servizi NFS:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Il riavvio impedisce la condizione di errore `“mount.nfs: an incorrect mount option was specified”` durante il montaggio di Kerberos.

8. Eseguire il `kinit` comando con l'account utente per ottenere i ticket: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Ad esempio:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>Configurazione di RHEL 8 se si usa il protocollo duale

I passaggi seguenti sono facoltativi. È necessario eseguire la procedura solo se si usa il mapping degli utenti nel client NFS: 

1. Completare tutti i passaggi descritti nella [configurazione RHEL 8 se si usa la sezione crittografia Kerberos NFSv 4.1](#rhel8_nfsv41_kerberos) .   

2. Aggiungere un record DNS statico nel file con estensione hosts per usare il nome di dominio completo (FQDN) per Active Directory, anziché usare l'indirizzo IP nel file di configurazione SSSD:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Aggiungere una sezione aggiuntiva per i domini per risolvere gli identificatori dal server AD LDAP:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Verificare che `/etc/nsswitch.conf` sia presente la `sss` voce:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Riavviare il `sssd` servizio e cancellare la cache:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Eseguire un test per assicurarsi che il client sia integrato con il server LDAP:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Configurazione di Ubuntu   

Questa sezione descrive le configurazioni di Ubuntu necessarie per la crittografia Kerberos NFSv 4.1 e il protocollo duale. 

Gli esempi in questa sezione usano il nome di dominio e l'indirizzo IP seguenti:  

* Nome di dominio: `contoso.com`
* IP privato: `10.6.1.4`

1. Configurare `/etc/resolv.conf` con il server DNS appropriato:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Aggiungere un record client NFS nel server DNS per la zona DNS di ricerca diretta e inversa.
 
    Per verificare DNS, usare i comandi seguenti dal client NFS:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Installare i pacchetti:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Quando richiesto, `$DOMAIN.NAME` l'input (usando maiuscolo, ad esempio, `CONTOSO.COM` ) come area di autenticazione Kerberos predefinita.

4. Riavviare il servizio `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18,04 USA Chrony per impostazione predefinita. Seguendo le linee guida per la configurazione di [Ubuntu Bionic: uso di Chrony per configurare NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Aggiungere la Dominio di Active Directory:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Ad esempio:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. Eseguire `kinit` con l'utente per ottenere i ticket: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Ad esempio:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Configurazione di Ubuntu se si usa il protocollo duale  

I passaggi seguenti sono facoltativi.  È necessario eseguire la procedura solo se si vuole usare il mapping degli utenti nel client NFS:  

1. Eseguire il comando seguente per aggiornare i pacchetti installati:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Nell'esempio seguente vengono utilizzati i valori di esempio. Quando il comando richiede l'immissione dell'input, è necessario fornire l'input in base all'ambiente in uso. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Eseguire il comando seguente per riavviare e abilitare il servizio:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

Nell'esempio seguente viene eseguita una query sul server AD LDAP dal client LDAP Ubuntu per un utente LDAP `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creare un volume con doppio protocollo per Azure NetApp Files](create-volumes-dual-protocol.md)

