---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare una VM modello con più macchine virtuali all'interno di.  In altre parole, abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 3c954c4689281838ea8c61c932cdcc3b74bac442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184674"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services

Attualmente, Azure Lab Services consente di configurare una macchina virtuale modello in un Lab e di rendere disponibile una singola copia per ogni utente. Se sei un professore che insegna la rete, la sicurezza o le classi IT, potrebbe essere necessario fornire a ognuno degli studenti un ambiente in cui più macchine virtuali possono comunicare tra loro in una rete.

La virtualizzazione nidificata consente di creare un ambiente con più VM all'interno della macchina virtuale modello di un Lab. La pubblicazione del modello fornirà a ogni utente del Lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Questo articolo illustra come configurare la virtualizzazione annidata in un computer modello in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Che cos'è la virtualizzazione nidificata?

La virtualizzazione nidificata consente di creare macchine virtuali all'interno di una macchina virtuale. La virtualizzazione nidificata viene eseguita tramite Hyper-V ed è disponibile solo nelle macchine virtuali Windows.

Per ulteriori informazioni sulla virtualizzazione nidificata, vedere gli articoli seguenti:

- [Virtualizzazione annidata in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerazioni

Prima di configurare un Lab con la virtualizzazione nidificata, ecco alcuni aspetti da prendere in considerazione.

- Quando si crea un nuovo Lab, selezionare dimensioni **medie (virtualizzazione annidata)** o **grandi (virtualizzazione nidificata)** per le dimensioni della macchina virtuale. Queste dimensioni della macchina virtuale supportano la virtualizzazione nidificata.
- Scegliere le dimensioni che forniranno prestazioni ottimali per le macchine virtuali host e client.  Tenere presente che quando si usa la virtualizzazione, le dimensioni scelte devono essere adeguate solo per un computer, ma anche per l'host e per qualsiasi computer Hyper-V in esecuzione simultanea.
- Le macchine virtuali client non avranno accesso alle risorse di Azure, ad esempio i server DNS, nella rete virtuale di Azure.
- Per la macchina virtuale host è necessaria la configurazione per consentire al computer client di disporre della connettività Internet.
- Le macchine virtuali client sono concesse in licenza come computer indipendenti. Per informazioni sulle licenze per i sistemi operativi e i prodotti Microsoft, vedere [Microsoft Licensing](https://www.microsoft.com/licensing/default) . Controllare i contratti di licenza per qualsiasi altro software usato prima di configurare il computer modello.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Abilitare la virtualizzazione annidata in una VM modello

Questo articolo presuppone che siano stati creati un account Lab e un Lab.  Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md). Per altre informazioni su come creare un Lab, vedere la pagina relativa alla [configurazione di un'esercitazione in aula Lab](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Selezionare **grande (virtualizzazione annidata)** o **media (virtualizzazione annidata)** per le dimensioni della macchina virtuale durante la creazione del Lab.  La virtualizzazione nidificata non funzionerà in caso contrario.  

Per connettersi al computer modello, vedere [creare e gestire un modello di classe](how-to-create-manage-template.md).

Per abilitare la virtualizzazione annidata, è necessario eseguire alcune attività.  

- **Abilitare il ruolo Hyper-V**. Il ruolo Hyper-V deve essere abilitato per la creazione e l'esecuzione di macchine virtuali Hyper-V nella macchina virtuale di Lab Services.
- **Abilitare DHCP**.  Quando per la macchina virtuale di Lab Services è abilitato il ruolo DHCP, alle macchine virtuali Hyper-V è possibile assegnare automaticamente un indirizzo IP.
- **Creare una rete NAT per le macchine virtuali Hyper-V**.  La rete NAT è configurata in modo da consentire l'accesso a Internet alle macchine virtuali Hyper-V.  Le macchine virtuali Hyper-V possono comunicare tra loro.

>[!NOTE]
>La rete NAT creata nella macchina virtuale di Lab Services consentirà a una macchina virtuale Hyper-V di accedere a Internet e ad altre macchine virtuali Hyper-V nella stessa macchina virtuale di Lab Services.  La VM Hyper-V non sarà in grado di accedere alle risorse di Azure, ad esempio i server DNS, nella rete virtuale di Azure.

L'esecuzione delle attività elencate in precedenza può essere eseguita tramite uno script o utilizzando gli strumenti di Windows.  Per ulteriori informazioni, leggere le sezioni seguenti.

### <a name="using-script-to-enable-nested-virtualization"></a>Uso dello script per abilitare la virtualizzazione annidata

Per usare la configurazione automatica per la virtualizzazione nidificata con Windows Server 2016 o Windows Server 2019, vedere [abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services usando uno script](how-to-enable-nested-virtualization-template-vm-using-script.md). Per installare il ruolo Hyper-V, si useranno gli script degli [script Hyper-v di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) .  Gli script configureranno anche la rete in modo che le macchine virtuali Hyper-V possano avere accesso a Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Uso degli strumenti di Windows per abilitare la virtualizzazione annidata

Per configurare la virtualizzazione annidata per Windows Server 2016 o Windows Server 2019 con i ruoli e gli strumenti di amministrazione di Windows, vedere [abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  Vengono inoltre illustrate le procedure per configurare la rete in modo che le macchine virtuali Hyper-V possano avere accesso a Internet.
