---
title: Panoramica della creazione di immagini Linux per Azure
description: Panoramica della procedura per importare le immagini di VM Linux o crearne di nuove da usare in Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: aa372d4e1b377ecdcbeb49b47f0f9a3a217ee7ad
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502181"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Importazione e creazione di immagini Linux in Azure

Questa panoramica illustra i concetti di base relativi alla creazione di immagini e spiega come creare e usare correttamente le immagini Linux in Azure. Prima di importare un'immagine personalizzata in Azure, è necessario conoscere i tipi e le opzioni disponibili.

Questo articolo illustra i requisiti e i punti decisionali per le immagini, oltre ai concetti chiave e alla procedura per creare immagini personalizzate in base alle proprie specifiche.

## <a name="difference-between-managed-disks-and-images"></a>Differenza tra dischi gestiti e immagini


Azure consente di inserire un disco rigido virtuale nella piattaforma per usarlo come [disco gestito](../windows/faq-for-disks.md#managed-disks) o come origine per un'immagine. 

I dischi gestiti di Azure sono singoli dischi rigidi virtuali. È possibile creare un disco gestito da un disco rigido virtuale esistente oppure crearne uno vuoto da zero. È possibile creare VM da dischi gestiti collegando il disco alla VM, ma è possibile usare un disco rigido virtuale solo con una singola VM. Non è possibile modificare le proprietà del sistema operativo. Azure proverà semplicemente ad attivare la VM e a iniziare a usare il disco. 

Le immagini di Azure possono essere costituite da più dischi del sistema operativo e dischi dati. Quando si usa un'immagine gestita per creare una macchina virtuale, la piattaforma ne crea una copia e la usa per creare la VM, quindi è possibile riutilizzare la stessa immagine gestita per più VM. Azure include anche funzionalità di gestione avanzate per le immagini, ad esempio la replica globale e il controllo delle versioni tramite [Raccolta immagini condivise](shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Immagini generalizzate e specializzate

Azure offre due tipi principali di immagine, ovvero generalizzata e specializzata. I termini generalizzata e specializzata sono originariamente di Windows, trasferiti in Azure. Questi tipi definiscono il modo in cui la piattaforma gestirà la macchina virtuale quando la attiva. Entrambi i tipi presentano vantaggi, svantaggi e prerequisiti. Prima di iniziare, è necessario conoscere il tipo di immagine necessario. Di seguito sono riepilogati gli scenari e i tipi tra cui scegliere:

| Scenario      | Tipo di immagine  | Opzioni di archiviazione |
| ------------- |:-------------:| :-------------:| 
| Creare un'immagine che può essere configurata per l'uso da parte di più macchine virtuali e che consente di impostare il nome host, aggiungere un utente amministratore ed eseguire altre attività durante il primo avvio. | Generalizzata | Raccolta immagini condivise o immagini gestite autonome |
| Creare un'immagine da uno snapshot della macchina virtuale o da un backup | Specializzata |Raccolta immagini condivise o disco gestito |
| Creare rapidamente un'immagine che non necessita di alcuna configurazione per la creazione di più macchine virtuali |Specializzata |Raccolta di immagini condivise |


### <a name="generalized-images"></a>Immagini generalizzate

Un'immagine generalizzata richiede il completamento della configurazione al primo avvio. Ad esempio, al primo avvio è possibile impostare il nome host, l'utente amministratore e altre configurazioni specifiche della macchina virtuale. Questa opzione è utile se si vuole riutilizzare l'immagine più volte e passare i parametri durante la creazione. Se l'immagine generalizzata lo contiene, l'agente di Azure elaborerà i parametri e segnalerà alla piattaforma che la configurazione iniziale è stata completata. Questo processo è detto **provisioning**. 

Il provisioning richiede l'inclusione di uno strumento di provisioning nell'immagine. Sono disponibili due strumenti di provisioning:
- [Agente Linux di Azure](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

Sono [prerequisiti](./create-upload-generic.md) per la creazione di un'immagine.


### <a name="specialized-images"></a>Immagini specializzate
Si tratta di immagini completamente configurate che non richiedono parametri di VM e speciali. La piattaforma attiva la VM ed è necessario gestire l'univocità al suo interno, ad esempio impostando un nome host, per evitare conflitti di DNS nella stessa rete virtuale. 

Gli agenti di provisioning non sono necessari per queste immagini, ma si può scegliere di avere funzionalità di gestione delle estensioni. È possibile installare l'agente Linux, ma disabilitare l'opzione di provisioning. Anche se un agente di provisioning non è necessario, l'immagine deve soddisfare i [prerequisiti](./create-upload-generic.md) per le immagini di Azure.


## <a name="image-storage-options"></a>Opzioni di archiviazione delle immagini
Quando si importa l'immagine Linux sono disponibili due opzioni:

- Immagini gestite per la semplice creazione di in un ambiente di sviluppo e test.
- [Raccolta immagini condivise](shared-image-galleries.md) per la creazione e la condivisione di immagini su larga scala.


### <a name="managed-images"></a>Immagini gestite

Le immagini gestite possono essere usate per creare più macchine virtuali, ma presentano numerose limitazioni. Le immagini gestite possono essere create solo da un'origine generalizzata (VM o disco rigido virtuale). Possono essere usate solo per creare macchine virtuali nella stessa area e non possono essere condivise tra sottoscrizioni e tenant.

Le immagini gestite possono essere usate per ambienti di sviluppo e test, in cui sono necessarie due semplici immagini generalizzate da usare all'interno di una singola area e una sottoscrizione. 

### <a name="azure-shared-image-gallery-sig"></a>Raccolta immagini condivise di Azure

Le [raccolte immagini condivise](shared-image-galleries.md) sono consigliate per la creazione, la gestione e la condivisione di immagini su larga scala. Consentono di creare una struttura e un'organizzazione intorno alle immagini.  

- Supporto sia di immagini generalizzate che specializzate.
- Supporto di immagini delle generazioni 1 e 2.
- Replica globale delle immagini.
- Controllo delle versioni e raggruppamento delle immagini per la semplificazione della gestione.
- Immagini a disponibilità elevata con archiviazione con ridondanza della zona (ZRS) in aree che supportano le zone di disponibilità. Le ZRS offrono una migliore resilienza nei confronti degli errori di zona.
- Condivisione tra sottoscrizioni e persino tra tenant Active Directory, con il Controllo degli accessi in base al ruolo.
- Ridimensionamento delle distribuzioni con repliche delle immagini in ogni area.

A livello generale, si crea una raccolta immagini condivise costituita da:
- Definizioni di immagine: contenitori per i gruppi di immagini.
- Versioni delle immagini: immagini effettive



## <a name="hyper-v-generation"></a>Generazione di Hyper-V

Azure supporta Hyper-V generazione 1 (Gen1) e generazione 2 (Gen2). Gen2 è la generazione più recente e offre funzionalità aggiuntive rispetto a Gen1, ad esempio una quantità maggiore di memoria, la tecnologia Intel Software Guard Extensions (Intel SGX) e la memoria persistente virtuale (vPMEM). Le macchine virtuali di seconda generazione eseguite in locale hanno alcune funzionalità che non sono ancora supportate in Azure. Per altre informazioni, vedere la sezione Funzionalità e caratteristiche. Per altre informazioni, vedere questo [articolo](../windows/generation-2.md). Creare immagini Gen2 se le funzionalità aggiuntive sono necessarie.

Se è comunque necessario creare un'immagine personalizzata, assicurarsi che soddisfi i [prerequisiti delle immagini](./create-upload-generic.md) e il caricamento in Azure. Requisiti specifici per la distribuzione:


- [Distribuzioni basate su CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare una [Raccolta immagini condivise](tutorial-custom-images.md).
