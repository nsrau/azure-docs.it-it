---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Informazioni su Red Hat Update Infrastructure per istanze di Red Hat Enterprise Linux su richiesta in Microsoft Azure
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
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: ea9a5cbd9b9b7b67ceb131cb8ba1d2476dbd5f72
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure per VM Red Hat Enterprise Linux su richiesta in Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) consente ai provider di servizi cloud (come Azure) di eseguire il mirroring del contenuto dei repository ospitati in Red Hat, creare repository personalizzati con contenuto specifico di Azure e garantirne la disponibilità per le VM degli utenti finali.

Le immagini di Red Hat Enterprise Linux (RHEL) con pagamento in base al consumo sono preconfigurate per accedere all'infrastruttura RHUI di Azure. Non è necessaria alcuna configurazione aggiuntiva. Per ottenere gli aggiornamenti più recenti, eseguire `sudo yum update` quando l'istanza di RHEL è pronta. Questo servizio è incluso nelle tariffe del software RHEL con pagamento in base al consumo.

## <a name="important-information-about-azure-rhui"></a>Informazioni importanti su Azure RHUI
* L'infrastruttura RHUI di Azure attualmente supporta solo la versione secondaria più recente di ogni famiglia RHEL (RHEL6 o RHEL7). Per aggiornare un'istanza di VM RHEL connessa a RHUI all'ultima versione secondaria, eseguire `sudo yum update`.

    Se si effettua il provisioning di una VM da un'immagine di RHEL 7.2 con pagamento in base al consumo e si esegue `sudo yum update`, ad esempio, verrà creata una VM RHEL 7.4 (ultima versione secondaria della famiglia RHEL7).

    Per evitare questo comportamento, è necessario compilare un'immagine personalizzata come descritto nell'articolo su come [creare e caricare una macchina virtuale basata su Red Hat per Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). L'immagine deve quindi essere connessa a una diversa infrastruttura di aggiornamento ([direttamente a server per la distribuzione di contenuti Red Hat](https://access.redhat.com/solutions/253273) o a un [server Red Hat Satellite](https://access.redhat.com/products/red-hat-satellite)).

* L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. Annullando la registrazione di una VM RHEL con pagamento in base al consumo nell'istanza di RHUI ospitata in Azure, la macchina virtuale non viene convertita in una VM di tipo BYOL (Bring Your Own License). Se si registra la stessa VM in un'altra origine di aggiornamenti, potrebbero essere applicati addebiti doppi _indiretti_: un primo addebito per la tariffa del software RHEL di Azure e un secondo per le sottoscrizioni di Red Hat acquistate in precedenza. Se è necessario usare in modo continuativo un'infrastruttura di aggiornamento diversa dall'istanza di RHUI ospitata in Azure, valutare la possibilità di creare e distribuire proprie immagini di tipo BYOL. Questo processo è descritto nell'articolo su come [creare e caricare una macchina virtuale basata su Red Hat per Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Due classi di immagini RHEL con pagamento in base al consumo (RHEL for SAP HANA e RHEL for SAP Business Applications) sono connesse a canali RHUI dedicati che vengono mantenuti nella versione secondaria di RHEL specificata come richiesto per la certificazione SAP. 

* L'accesso all'istanza di RHUI ospitata in Azure è limitato alle VM incluse negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se si esegue l'inoltro tramite proxy di tutto il traffico delle VM tramite un'infrastruttura di rete locale, potrebbe essere necessario configurare route definite dall'utente per consentire alle VM RHEL con pagamento in base al consumo di accedere all'infrastruttura RHUI di Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Gli indirizzi IP per i server di distribuzione di contenuti RHUI

Il servizio RHUI è disponibile in tutte le aree in cui sono disponibili le immagini di RHEL su richiesta. Include attualmente tutte le aree pubbliche elencate nella pagina del [dashboard di stato di Azure](https://azure.microsoft.com/status/) e le aree Azure US Government e Microsoft Azure Germania. 

Se si usa una configurazione di rete per limitare ulteriormente l'accesso da VM RHEL con pagamento in base al consumo, per il funzionamento di `yum update` verificare che siano consentiti gli indirizzi IP seguenti, a seconda dell'ambiente in cui ci si trova: 

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

Nel mese di settembre 2016 è stata distribuita un'infrastruttura RHUI di Azure aggiornata e nel mese di aprile 2017 è stata arrestata quella precedente. Se si usano immagini RHEL con pagamento in base al consumo (o i relativi snapshot) da settembre 2016 o da un momento successivo, si viene connessi automaticamente alla nuova infrastruttura RHUI di Azure. Se nelle VM sono presenti snapshot precedenti, invece, per accedere all'infrastruttura RHUI di Azure è necessario aggiornarne manualmente la configurazione come descritto in una sezione successiva.

I nuovi server RHUI di Azure vengono distribuiti con [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/). In Gestione traffico, un singolo endpoint (rhui-1.micrsoft.com) può essere usato da qualsiasi VM, indipendentemente dall'area. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Risoluzione dei problemi di connessione all'infrastruttura RHUI di Azure
Se si verificano problemi di connessione all'infrastruttura RHUI di Azure da una VM RHEL di Azure con pagamento in base al consumo, seguire questa procedura:

1. Individuare nella configurazione della VM un endpoint dell'infrastruttura RHUI di Azure:

    a. Controllare se la sezione `[rhui-microsoft-azure-rhel*]` del file `/etc/yum.repos.d/rh-cloud.repo` contiene un riferimento a `rhui-[1-3].microsoft.com` in `baseurl`. In questo caso si sta usando la nuova infrastruttura RHUI di Azure.

    b. Se punta a un percorso con il modello `mirrorlist.*cds[1-4].cloudapp.net`, è necessario un aggiornamento della configurazione. Se si sta usando uno snapshot di VM precedente, è necessario aggiornarlo in modo che punti alla nuova infrastruttura RHUI di Azure.

2. L'accesso all'istanza di RHUI ospitata in Azure è limitato alle VM incluse negli [intervalli IP dei data center di Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Se si sta usando la nuova configurazione ed è stato verificato che la VM si connette dall'intervallo IP di Azure ma non è comunque possibile connettersi all'infrastruttura RHUI di Azure, richiedere supporto tecnico a Microsoft o Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura di aggiornamento manuale per l'uso dei server RHUI di Azure
Questa procedura viene illustrata solo per riferimento. Le immagini RHEL con pagamento in base al consumo hanno già la configurazione corretta per connettersi all'infrastruttura RHUI di Azure. Per aggiornare manualmente la configurazione per l'uso dei server RHUI di Azure, seguire questa procedura:

1. Scaricare la firma a chiave pubblica tramite curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Verificare la validità della chiave scaricata.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Controllare l'output e quindi verificare `keyid` e `user ID packet`.

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

4. Installare la chiave pubblica.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Scaricare, verificare e installare un pacchetto RPM Package Manager (RPM) client.
    
    >[!NOTE]
    >Le versioni dei pacchetti variano. In caso di connessione manuale all'infrastruttura RHUI di Azure, è possibile trovare l'ultima versione del pacchetto client per ogni famiglia RHEL effettuando il provisioning dell'immagine più recente dalla raccolta.
  
   a. Eseguire il download. 
   
    - Per RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Per RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Verificare.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Controllare l'output per verificare che la firma del pacchetto sia corretta.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Installare il pacchetto RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. Al termine, verificare di poter accedere all'infrastruttura RHUI di Azure dalla VM.

## <a name="next-steps"></a>Passaggi successivi
Per creare una VM Red Hat Enterprise Linux da un'immagine con pagamento in base al consumo di Azure Marketplace e usare l'istanza di RHUI ospitata in Azure, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
