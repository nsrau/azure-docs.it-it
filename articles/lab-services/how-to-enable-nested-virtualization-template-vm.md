---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services | Microsoft Docs
description: In questo articolo viene illustrato come configurare la virtualizzazione annidata in un computer modello in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251475"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services

Attualmente, Azure Lab Services consente di configurare una sola macchina virtuale modello in un lab e di rendere disponibile una singola copia a ogni utente. Per i docenti che insegnano lezioni di rete, sicurezza o IT, potrebbe essere necessario fornire a ognuno degli studenti un ambiente in cui più macchine virtuali possono comunicare tra loro in una rete.

La virtualizzazione annidata consente di creare un ambiente con più macchine virtuali all'interno della macchina virtuale modello di un lab. La pubblicazione del modello fornirà a ogni utente del lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Questo articolo illustra come configurare la virtualizzazione annidata in una macchina modello in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Cos'è la virtualizzazione annidata?

La virtualizzazione annidata consente di creare macchine virtuali all'interno di una macchina virtuale. La virtualizzazione annidata viene eseguita tramite Hyper-V ed è disponibile solo nelle macchine virtuali Windows.

Per altre informazioni sulla virtualizzazione annidata, vedere gli articoli seguenti:

- [Virtualizzazione annidata in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerazioni

Prima di configurare un lab con la virtualizzazione annidata, prendere in considerazione gli aspetti seguenti.

- Quando si crea un nuovo lab, selezionare le dimensioni **Medium (virtualizzazione annidata)** o **Large (virtualizzazione annidata)** per la macchina virtuale. Queste dimensioni della macchina virtuale supportano la virtualizzazione annidata.
- Scegliere le dimensioni che forniranno prestazioni ottimali per le macchine virtuali host e client.  Tenere presente che quando si usa la virtualizzazione, le dimensioni scelte devono essere adeguate non solo per una macchina, ma anche per l'host e per tutte le macchine Hyper-V in esecuzione contemporaneamente.
- Le macchine virtuali client non avranno accesso alle risorse di Azure, come i server DNS, nella rete virtuale di Azure.
- La macchina virtuale host deve essere configurata in modo da consentire alla macchina client di avere la connettività Internet.
- Le macchine virtuali client sono concesse in licenza come macchine indipendenti. Per informazioni sulle licenze per i prodotti e i sistemi operativi Microsoft, vedere [Licenze Microsoft](https://www.microsoft.com/licensing/default). Controllare i contratti di licenza per qualsiasi altro software usato prima di configurare la macchina modello.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Abilitare la virtualizzazione annidata in una VM modello

In questo articolo si presuppone che siano già stati creati un account lab e un lab.  Per altre informazioni sulla creazione di un nuovo account lab, vedere l'[esercitazione per configurare un account lab](tutorial-setup-lab-account.md). Per altre informazioni su come creare un lab, vedere l'[esercitazione per configurare un lab per le classi](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Per la creazione del lab, selezionare le dimensioni **Medium (virtualizzazione annidata)** o **Large (virtualizzazione annidata)** per la macchina virtuale.  In caso contrario, la virtualizzazione annidata non funzionerà.  

Per connettersi alla macchina modello, vedere [Creare e gestire un modello per le classi](how-to-create-manage-template.md).

Per abilitare la virtualizzazione annidata, è necessario eseguire alcune attività.  

- **Abilitare il ruolo Hyper-V**. Il ruolo Hyper-V deve essere abilitato per la creazione e l'esecuzione di macchine virtuali Hyper-V nella macchina virtuale di Lab Services.
- **Abilitare DHCP**.  Quando per la macchina virtuale di Lab Services è abilitato il ruolo DHCP, alle macchine virtuali Hyper-V è possibile assegnare automaticamente un indirizzo IP.
- **Creare una rete NAT per macchine virtuali Hyper-V**.  La rete NAT è configurata in modo da consentire alle macchine virtuali Hyper-V l'accesso a Internet.  Le macchine virtuali Hyper-V possono comunicare tra loro.

>[!NOTE]
>La rete NAT creata nella macchina virtuale di Lab Services consentirà a una macchina virtuale Hyper-V di accedere a Internet e alle altre macchine virtuali Hyper-V presenti nella stessa macchina virtuale di Lab Services.  La macchina virtuale Hyper-V non potrà accedere alle risorse di Azure, come i server DNS, nella rete virtuale di Azure.

L'esecuzione delle attività elencate sopra può essere eseguita tramite uno script o usando gli strumenti di Windows.  Per altre informazioni, leggere le sezioni seguenti.

### <a name="using-script-to-enable-nested-virtualization"></a>Uso di uno script per abilitare la virtualizzazione annidata

Per usare la configurazione automatica per la virtualizzazione annidata con Windows Server 2016 o Windows Server 2019, vedere [Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services con uno script](how-to-enable-nested-virtualization-template-vm-using-script.md). Per installare il ruolo Hyper-V verranno usati gli script di [Script Hyper-V di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).  Gli script configureranno la rete anche in modo che le macchine virtuali Hyper-V possano avere accesso a Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Uso degli strumenti Windows per abilitare la virtualizzazione annidata

Per configurare la virtualizzazione annidata per Windows Server 2016 o Windows Server 2019 usando i ruoli e gli strumenti amministrativi di Windows, vedere [Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  Le istruzioni illustreranno anche come configurare la rete in modo che le macchine virtuali Hyper-V possano avere accesso a Internet.
