---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Informazioni su Red Hat Update Infrastructure per istanze di Red Hat Enterprise Linux su richiesta in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: ac3b29e3cd6cbaf0a8a34f442c55b386f150e018
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543776"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure per VM Red Hat Enterprise Linux su richiesta in Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) consente ai provider di servizi cloud (come Azure) di eseguire il mirroring del contenuto dei repository ospitati in Red Hat, creare repository personalizzati con contenuto specifico di Azure e garantirne la disponibilità per le VM degli utenti finali.

Le immagini di Red Hat Enterprise Linux (RHEL) con pagamento in base al consumo sono preconfigurate per accedere all'infrastruttura RHUI di Azure. Non è necessaria alcuna configurazione aggiuntiva. Per ottenere gli aggiornamenti più recenti, eseguire `sudo yum update` quando l'istanza di RHEL è pronta. Questo servizio è incluso nelle tariffe del software RHEL con pagamento in base al consumo.

Altre informazioni sulle immagini RHEL in Azure, inclusi i criteri di pubblicazione e conservazione, sono disponibili [qui](./rhel-images.md).

Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).

## <a name="important-information-about-azure-rhui"></a>Informazioni importanti su Azure RHUI

* Azure RHUI è l'infrastruttura di aggiornamento che supporta tutte le VM PAYG RHEL create in Azure. Questa operazione non impedisce la registrazione delle macchine virtuali PAYG RHEL con Gestione sottoscrizioni o satellite o un'altra origine di aggiornamenti, ma con una macchina virtuale PAYG verrà generata una doppia fatturazione indiretta. Per informazioni dettagliate, vedere il punto seguente.
* L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. Annullando la registrazione di una VM RHEL con pagamento in base al consumo nell'istanza di RHUI ospitata in Azure, la macchina virtuale non viene convertita in una VM di tipo BYOL (Bring Your Own License). Se si registra la stessa VM in un'altra origine di aggiornamenti, potrebbero essere applicati addebiti doppi _indiretti_: un primo addebito per la tariffa del software RHEL di Azure e un secondo per le sottoscrizioni di Red Hat acquistate in precedenza. Se è necessario usare un'infrastruttura di aggiornamento diversa da quella di RHUI ospitata in Azure, prendere in considerazione la registrazione per usare le [Immagini RHEL BYOS](https://aka.ms/rhel-byos).

* Immagini RHEL SAP con pagamento in base al consumo (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) sono connesse a canali RHUI dedicati che vengono mantenuti nella versione secondaria di RHEL specificata come richiesto per la certificazione SAP.

* L'accesso all'istanza di RHUI ospitata in Azure è limitato alle VM incluse negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se si esegue l'inoltro tramite proxy di tutto il traffico delle VM tramite un'infrastruttura di rete locale, potrebbe essere necessario configurare route definite dall'utente per consentire alle VM RHEL con pagamento in base al consumo di accedere all'infrastruttura RHUI di Azure. In tal caso, le route definite dall'utente dovranno essere aggiunte per _tutti_ gli indirizzi IP di RHUI.

## <a name="image-update-behavior"></a>Comportamento aggiornamento immagine

A partire dal 2019 aprile, Azure offre immagini RHEL connesse a repository EUS (Extended Update Support) per impostazione predefinita e immagini RHEL connesse ai repository regolari (non EUS) per impostazione predefinita. Altre informazioni su RHEL EUS sono disponibili nella [documentazione del ciclo](https://access.redhat.com/support/policy/updates/errata) di vita della versione di Red Hat e nella [documentazione di EUS](https://access.redhat.com/articles/rhel-eus). Il comportamento predefinito di `sudo yum update` varia a seconda dell'immagine RHEL da cui è stato effettuato il provisioning, in quanto le diverse immagini sono connesse a repository diversi.

Per un elenco completo di immagini, `az vm image list --publisher redhat --all` eseguire usando l'interfaccia della riga di comando di Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Immagini connesse a repository non EUS

Se si esegue il provisioning di una macchina virtuale da un'immagine RHEL connessa a repository non EUS, verrà eseguito l'aggiornamento alla versione secondaria di RHEL più recente `sudo yum update`quando si esegue. Ad esempio, se si esegue il provisioning di una macchina virtuale da un' `sudo yum update`immagine RHEL 7,4 PAYG e si esegue, si finisce con una VM RHEL 7,7 (la versione secondaria più recente della famiglia RHEL7).

Le immagini connesse a repository non EUS non conterranno un numero di versione secondario nello SKU. Lo SKU è il terzo elemento dell'URN (nome completo dell'immagine). Ad esempio, tutte le immagini seguenti vengono associate a repository non EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Si noti che gli SKU sono 7-LVM o 7-RAW. La versione secondaria è indicata nella versione (quarto elemento dell'URN) di queste immagini.

### <a name="images-connected-to-eus-repositories"></a>Immagini connesse a repository EUS

Se si esegue il provisioning di una macchina virtuale da un'immagine RHEL connessa a repository EUS, non verrà eseguito l'aggiornamento alla versione secondaria di RHEL più `sudo yum update`recente quando si esegue. Questo perché le immagini connesse ai repository EUS sono anche bloccate alla versione secondaria specifica.

Le immagini connesse ai repository EUS conterranno un numero di versione secondario nello SKU. Ad esempio, tutte le immagini seguenti vengono associate ai repository EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e macchine virtuali RHEL con blocco della versione

Alcuni clienti potrebbero voler bloccare le VM RHEL in una determinata versione secondaria di RHEL dopo il provisioning della macchina virtuale. È possibile bloccare la versione di una macchina virtuale RHEL su una specifica versione secondaria aggiornando il repository in modo che faccia riferimento ai repository del supporto di aggiornamento esteso. È anche possibile annullare l'operazione di blocco della versione di EUS.

>[!NOTE]
> EUS non è supportato in extra RHEL. Ciò significa che se si installa un pacchetto che in genere è disponibile dal canale extra RHEL, non sarà possibile eseguire questa operazione in EUS. Il ciclo di vita del prodotto Red Hat extra è descritto [qui](https://access.redhat.com/support/policy/updates/extras/).

Al momento della stesura di questo articolo, il supporto per EUS è terminato per RHEL < = 7,3. Per altri dettagli, vedere la sezione relativa ai componenti aggiuntivi per il supporto di Red Hat Enterprise Linux più lunghi nella [documentazione di Red Hat](https://access.redhat.com/support/policy/updates/errata/) .
* Il supporto per RHEL 7,4 EUS termina il 31 agosto 2019
* Il supporto per RHEL 7,5 EUS termina il 30 aprile 2020
* Il supporto per RHEL 7,6 EUS termina il 31 ottobre 2020
* Il supporto per RHEL 7,7 EUS termina il 30 agosto 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Passare una macchina virtuale RHEL a EUS (blocco della versione a una versione secondaria specifica)
Usare le istruzioni seguenti per bloccare una macchina virtuale RHEL a una versione secondaria specifica (Esegui come radice):

>[!NOTE]
> Ciò vale solo per le versioni RHEL per le quali è disponibile EUS. Al momento della stesura di questo articolo, è incluso RHEL 7.2-7,7. Altri dettagli sono disponibili nella pagina [ciclo di vita di Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

1. Disabilitare i repository non EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Aggiungere i repository EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Blocca la variabile releasever (Esegui come radice):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Le istruzioni precedenti consentono di bloccare la versione secondaria di RHEL sulla versione secondaria corrente. Se si intende eseguire un aggiornamento, immettere una versione secondaria specifica e bloccare la macchina virtuale su una versione secondaria successiva che non sia la più recente. `echo 7.5 > /etc/yum/vars/releasever`, ad esempio, bloccherà la versione di RHEL su RHEL 7.5

1. Aggiornare la macchina virtuale RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Ripristinare una macchina virtuale RHEL a un blocco non EUS (rimuovere un blocco di versione)
Eseguire il comando seguente come radice:
1. Rimuovere il file releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Disabilitare i repository EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Configurare la macchina virtuale RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```
    
1. Aggiornare la macchina virtuale RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Gli indirizzi IP per i server di distribuzione di contenuti RHUI

Il servizio RHUI è disponibile in tutte le aree in cui sono disponibili le immagini di RHEL su richiesta. Include attualmente tutte le aree pubbliche elencate nella pagina del [dashboard di stato di Azure](https://azure.microsoft.com/status/) e le aree Azure US Government e Microsoft Azure Germania.

Se si usa una configurazione di rete per limitare ulteriormente l'accesso da VM RHEL con pagamento in base al consumo, per il funzionamento di `yum update` verificare che siano consentiti gli indirizzi IP seguenti, a seconda dell'ambiente in cui ci si trova:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infrastruttura RHUI di Azure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aggiornare il certificato client RHUI scaduto in una macchina virtuale

Se si usa un'immagine di macchina virtuale RHEL di una versione precedente, ad esempio RHEL 7.4 (URN immagine: `RedHat:RHEL:7.4:7.4.2018010506`), si riscontreranno problemi di connettività a RHUI dovuti a un certificato client SSL ormai scaduto. Verrà visualizzato un errore simile al seguente: _"Il peer SSL ha rifiutato il certificato considerandolo scaduto"_ oppure _Errore: impossibile recuperare i metadati del repository (repomd.xml) per il repository:... Si prega di verificare il suo percorso e riprovare"_ . Per risolvere il problema, aggiornare il pacchetto client RHUI nella macchina virtuale usando il comando seguente:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

In alternativa, anche l'esecuzione di `sudo yum update` potrebbe aggiornare il pacchetto del certificato client (a seconda della versione RHEL), nonostante gli errori di "certificato SSL scaduto" visualizzati per altri repository. Se l'aggiornamento ha esito positivo, dovrebbe essere ripristinata la normale connettività agli altri repository RHUI e sarà possibile eseguire `sudo yum update`.

Se si esegue un errore 404 durante l'esecuzione di `yum update`un, provare a eseguire le operazioni seguenti per aggiornare la cache yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Risoluzione dei problemi di connessione all'infrastruttura RHUI di Azure
Se si verificano problemi di connessione all'infrastruttura RHUI di Azure da una VM RHEL di Azure con pagamento in base al consumo, seguire questa procedura:

1. Individuare nella configurazione della VM un endpoint dell'infrastruttura RHUI di Azure:

    1. Controllare se la sezione `[rhui-microsoft-azure-rhel*]` del file `/etc/yum.repos.d/rh-cloud.repo` contiene un riferimento a `rhui-[1-3].microsoft.com` in `baseurl`. In questo caso si sta usando la nuova infrastruttura RHUI di Azure.

    1. Se punta a un percorso con il modello `mirrorlist.*cds[1-4].cloudapp.net`, è necessario un aggiornamento della configurazione. Se si sta usando uno snapshot di VM precedente, è necessario aggiornarlo in modo che punti alla nuova infrastruttura RHUI di Azure.

1. L'accesso all'istanza di RHUI ospitata in Azure è limitato alle macchine virtuali incluse negli [intervalli di indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Se si sta usando la nuova configurazione ed è stato verificato che la VM si connette dall'intervallo IP di Azure ma non è comunque possibile connettersi all'infrastruttura RHUI di Azure, richiedere supporto tecnico a Microsoft o Red Hat.

### <a name="infrastructure-update"></a>Aggiornamento dell'infrastruttura

Nel mese di settembre 2016 è stata distribuita un'infrastruttura RHUI di Azure aggiornata e nel mese di aprile 2017 è stata arrestata quella precedente. Se si usano immagini RHEL con pagamento in base al consumo (o i relativi snapshot) da settembre 2016 o da un momento successivo, si viene connessi automaticamente alla nuova infrastruttura RHUI di Azure. Se nelle VM sono presenti snapshot precedenti, invece, per accedere all'infrastruttura RHUI di Azure è necessario aggiornarne manualmente la configurazione come descritto in una sezione successiva.

I nuovi server RHUI di Azure vengono distribuiti con [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/). In Gestione traffico, un singolo endpoint (rhui-1.micrsoft.com) può essere usato da qualsiasi VM, indipendentemente dall'area.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura di aggiornamento manuale per l'uso dei server RHUI di Azure
Questa procedura viene illustrata solo per riferimento. Le immagini RHEL con pagamento in base al consumo hanno già la configurazione corretta per connettersi all'infrastruttura RHUI di Azure. Per aggiornare manualmente la configurazione per l'uso dei server RHUI di Azure, seguire questa procedura:

- Per RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Per RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Passaggi successivi
* Per creare una VM Red Hat Enterprise Linux da un'immagine con pagamento in base al consumo di Azure Marketplace e usare l'istanza di RHUI ospitata in Azure, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Per altre informazioni sulle immagini di Red Hat in Azure, vedere la [pagina della documentazione](./rhel-images.md).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).
