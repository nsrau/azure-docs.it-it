---
title: Soluzione Azure VMware di CloudSimple - Usare il cloud privato come sito di emergenza per i carichi di lavoro localiAzure VMware Solution by CloudSimple - Use Private Cloud as disaster site for on-premises workloads
description: Descrive come configurare il cloud privato CloudSimple come sito di ripristino di emergenza per i carichi di lavoro VMware locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083140"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configurare CloudSimple Private Cloud come sito di ripristino di emergenza per i carichi di lavoro VMware localiSet up CloudSimple Private Cloud as a disaster recovery site for on-premises VMware workloads

Il cloud privato CloudSimple può essere configurato come sito di ripristino per le applicazioni locali per garantire la continuità aziendale in caso di emergenza. La soluzione di ripristino si basa sulla replica virtuale zerto come piattaforma di replica e orchestrazione. L'infrastruttura critica e le macchine virtuali delle applicazioni possono essere replicate continuamente dal vCenter locale al cloud privato. È possibile usare il cloud privato per i test di failover e per garantire la disponibilità dell'applicazione durante un'emergenza. Un approccio simile può essere seguito per configurare il cloud privato come sito primario protetto da un sito di ripristino in una posizione diversa.

> [!NOTE]
> Per informazioni su come eseguire il dimensionamento dell'ambiente di ripristino di emergenza, fare riferimento al documento [di considerazioni sul ridimensionamento della](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) replica virtuale .

La soluzione CloudSimple:

* Elimina la necessità di configurare un data center specifico per il ripristino di emergenza.
* Consente di sfruttare le posizioni di Azure in cui CloudSimple viene distribuito per la resilienza geografica in tutto il mondo.
* Offre un'opzione per ridurre i costi di distribuzione e il costo totale di proprietà per il ripristino di emergenza.

La soluzione richiede di:

* Installa, configura e gestisci il tuo cloud privato.
* Fornire le proprie licenze per il sito protetto di .erato quando il cloud privato. È possibile associare il sito CloudSimple al sito locale con il sito locale per la concessione di licenze.

Nella figura seguente viene illustrata l'architettura per la soluzione .

![Architecture](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Come distribuire la soluzione

Nelle sezioni seguenti viene descritto come distribuire una soluzione di ripristino di emergenza usando la replica virtuale zerto nel cloud privato.

1. [Prerequisiti](#prerequisites)
2. [Configurazione opzionale su CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [Configurare il cloud privato semplice e VM e VRA su Cloud](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Configurazione del gruppo protezione virtuale zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Prerequisiti

Per abilitare la replica virtuale di .erato dall'ambiente locale al cloud privato, completare i prerequisiti seguenti.

1. [Configurare una connessione VPN da sito a sito tra la rete locale e cloud cloud privato CloudSimple](set-up-vpn.md).
2. [Configurare la ricerca DNS in modo che i componenti di gestione del cloud privato vengano inoltrati ai server DNS private cloud.](on-premises-dns-setup.md)  Per abilitare l'inoltro della ricerca DNS, creare una `*.cloudsimple.io` voce di zona di inoltro nel server DNS locale per ai server DNS CloudSimple.
3. Configurare la ricerca DNS in modo che i componenti vCenter locali vengano inoltrati ai server DNS locali.  I server DNS devono essere raggiungibili dal cloud cloud privato CloudSimple tramite VPN da sito a sito. Per assistenza, inviare una richiesta di [supporto,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornendo le seguenti informazioni.  

    * Nome di dominio DNS locale
    * Indirizzi IP del server DNS locale

4. Installare un server Windows nel cloud privato. Il server viene utilizzato per l'installazione di .erto Virtual Manager.
5. [Inoltrare i privilegi CloudSimple](escalate-private-cloud-privileges.md).
6. Creare un nuovo utente nel centro commerciale Private Cloud con il ruolo amministrativo da utilizzare come account di servizio per Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Configurazione opzionale sul tuo cloud privato

1. Creare uno o più pool di risorse nel vCenter del cloud privato da usare come pool di risorse di destinazione per le macchine virtuali dall'ambiente locale.
2. Creare una o più cartelle nel centro commerciale privato da usare come cartelle di destinazione per le macchine virtuali dall'ambiente locale.
3. Creare VLAN per la rete di failover e configurare le regole del firewall. Aprire una [richiesta di assistenza.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
4. Creare gruppi di porte distribuite per la rete di failover e testare la rete per testare il failover delle macchine virtuali.
5. Installare [server DHCP e DNS](dns-dhcp-setup.md) o utilizzare un controller di dominio Active Directory nell'ambiente Private Cloud.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Configura il tuo cloud privato

1. Installare la di gestione virtuale di .erto sul server Windows nel cloud privato.
2. Eseguire l'accesso a : VM utilizzando l'account del servizio creato nei passaggi precedenti.
3. Impostare le licenze per il Virtual Manager di .erto.
4. Installare l'appliance di replica virtuale (VRA) di .erto sugli host ESXi del cloud privato.
5. Abbina il tuo Cloud Privato con la tua macchina virtuale in locale.

### <a name="set-up-zerto-virtual-protection-group"></a>Configurazione del gruppo protezione virtuale zerto

1. Creare un nuovo gruppo di protezione virtuale (VPG) e specificare la priorità per il VPG.
2. Selezionare le macchine virtuali che richiedono protezione per la continuità aziendale e personalizzare l'ordine di avvio, se necessario.
3. Selezionare il sito di ripristino come cloud privato e il server di ripristino predefinito come cluster Private Cloud o il gruppo di risorse creato. Selezionare **vsanDatastore** per l'archivio dati di ripristino nel cloud privato.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > È possibile personalizzare l'opzione host per le singole macchine virtuali nell'opzione Impostazioni macchina virtuale.

4. Personalizzare le opzioni di archiviazione in base alle esigenze.
5. Specificare le reti di ripristino da utilizzare per la rete di failover e la rete di test del failover come gruppi di porte distribuite creati in precedenza e personalizzare gli script di ripristino in base alle esigenze.
6. Personalizzare le impostazioni di rete per le singole macchine virtuali, se necessario, e creare il VPG.
7. Testare il failover al termine della replica.

## <a name="reference"></a>Riferimento

[Documentazione di zerto](https://www.zerto.com/myzerto/technical-documentation/)
