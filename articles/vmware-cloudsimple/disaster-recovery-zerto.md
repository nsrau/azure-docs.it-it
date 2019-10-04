---
title: 'Soluzione VMware di Azure di CloudSimple: usare il cloud privato come sito di emergenza per i carichi di lavoro locali'
description: Viene descritto come configurare il cloud privato CloudSimple come sito di ripristino di emergenza per i carichi di lavoro VMware locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037416"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configurare il cloud privato CloudSimple come sito di ripristino di emergenza per i carichi di lavoro VMware locali

Il cloud privato CloudSimple può essere configurato come sito di ripristino per le applicazioni locali per garantire la continuità aziendale in caso di emergenza. La soluzione di ripristino si basa sulla replica virtuale Zerto come piattaforma di replica e orchestrazione. Le macchine virtuali dell'infrastruttura e delle applicazioni critiche possono essere replicate continuamente dall'ambiente vCenter locale al cloud privato. È possibile usare il cloud privato per i test di failover e per garantire la disponibilità dell'applicazione durante un'emergenza. È possibile seguire un approccio simile per configurare il cloud privato come sito primario protetto da un sito di ripristino in una posizione diversa.

> [!NOTE]
> Per le linee guida sul dimensionamento dell'ambiente di ripristino di emergenza, vedere Considerazioni sul dimensionamento dei documenti Zerto [per la replica virtuale Zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) .

La soluzione CloudSimple:

* Elimina la necessità di configurare un Data Center in modo specifico per il ripristino di emergenza.
* Consente di sfruttare le località di Azure in cui viene distribuito CloudSimple per la resilienza geografica mondiale.
* Offre un'opzione per ridurre i costi di distribuzione e il costo totale di proprietà per il ripristino di emergenza.

Per la soluzione è necessario:

* Installare, configurare e gestire Zerto nel cloud privato.
* Fornire le proprie licenze per Zerto quando il cloud privato è il sito protetto. È possibile associare Zerto in esecuzione nel sito di CloudSimple con il sito locale per la gestione delle licenze.

Nella figura seguente viene illustrata l'architettura per la soluzione Zerto.

![Architettura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Come distribuire la soluzione

Le sezioni seguenti descrivono come distribuire una soluzione di ripristino di emergenza tramite la replica virtuale Zerto nel cloud privato.

1. [Prerequisiti](#prerequisites)
2. [Configurazione facoltativa nel cloud privato CloudSimple](#optional-configuration-on-your-private-cloud)
3. [Configurare ZVM e LRV nel cloud privato CloudSimple](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Configurare il gruppo di protezione virtuale Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Prerequisiti

Per abilitare la replica virtuale Zerto dall'ambiente locale al cloud privato, completare i prerequisiti seguenti.

1. [Configurare una connessione VPN da sito a sito tra la rete locale e il cloud privato CloudSimple](set-up-vpn.md).
2. [Configurare la ricerca DNS in modo che i componenti di gestione del cloud privato vengano trasmessi ai server DNS del cloud privato](on-premises-dns-setup.md).  Per abilitare l'invio della ricerca DNS, creare una voce di area di invio nel server DNS locale per `*.cloudsimple.io` i server DNS CloudSimple.
3. Configurare la ricerca DNS in modo che i componenti vCenter locali vengano trasmessi ai server DNS locali.  I server DNS devono essere raggiungibili dal cloud privato di CloudSimple tramite VPN da sito a sito. Per assistenza, inviare una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), fornendo le informazioni seguenti.  

    * Nome di dominio DNS locale
    * Indirizzi IP del server DNS locale

4. Installare un server Windows nel cloud privato. Il server viene usato per installare Zerto Virtual Manager.
5. [Inoltrare i privilegi di CloudSimple](escalate-private-cloud-privileges.md).
6. Creare un nuovo utente nel cloud privato vCenter con il ruolo amministrativo da usare come account del servizio per Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Configurazione facoltativa nel cloud privato

1. Creare uno o più pool di risorse nel cloud privato vCenter da usare come pool di risorse di destinazione per le macchine virtuali dall'ambiente locale.
2. Creare una o più cartelle nel cloud privato vCenter da usare come cartelle di destinazione per le macchine virtuali dall'ambiente locale.
3. Creare VLAN per la rete di failover e configurare le regole del firewall. Aprire una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per assistenza.
4. Creare gruppi di porte distribuiti per la rete di failover e la rete di test per il failover di test delle macchine virtuali.
5. Installare i [server DHCP e DNS](dns-dhcp-setup.md) oppure usare un Active Directory controller di dominio nell'ambiente cloud privato.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Configurare ZVM e LRV nel cloud privato

1. Installare Zerto Virtual Manager (ZVM) in Windows Server nel cloud privato.
2. Accedere a ZVM usando l'account del servizio creato nei passaggi precedenti.
3. Configurare la gestione delle licenze per Zerto Virtual Manager.
4. Installare Zerto Virtual Replication Appliance (LRV) negli host ESXi del cloud privato.
5. Associare il cloud privato ZVM al ZVM locale.

### <a name="set-up-zerto-virtual-protection-group"></a>Configurare il gruppo di protezione virtuale Zerto

1. Creare un nuovo gruppo protezione dati virtuale (VPG) e specificare la priorità per il VPG.
2. Selezionare le macchine virtuali che richiedono la protezione per la continuità aziendale e personalizzare l'ordine di avvio, se necessario.
3. Selezionare il sito di ripristino come cloud privato e il server di ripristino predefinito come cluster di cloud privato o il gruppo di risorse creato. Selezionare **vsanDatastore** per l'archivio dati di ripristino nel cloud privato.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > È possibile personalizzare l'opzione host per le singole macchine virtuali nell'opzione impostazioni macchina virtuale.

4. Personalizzare le opzioni di archiviazione come richiesto.
5. Specificare le reti di ripristino da utilizzare per la rete di failover e la rete di test di failover come gruppi di porte distribuiti creati in precedenza e personalizzare gli script di ripristino in modo obbligatorio.
6. Personalizzare le impostazioni di rete per le singole macchine virtuali, se necessario, e creare il VPG.
7. Test del failover dopo il completamento della replica.

## <a name="reference"></a>Riferimenti

[Documentazione di Zerto](https://www.zerto.com/myzerto/technical-documentation/)
