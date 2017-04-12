---
title: Red Hat Update Infrastructure (RHUI) | Microsoft Docs
description: Informazioni sul servizio Red Hat Update Infrastructure (RHUI) per istanze di Red Hat Enterprise Linux in Microsoft Azure
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3ea6083425b9f881ba82077cfecbe24964304a79
ms.lasthandoff: 04/03/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure (RHUI) per macchine virtuali Red Hat Enterprise Linux su richiesta in Azure
Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux (RHEL) su richiesta disponibili in Azure Marketplace vengono registrate per accedere al servizio Red Hat Update Infrastructure (RHUI) distribuito in Azure.  Le istanze di RHEL su richiesta potranno accedere a repository yum a livello di area e potranno ricevere aggiornamenti incrementali.

L'elenco di repository yum, gestito da RHUI, viene configurato nell'istanza di RHEL durante il provisioning. Non è necessario eseguire altre attività di configurazione. Eseguire `yum update` non appena l'istanza di RHEL è pronta per l'installazione degli aggiornamenti più recenti.

> [!NOTE]
> In settembre 2016 è stata distribuita l'infrastruttura RHUI di Azure aggiornata e in gennaio 2017 è stato avviato l'arresto graduale della precedente. Se si usano immagini RHEL (o i relativi snapshot) da settembre 2016 o da un momento successivo probabilmente non è richiesta alcuna azione. Se invece si dispone di snapshot/VM precedenti, la loro configurazione deve essere aggiornata per l'accesso ininterrotto all'infrastruttura RHUI di Azure.
> 

## <a name="rhui-azure-infrastructure-update"></a>Aggiornamento dell'infrastruttura RHUI di Azure
A partire da settembre 2016, Azure offre un nuovo set di server RHUI (Red Hat Update Infrastructure). Questi server vengono distribuiti con [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/) in modo che un singolo endpoint (rhui-1.micrsoft.com) possa essere usato da qualsiasi VM indipendentemente dall'area. Le nuove immagini RHEL Pay-As-You-Go (PAYG) con pagamento in base al consumo in Azure Marketplace (versioni di settembre 2016 o successive) puntano ai nuovi server RHUI di Azure senza richiedere altre operazioni.

### <a name="determine-if-action-is-required"></a>Determinare se è richiesta qualche azione
Se si verificano problemi di connessione all'infrastruttura RHUI di Azure da una VM con pagamento in base al consumo di Azure RHEL, seguire questi passaggi
1. Individuare nella configurazione della VM un endpoint Azure RHUI

    Controllare se il file `/etc/yum.repos.d/rh-cloud.repo` contiene riferimenti a `rhui-[1-3].microsoft.com` nel baseurl della sezione `[rhui-microsoft-azure-rhel*]` del file. In questo caso è in uso la nuova infrastruttura RHUI di Azure.

    Se punta un percorso con il modello `mirrorlist.*cds[1-4].cloudapp.net`, è necessario l'aggiornamento della configurazione.

    Se è in uso la nuova configurazione ma non è possibile connettersi all'infrastruttura RHUI di Azure, rivolgersi al servizio di supporto di Microsoft o di Red Hat.

    > [!NOTE]
    > L'accesso all'istanza di RHUI ospitata in Azure è limitato alle macchine virtuali comprese negli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
    > 

2. Se la vecchia infrastruttura RHUI di Azure è ancora disponibile quando si eseguire questo controllo e si desidera aggiornare automaticamente la configurazione, eseguire il comando seguente:

    `sudo yum update RHEL6`o `sudo yum update RHEL7` a seconda della versione della famiglia RHEL.

3. Se non è più possibile connettersi alla vecchia infrastruttura RHUI di Azure, seguire i passaggi manuali descritti nella sezione successiva.

4. Assicurarsi di aggiornare la configurazione sull'immagine/snapshot sorgente da cui è stato eseguito il provisioning della VM interessata.

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>Arresto graduale della precedente infrastruttura RHUI di Azure
Durante l'arresto della precedente infrastruttura RHUI di Azure, l'accesso all'infrastruttura stessa è limitato come segue:

1. Accesso ulteriormente limitato (elenco di controllo di accesso) al gruppo di indirizzi IP già connessi. Possibili effetti collaterali: se si continua a usare la precedente infrastruttura RHUI di Azure, le nuove VM potrebbero non riuscire a connettersi a essa. Le VM RHEL con indirizzi IP dinamici che passano attraverso la sequenza di arresto/deallocazione/avvio possono ricevere un nuovo IP e pertanto potrebbero iniziare a non riuscire a connettersi alla precedente infrastruttura RHUI di Azure

2. Arresto del server di distribuzione di contenuti di mirror. Possibili effetti collaterali: poiché sono stati arrestati altri CDSes, potrebbe essere necessario più tempo per la manutenzione di `yum update` e potrebbero verificarsi più timeout fino a quando non sarà più possibile connettersi alla vecchia infrastruttura RHUI di Azure.

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Gli indirizzi IP per i nuovi server di distribuzione di contenuti RHUI sono
Se si usa la configurazione di rete per limitare ulteriormente l'accesso dalle VM RHEL con pagamento in base al consumo, assicurarsi che siano consentiti i seguenti IP per il funzionamento di `yum update` a seconda dell'ambiente in cui ci si trova. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedura di aggiornamento manuale per l'uso dei nuovi server RHUI di Azure
Scaricare (tramite curl) la firma con chiave pubblica

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verificare la chiave scaricata

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Controllare l'output, verificare `keyid` e `user ID packet`:

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installare la chiave pubblica

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Scaricare, verificare e installare l'RPM del client

Scaricare: per RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Per RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verificare:

```bash
rpm -Kv azureclient.rpm
```

Controllare nell'output che la firma del pacchetto sia corretta

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installare l'RPM

```bash
sudo rpm -U azureclient.rpm
```

Al termine, verificare che sia possibile accedere all'infrastruttura RHUI di Azure dalla macchina virtuale

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>Script all-in-one per automatizzare l'attività precedente
Usare lo script seguente, in base alle proprie esigenze, per automatizzare l'attività di aggiornamento delle macchine virtuali interessate ai nuovi server RHUI di Azure.

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Panoramica di RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) offre una soluzione altamente scalabile per gestire il contenuto del repository yum per le istanze cloud di Red Hat Enterprise Linux ospitate dai provider cloud certificati da Red Hat. In base al progetto Pulp upstream, RHUI consente ai provider cloud di eseguire il mirroring locale del contenuto del repository ospitato da Red Hat e creare repository personalizzati con il proprio contenuto e rendere disponibili tali repository per un ampio gruppo di utenti finali tramite un sistema di distribuzione del contenuto con bilanciamento del carico.

## <a name="regions-where-rhui-is-available"></a>Aree in cui viene è disponibile l'infrastruttura RHUI
Il servizio RHUI è disponibile in tutte le aree in cui sono disponibili le immagini di RHEL su richiesta. Include attualmente tutte le aree pubbliche elencate nella pagina del [dashboard di stato di Azure](https://azure.microsoft.com/status/), le aree di Azure per il governo degli Stati Uniti e le aree di Azure per la Germania. L'accesso al servizio RHUI per macchine virtuali sottoposte a provisioning da immagini di RHEL su richiesta è incluso nel rispettivo prezzo. La disponibilità di cloud locale/nazionale verrà aggiornata in base all'espansione della disponibilità di RHEL su richiesta in futuro.

> [!NOTE]
> L'accesso all'istanza di RHUI ospitata in Azure è limitato alle macchine virtuali comprese negli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## <a name="get-updates-from-another-update-repository"></a>Ottenere aggiornamenti da un altro repository di aggiornamenti
Se è necessario ottenere gli aggiornamenti da un repository di aggiornamento diverso (anziché da un'infrastruttura RHUI ospitata in Azure), prima è necessario annullare la registrazione delle istanze dall'infrastruttura RHUI. Quindi è necessario registrarle nuovamente con l'infrastruttura di aggiornamento desiderata (ad esempio Red Hat Satellite o la rete CDN Red Hat Customer Portal). Saranno necessarie sottoscrizioni di Red Hat appropriate per questi servizi e una registrazione per il programma [Red Hat Cloud Access in Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Per annullare la registrazione all'infrastruttura RHUI e registrarsi all'infrastruttura di aggiornamento desiderata, seguire questa procedura:

1. Modificare /etc/yum.repos.d/rh-cloud.repo e sostituire tutte le occorrenze di `enabled=1` con `enabled=0`. ad esempio:
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. Modificare /etc/yum/pluginconf.d/rhnplugin.conf e sostituire tutte le occorrenze di `enabled=0` con `enabled=1`.
3. Eseguire quindi la registrazione all'infrastruttura desiderata, ad esempio il portale dei clienti di Red Hat. Per informazioni su come [eseguire la registrazione e la sottoscrizione di un sistema nel portale dei clienti di Red Hat](https://access.redhat.com/solutions/253273), vedere la guida della soluzione Red Hat.

> [!NOTE]
> L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. Annullando la registrazione di una VM RHEL con pagamento in base al consumo dall'infrastruttura RHUI ospitata in Azure non si converte la VM nel tipo BYOL (Bring-Your-Own-License). Se si registra la stessa VM con un'altra origine di aggiornamenti si potrebbe incorrere in addebiti doppi: la prima volta per la tariffa del software Azure RHEL e la seconda volta per le sottoscrizioni di Red Hat. 
> 
> Se è necessario usare in modo continuativo un'infrastruttura di aggiornamento diversa dall'infrastruttura RHUI ospitata in Azure, prendere in considerazione la creazione e la distribuzione di immagini personalizzate di tipo BYOL, come descritto nell'articolo [Preparare una macchina virtuale basata su RedHat per Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
> 

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale Red Hat Enterprise Linux dall'immagine con pagamento in base al consumo di Azure Marketplace e sfruttare i vantaggi dell'infrastruttura RHUI ospitata in Azure, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Sarà possibile usare `yum update` nell'istanza di RHEL senza impostazioni aggiuntive.


