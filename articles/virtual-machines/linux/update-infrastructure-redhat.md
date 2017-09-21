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
ms.date: 09/10/2017
ms.author: borisb
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 43d9095bcbd490abc6d01c214a17b8586f92ed1e
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure (RHUI) per macchine virtuali Red Hat Enterprise Linux su richiesta in Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) consente ai provider cloud (ad esempio Azure) di eseguire il mirroring del contenuto del repository ospitato da Red Hat, creare repository personalizzati con il contenuto specifico di Azure e rendere disponibili tali repository per le macchine virtuali degli utenti finali.

Le immagini di Red Hat Enterprise Linux (RHEL) con pagamento in base al consumo sono preconfigurate per accedere all'infrastruttura RHUI di Azure. Non è necessario eseguire altre attività di configurazione. Eseguire `sudo yum update` non appena l'istanza di RHEL è pronta per l'installazione degli aggiornamenti più recenti. Questo servizio è incluso nelle tariffe software di RHEL con pagamento in base al consumo.

## <a name="important-information-about-azure-rhui"></a>Informazioni importanti su Azure RHUI

1. L'infrastruttura RHUI attualmente supporta solo le versioni secondarie più recenti di una determinata famiglia RHEL (RHEL6 o RHEL7). L'istanza VM RHEL connessa all'infrastruttura RHUI esegue l'aggiornamento alla versione secondaria più recente quando si esegue `sudo yum update`. 

    Ad esempio, se si esegue il provisioning di una macchina virtuale dall'immagine con pagamento in base al consumo di RHEL 7.2 e si esegue `sudo yum update`, viene creata una macchina virtuale RHEL 7.4 (la versione secondaria più recente attualmente disponibile nella famiglia RHEL7).

    Per evitare questo comportamento, è necessario compilare un'immagine come descritto nell'articolo [Preparare una macchina virtuale basata su Red Hat per Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e connetterla a un'altra infrastruttura di aggiornamento ([direttamente ai server per la distribuzione di contenuti di Red Hat](https://access.redhat.com/solutions/253273) o ai server satellite di Red Hat).

2. L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. Annullando la registrazione di una VM RHEL con pagamento in base al consumo dall'infrastruttura RHUI ospitata in Azure non si converte la VM nel tipo BYOL (Bring-Your-Own-License). Se si registra la stessa macchina virtuale con un'altra origine di aggiornamenti, si potrebbe incorrere in addebiti doppi _indiretti_: la prima volta per la tariffa del software Azure RHEL e la seconda volta per le sottoscrizioni di Red Hat acquistate precedentemente. Se è necessario usare in modo continuativo un'infrastruttura di aggiornamento diversa dall'infrastruttura RHUI ospitata in Azure, prendere in considerazione la creazione e la distribuzione di immagini personalizzate di tipo BYOL, come descritto in [Preparare una macchina virtuale basata su Red Hat per Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

3. Due classi di immagini RHEL con pagamento in base al consumo (RHEL for SAP HANA e RHEL for SAP Business Applications) sono connesse ai canali RHUI dedicati che mantengono la versione secondaria di RHEL specificata come richiesto per la certificazione SAP. 

4. L'accesso all'istanza di RHUI ospitata in Azure è limitato alle macchine virtuali comprese negli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se si esegue l'inoltro tramite proxy del traffico delle macchine virtuali tramite l'infrastruttura di rete locale, potrebbe essere necessario configurare route definite dall'utente per consentire alle macchine virtuali di RHEL con pagamento in base al consumo di accedere all'infrastruttura RHUI di Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Gli indirizzi IP per i server di distribuzione di contenuti RHUI
Il servizio RHUI è disponibile in tutte le aree in cui sono disponibili le immagini di RHEL su richiesta. Include attualmente tutte le aree pubbliche elencate nella pagina del [dashboard di stato di Azure](https://azure.microsoft.com/status/), le aree di Azure per il governo degli Stati Uniti e le aree di Azure per la Germania. 

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

## <a name="rhui-azure-infrastructure-update"></a>Aggiornamento dell'infrastruttura RHUI di Azure

A settembre 2016 è stata distribuita l'infrastruttura RHUI di Azure aggiornata e ad aprile 2017 è stato eseguito l'arresto della precedente. Se si usano immagini RHEL con pagamento in base al consumo (o i relativi snapshot) da settembre 2016 o da un momento successivo, si viene connessi automaticamente alla nuova infrastruttura RHUI di Azure. Se invece si dispone di snapshot/VM precedenti, la loro configurazione deve essere aggiornata manualmente per accedere all'infrastruttura RHUI di Azure come descritto di seguito.

I nuovi server RHUI di Azure vengono distribuiti con [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/) in modo che un singolo endpoint (rhui-1.micrsoft.com) possa essere usato da qualsiasi macchina virtuale indipendentemente dall'area. 

### <a name="troubleshooting-connection-problems-to-azure-rhui"></a>Risoluzione dei problemi di connessione all'infrastruttura RHUI di Azure
Se si verificano problemi di connessione all'infrastruttura RHUI di Azure da una macchina virtuale con pagamento in base al consumo di Azure RHEL, seguire questi passaggi
1. Individuare nella configurazione della VM un endpoint Azure RHUI

    Controllare se il file `/etc/yum.repos.d/rh-cloud.repo` contiene riferimenti a `rhui-[1-3].microsoft.com` nel baseurl della sezione `[rhui-microsoft-azure-rhel*]` del file. In questo caso è in uso la nuova infrastruttura RHUI di Azure.

    Se punta un percorso con il modello `mirrorlist.*cds[1-4].cloudapp.net`, è necessario l'aggiornamento della configurazione. Se è in uso lo snapshot della VM precedente, eseguire l'aggiornamento in modo che punti la nuova infrastruttura RHUI di Azure.

2. L'accesso all'istanza di RHUI ospitata in Azure è limitato alle macchine virtuali comprese negli intervalli di indirizzi IP dei data center di Microsoft Azure (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Se è in uso la nuova configurazione ed è stato verificato che la VM si connette dall'intervallo di indirizzi IP di Azure ma non è possibile connettersi all'infrastruttura RHUI di Azure, rivolgersi al servizio di supporto di Microsoft o di Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura di aggiornamento manuale per l'uso dei server RHUI di Azure
Questa procedura viene descritta solo per riferimento. Le immagini RHEL con pagamento in base al consumo sono già configurate per connettersi all'infrastruttura RHUI di Azure.

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

Scaricare, verificare e installare l'RPM del client. 

> [!NOTE]
> Le versioni dei pacchetti vengono modificate. Se ci si connette manualmente all'infrastruttura RHUI di Azure, è possibile trovare la versione più recente del pacchetto client per ogni famiglia RHEL eseguendo il provisioning dell'immagine più recente della raccolta.
> 

Scaricare: per RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
```

Per RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
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

Al termine, verificare che sia possibile accedere all'infrastruttura RHUI di Azure dalla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale Red Hat Enterprise Linux dall'immagine con pagamento in base al consumo di Azure Marketplace e sfruttare i vantaggi dell'infrastruttura RHUI ospitata in Azure, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
