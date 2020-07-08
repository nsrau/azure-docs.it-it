---
title: Comprendere gli indirizzi IP condivisi in Azure DevTest Labs| Microsoft Docs
description: Informazioni su come Azure DevTest Labs usa gli indirizzi IP condivisi per ridurre al minimo gli indirizzi IP pubblici necessari per accedere alle macchine virtuali del lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85484095"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Comprendere gli indirizzi IP condivisi in Azure DevTest Labs

Azure DevTest Labs consente alle macchine virtuali del lab di condividere gli stessi indirizzi IP per ridurre al minimo il numero di indirizzi IP pubblici necessari per accedere alle singole macchine virtuali del lab.  In questo articolo viene descritto come funzionano gli indirizzi IP condivisi e le opzioni di configurazione correlate.

## <a name="shared-ip-setting"></a>Impostazione di un indirizzo IP condiviso

Quando si crea un Lab, questo viene creato in una subnet di una rete virtuale.  Per impostazione predefinita, questa subnet viene creata con **Enable shared public IP** (Abilita indirizzo IP pubblico condiviso) impostato su *Sì*.  Questa configurazione crea un indirizzo IP pubblico per l'intera subnet.  Per altre informazioni sulla configurazione di reti e subnet virtuali, vedere [Configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nuova subnet del lab](media/devtest-lab-shared-ip/lab-subnet.png)

Per i lab esistenti, è possibile abilitare questa opzione selezionando **Criteri e configurazione > Reti virtuali**. Quindi, selezionare una rete virtuale dall'elenco e scegliere **ABILITA IP PUBBLICO CONDIVISO** per una subnet selezionata. È anche possibile disabilitare questa opzione in qualsiasi lab se non si desidera condividere un indirizzo IP con le macchine virtuali del lab.

Tutte le macchine virtuali create in questo lab usano per impostazione predefinita un indirizzo IP condiviso.  Quando si crea la macchina virtuale, questa impostazione può essere osservata nella pagina **Impostazioni avanzate** in **Configurazione indirizzi IP**.

![Nuova macchina virtuale](media/devtest-lab-shared-ip/new-vm.png)

- **Condiviso:** tutte le macchine virtuali create come **Condivise** vengono inserite in un gruppo di risorse (RG). Un singolo indirizzo IP viene assegnato al relativo gruppo di risorse e tutte le macchine virtuali nel gruppo di risorse utilizzeranno l'indirizzo IP.
- **Pubblico:** ciascuna macchina virtuale creata dispone del proprio indirizzo IP e viene creata nel proprio gruppo di risorse.
- **Privato:** ogni macchina virtuale creata usa un indirizzo IP privato. Non è possibile connettersi a questa macchina virtuale direttamente da Internet con Desktop remoto.

Ogni volta che una macchina virtuale con indirizzo IP condiviso abilitato viene aggiunta alla subnet, DevTest Labs aggiunge automaticamente la macchina virtuale a un sistema di bilanciamento del carico e assegna un numero di porta TCP all'indirizzo IP pubblico, che inoltra alla porta RDP nella macchina virtuale.  

## <a name="using-the-shared-ip"></a>Uso dell'indirizzo IP condiviso

- **Utenti Linux:** il collegamento di SSH alla macchina virtuale avviene tramite l'indirizzo IP o un nome di dominio completo, seguito da due punti e dalla porta. Nell'immagine seguente, ad esempio, l'indirizzo RDP per connettersi alla macchina virtuale è `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`.

  ![Esempio di macchina virtuale](media/devtest-lab-shared-ip/vm-info.png)

- **Utenti Windows:** selezionare il pulsante **Connetti** dal portale Azure per scaricare un file RDP preconfigurato e accedere alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

* [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md)





