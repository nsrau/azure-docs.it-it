---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services. Documenti Microsoft
description: Informazioni su come creare una macchina virtuale modello con più macchine virtuali all'interno.  In other words, enable nested virtualization on a template VM in Azure Lab Services.
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
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502017"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab ServicesEnable nested virtualization on a template virtual machine in Azure Lab Services

Attualmente, Azure Lab Services consente di configurare una macchina virtuale modello in un lab e rendere disponibile una singola copia per ogni utente. Se si è un professore che insegna a fare networking, sicurezza o corsi IT, potrebbe essere necessario fornire a ciascuno degli studenti un ambiente in cui più macchine virtuali possono comunicare tra loro tramite una rete.

La virtualizzazione annidata consente di creare un ambiente con più macchine virtuali all'interno della macchina virtuale modello di un lab. La pubblicazione del modello fornirà a ogni utente del lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Questo articolo illustra come configurare la virtualizzazione annidata in un computer modello in Azure Lab Services.This article covers how to set up nested virtualization on a template machine in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Che cos'è la virtualizzazione annidata?

La virtualizzazione annidata consente di creare macchine virtuali all'interno di una macchina virtuale. La virtualizzazione annidata viene eseguita tramite Hyper-V ed è disponibile solo nelle macchine virtuali Windows.Nested virtualization is done through Hyper-V, and is only available on Windows VMs.

Per altre informazioni sulla virtualizzazione annidata, vedere gli articoli seguenti:For more information about nested virtualization, see the following articles:

- [Virtualizzazione annidata in AzureNested Virtualization in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerazioni

Prima di configurare un lab con virtualizzazione annidata, ecco alcuni aspetti da prendere in considerazione.

- Quando si crea un nuovo lab, selezionare **Medie (virtualizzazione annidata)** o **Dimensioni grandi (virtualizzazione annidate)** per le dimensioni della macchina virtuale. Queste dimensioni di macchine virtuali supportano la virtualizzazione annidata.
- Scegliere una dimensione che fornisca buone prestazioni sia per le macchine virtuali host che per le macchine virtuali client.  Tenere presente che quando si utilizza la virtualizzazione, le dimensioni scelte devono essere adeguate non per un solo computer, ma per l'host e per tutti i computer client che devono essere eseguiti contemporaneamente.
- Le macchine virtuali client non avranno accesso alle risorse di Azure, ad esempio i server DNS nella rete virtuale di Azure.Client virtual machines will not have access to Azure resources, such as DNS servers on the Azure virtual network.
- La macchina virtuale host richiede il programma di installazione per consentire alla macchina client di disporre di connettività Internet.Host virtual machine requires setup to allow for the client machine to have internet connectivity.
- Le macchine virtuali client sono concesse in licenza come macchine indipendenti. Per informazioni sulle licenze per i prodotti e i sistemi operativi Microsoft, vedere [Licenze Microsoft.See Microsoft Licensing](https://www.microsoft.com/licensing/default) for information about licensing for Microsoft operation systems and products. Prima di configurare la macchina modello, verificare i contratti di licenza per qualsiasi altro software in uso.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Abilitare la virtualizzazione annidata in una VM modello

In questo articolo si presuppone che siano stati creati un account lab e un lab.  Per ulteriori informazioni sulla creazione di un nuovo account lab, vedere [esercitazione per configurare un account lab](tutorial-setup-lab-account.md). Per altre informazioni su come creare lab, vedere [Configurare un'esercitazione](tutorial-setup-classroom-lab.md)del laboratorio in classe.

>[!IMPORTANT]
>Selezionare **Grande (virtualizzazione annidata)** o **Medio (virtualizzazione annidata)** per le dimensioni della macchina virtuale durante la creazione del lab.  La virtualizzazione annidata non funzionerà in caso contrario.  

Per connettersi alla macchina modello, vedere [Creare e gestire un modello](how-to-create-manage-template.md)di classe .

### <a name="using-script-to-enable-nested-virtualization"></a>Utilizzo di script per abilitare la virtualizzazione annidataUsing script to enable nested virtualization

Per usare l'installazione automatica per la virtualizzazione annidata con Windows Server 2016 o Windows Server 2019, vedere [Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services usando uno script.](how-to-enable-nested-virtualization-template-vm-using-script.md) Si utilizzeranno script da [script Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) di Lab Services per installare il ruolo Hyper-V.  Gli script imposteranno anche la rete in modo che le macchine virtuali Hyper-V possano avere accesso a Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Utilizzo degli strumenti di Windows per abilitare la virtualizzazione annidata

Configurazione della virtualizzazione annidata per Windows Server 2016 o Windows Server 2019 tramite ruoli di Windows e strumenti di amministrazione, vedere [Abilitare manualmente la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.The](how-to-enable-nested-virtualization-template-vm-ui.md)setup nested virtualization for Windows Server 2016 or Windows Server 2019 using Windows roles and administrative tools, see Enable nested virtualization on a template virtual machine in Azure Lab Services manually.  Le istruzioni illustrano anche come configurare la rete in modo che le macchine virtuali Hyper-V possano avere accesso a Internet.
