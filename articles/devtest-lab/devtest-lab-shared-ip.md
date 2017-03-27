---
title: Comprendere gli indirizzi IP condivisi in Azure DevTest Labs| Microsoft Docs
description: Informazioni su come Azure DevTest Labs usa gli indirizzi IP condivisi per ridurre al minimo gli indirizzi IP pubblici necessari per accedere alle macchine virtuali del lab.
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b4ae6b16880e4a5e53c8f94dc814e25254c20d73
ms.lasthandoff: 03/10/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Comprendere gli indirizzi IP condivisi in Azure DevTest Labs

Azure DevTest Labs usa gli indirizzi IP condivisi per ridurre al minimo il numero di indirizzi IP pubblici necessari per accedere alle singole macchine virtuali del lab.  In questo articolo viene descritto come funzionano gli indirizzi IP condivisi e le opzioni di configurazione correlate.

## <a name="shared-ip-setting"></a>Impostazione di un indirizzo IP condiviso

Quando viene creato, un lab si trova nella subnet di una rete virtuale.  Per impostazione predefinita, questa subnet viene creata con **Enable shared public IP** (Abilita indirizzo IP pubblico condiviso) impostato su *Sì*.  Questa configurazione crea un indirizzo IP pubblico per l'intera subnet.  È possibile disabilitare questa funzionalità al termine della creazione un lab [seguendo i passaggi descritti in questo articolo](devtest-lab-configure-vnet.md).

![Nuova subnet del lab](media/devtest-lab-shared-ip/lab-subnet.png)

Tutte le macchine virtuali create in questo lab usano per impostazione predefinita un indirizzo IP condiviso.  Quando si crea la macchina virtuale, questa impostazione è visibile nel pannello **Impostazioni avanzate** in **Configurazione indirizzi IP**.

![Nuova macchina virtuale](media/devtest-lab-shared-ip/new-vm.png)

Ogni volta che una macchina virtuale con indirizzo IP condiviso abilitato viene aggiunta alla subnet, viene assegnata una porta TCP all'indirizzo IP pubblico che inoltra alla porta RDP nella macchina virtuale.  

## <a name="using-the-shared-ip"></a>Uso dell'indirizzo IP condiviso

La connessione al desktop remoto della macchina virtuale in un client RDP avviene tramite l'indirizzo IP o un nome di dominio completo, seguito da due punti e dalla porta.  Nell'immagine seguente, ad esempio, l'indirizzo RDP per connettersi alla macchina virtuale è `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.  In alternativa, dal Portale di Azure selezionare il pulsante **Connetti** per scaricare un file RDP preconfigurato.

![Esempio di macchina virtuale](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Passaggi successivi

* [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md)






