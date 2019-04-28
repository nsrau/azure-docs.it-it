---
title: Comprendere gli indirizzi IP condivisi in Azure DevTest Labs| Microsoft Docs
description: Informazioni su come Azure DevTest Labs usa gli indirizzi IP condivisi per ridurre al minimo gli indirizzi IP pubblici necessari per accedere alle macchine virtuali del lab.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e7080901118dde33ed07c8a80f254b9b0d2e221c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623011"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Comprendere gli indirizzi IP condivisi in Azure DevTest Labs

Azure DevTest Labs consente alle macchine virtuali del lab di condividere gli stessi indirizzi IP per ridurre al minimo il numero di indirizzi IP pubblici necessari per accedere alle singole macchine virtuali del lab.  In questo articolo viene descritto come funzionano gli indirizzi IP condivisi e le opzioni di configurazione correlate.

## <a name="shared-ip-setting"></a>Impostazione di un indirizzo IP condiviso

Quando viene creato, un lab si trova nella subnet di una rete virtuale.  Per impostazione predefinita, questa subnet viene creata con **Enable shared public IP** (Abilita indirizzo IP pubblico condiviso) impostato su *Sì*.  Questa configurazione crea un indirizzo IP pubblico per l'intera subnet.  Per altre informazioni sulla configurazione di reti e subnet virtuali, vedere [Configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nuova subnet del lab](media/devtest-lab-shared-ip/lab-subnet.png)

Per i lab esistenti, è possibile abilitare questa opzione selezionando **Criteri e configurazione > Reti virtuali**. Quindi, selezionare una rete virtuale dall'elenco e scegliere **ABILITA IP PUBBLICO CONDIVISO** per una subnet selezionata. È anche possibile disabilitare questa opzione in qualsiasi lab se non si desidera condividere un indirizzo IP pubblico in macchine virtuali del lab.

Tutte le macchine virtuali create in questo lab usano per impostazione predefinita un indirizzo IP condiviso.  Quando si crea la macchina virtuale, questa impostazione è visibile nel pannello **Impostazioni avanzate** in **Configurazione indirizzi IP**.

![Nuova macchina virtuale](media/devtest-lab-shared-ip/new-vm.png)

- **condiviso:** Tutte le macchine virtuali create come **condiviso** vengono inseriti in un gruppo di risorse (RG). Un singolo indirizzo IP viene assegnato al relativo gruppo di risorse e tutte le macchine virtuali nel gruppo di risorse utilizzeranno l'indirizzo IP.
- **Pubblico**: Ogni macchina virtuale creata ha il proprio indirizzo IP e viene creato nel proprio gruppo di risorse.
- **Privato:** Ogni macchina virtuale creata Usa un indirizzo IP privato. Non sarà possibile connettersi a questa macchina virtuale direttamente da Internet con Desktop remoto.

Ogni volta che una macchina virtuale con indirizzo IP condiviso abilitato viene aggiunta alla subnet, DevTest Labs automaticamente aggiunge la macchina virtuale a un servizio di bilanciamento del carico e assegna un numero di porta TCP all'indirizzo IP pubblico, l'inoltro alla porta RDP nella macchina virtuale.  

## <a name="using-the-shared-ip"></a>Uso dell'indirizzo IP condiviso

- **Utenti Linux:** Stabilire una connessione SSH alla macchina virtuale usando l'indirizzo IP o nome di dominio completo, seguito da due punti poi dalla porta. Nell'immagine seguente, ad esempio, l'indirizzo RDP per connettersi alla macchina virtuale è `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Esempio di macchina virtuale](media/devtest-lab-shared-ip/vm-info.png)

- **Utenti di Windows:** Selezionare il **Connect** pulsante nel portale di Azure per scaricare un file RDP preconfigurato e accedere alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

* [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md)





