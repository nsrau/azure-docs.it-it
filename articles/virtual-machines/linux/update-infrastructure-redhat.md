---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Informazioni su Red Hat Update Infrastructure per istanze di Red Hat Enterprise Linux su richiesta in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 2add03d68b00fb18fb4323a6acd04480b3770c1c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708408"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure per VM Red Hat Enterprise Linux su richiesta in Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) consente ai provider di servizi cloud (come Azure) di eseguire il mirroring del contenuto dei repository ospitati in Red Hat, creare repository personalizzati con contenuto specifico di Azure e garantirne la disponibilità per le VM degli utenti finali.

Le immagini di Red Hat Enterprise Linux (RHEL) con pagamento in base al consumo sono preconfigurate per accedere all'infrastruttura RHUI di Azure. Non è necessaria alcuna configurazione aggiuntiva. Per ottenere gli aggiornamenti più recenti, eseguire `sudo yum update` quando l'istanza di RHEL è pronta. Questo servizio è incluso nelle tariffe del software RHEL con pagamento in base al consumo.

Altre informazioni sulle immagini RHEL in Azure, inclusi i criteri di pubblicazione e conservazione, sono disponibili [qui](./rhel-images.md).

Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).

## <a name="important-information-about-azure-rhui"></a>Informazioni importanti su Azure RHUI
* Infrastruttura RHUI di Azure è l'infrastruttura di aggiornamento che supporta tutte le macchine virtuali in base al consumo RHEL creato in Azure. Ciò non impedisce è dalla registrazione delle macchine virtuali RHEL in base al consumo con gestione della sottoscrizione o Satellite o un'altra origine degli aggiornamenti, ma questa operazione con una VM PAYG comporterà indiretta doppia fatturazione. Il punto di seguito per informazioni dettagliate, vedere.
* L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. Annullando la registrazione di una VM RHEL con pagamento in base al consumo nell'istanza di RHUI ospitata in Azure, la macchina virtuale non viene convertita in una VM di tipo BYOL (Bring Your Own License). Se si registra la stessa VM in un'altra origine di aggiornamenti, potrebbero essere applicati addebiti doppi _indiretti_: un primo addebito per la tariffa del software RHEL di Azure e un secondo per le sottoscrizioni di Red Hat acquistate in precedenza. Se in modo coerente, è necessario usare un'infrastruttura di aggiornamento diverso dal servizio RHUI ospitato in Azure, prendere in considerazione la registrazione per usare la [immagini BYOS RHEL](https://aka.ms/rhel-byos).
* Il comportamento predefinito dell'istanza di RHUI consiste nell'aggiornare la VM RHEL per la versione secondaria più recente quando si esegue `sudo yum update`.

    Se si effettua il provisioning di una macchina virtuale da un'immagine di RHEL 7.4 con pagamento in base al consumo e si esegue `sudo yum update`, ad esempio, verrà creata una VM RHEL 7.6 (ultima versione secondaria della famiglia RHEL7).

    Per evitare questo comportamento, è possibile passare a [canali di supporto "Extended" di aggiornamento](#rhel-eus-and-version-locking-rhel-vms) oppure creare un'immagine come descritto nel [creare e caricare una macchina virtuale basata su Red Hat per Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) articolo. Se si compila un'immagine personalizzata, è necessario connetterla a un'infrastruttura di aggiornamento diversi ([direttamente a Red Hat server per la distribuzione di contenuti](https://access.redhat.com/solutions/253273) o una [server Red Hat Satellite](https://access.redhat.com/products/red-hat-satellite)).



* Immagini RHEL SAP con pagamento in base al consumo (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) sono connesse a canali RHUI dedicati che vengono mantenuti nella versione secondaria di RHEL specificata come richiesto per la certificazione SAP.

* L'accesso all'istanza di RHUI ospitata in Azure è limitato alle VM incluse negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se si esegue l'inoltro tramite proxy di tutto il traffico delle VM tramite un'infrastruttura di rete locale, potrebbe essere necessario configurare route definite dall'utente per consentire alle VM RHEL con pagamento in base al consumo di accedere all'infrastruttura RHUI di Azure.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e macchine virtuali RHEL con blocco della versione
È possibile che un cliente voglia bloccare una macchina virtuale RHEL su una determinata versione secondaria di RHEL. È possibile bloccare la versione di una macchina virtuale RHEL su una specifica versione secondaria aggiornando il repository in modo che faccia riferimento ai repository del supporto di aggiornamento esteso. È anche possibile annullare l'operazione di blocco a livello di versione EUS.

>[!NOTE]
> EUS non è supportata nella funzionalità aggiuntive di RHEL. Ciò significa che se si installa un pacchetto che è in genere disponibile dal canale funzionalità aggiuntive di RHEL, non sarà in grado di eseguire questa operazione mentre è in EUS. È descritta in dettaglio il ciclo di vita del prodotto di Red Hat Extras [qui](https://access.redhat.com/support/policy/updates/extras/).

Al momento della stesura di questo articolo, il supporto EUS è terminato per RHEL < = 7.3. Vedere la sezione "Red Hat Enterprise Linux più supporto componenti aggiuntivi" nel [documentazione di Red Hat](https://access.redhat.com/support/policy/updates/errata/) per altri dettagli.
* RHEL 7.4 EUS il supporto termina il 31 agosto 2019
* RHEL 7.5 EUS il supporto termina il 30 aprile 2020
* RHEL 7.6 EUS il supporto termina il 31 ottobre 2020

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Passare a una VM RHEL per EUS (versione blocco a una specifica versione secondaria)
Usare le istruzioni seguenti per bloccare una VM RHEL per una particolare versione secondaria (eseguita come radice):

>[!NOTE]
> Ciò vale solo per le versioni RHEL per le quali è disponibile EUS. Al momento della stesura di questo articolo, sono inclusi RHEL 7.2 e 7.6. Altri dettagli sono disponibili nella pagina [ciclo di vita di Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

1. Disabilitare i repository non EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Aggiungere i repository EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloccare la variabile releasever (eseguita come radice):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Le istruzioni precedenti consentono di bloccare la versione secondaria di RHEL sulla versione secondaria corrente. Se si intende eseguire un aggiornamento, immettere una versione secondaria specifica e bloccare la macchina virtuale su una versione secondaria successiva che non sia la più recente. `echo 7.5 > /etc/yum/vars/releasever`, ad esempio, bloccherà la versione di RHEL su RHEL 7.5

1. Aggiornare la macchina virtuale RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Tornare a una VM RHEL a non-EUS (rimuovere un blocco di versione)
Eseguire il comando seguente come radice:
1. Rimuovere il file releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Disabilitare i repository EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
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

Se si verifica un errore 404 mentre è in esecuzione un `yum update`, ripetere il comando seguente per aggiornare la cache yum:
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
