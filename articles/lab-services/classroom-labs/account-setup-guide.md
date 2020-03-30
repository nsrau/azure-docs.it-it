---
title: Guida alla configurazione dell'account lab accelerato per Azure Lab ServicesAccelerated lab account setup guide for Azure Lab Services
description: Questa guida consente agli amministratori di configurare rapidamente un account lab per l'utilizzo all'interno della scuola.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 7b9ef32f16369bbb6c5bb69ef500ec8bb90fde20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370863"
---
# <a name="lab-account-setup-guide"></a>Guida alla configurazione dell'account lab

Il primo passaggio che deve essere completato dagli amministratori consiste nel configurare un account lab all'interno della sottoscrizione di Azure.The first step that needs to be completed by administrators is to set up a lab account within your Azure subscription.  La configurazione di un account lab è un contenitore per i laboratori della classe e richiede solo pochi minuti.

## <a name="understand-your-schools-lab-account-requirements"></a>Comprendere i requisiti dell'account lab della scuola

Per comprendere come configurare l'account lab in base alle esigenze dell'istituto di istruzione, è necessario considerare le domande seguenti:

**È possibile accedere a una sottoscrizione di Azure?**

Per creare un account lab, è necessario accedere a una sottoscrizione di Azure configurata per l'istituto di istruzione. la tua scuola può avere uno o più abbonamenti.  Una sottoscrizione viene usata per gestire la fatturazione e la sicurezza per tutte le risorse di Azure, ovvero i servizi usati al suo interno, inclusi gli account lab.

**Quanti account lab devono essere creati?**

Per iniziare rapidamente, un approccio ragionevole consiste nel creare un singolo account lab e successivamente creare altri account lab in base alle esigenze.  Ad esempio, è possibile evolvere fino ad avere un account lab per reparto.

**Chi devono essere proprietari e collaboratori dell'account di laboratorio?**

Gli amministratori sono in genere i proprietari e i collaboratori di un account lab poiché sono responsabili della gestione dei criteri che si applicano a tutti i lab contenuti nell'account lab.  La persona che crea l'account lab è automaticamente un proprietario.  È possibile aggiungere altri proprietari, ovvero collaboratori (in genere dal tenant AAD associato alla sottoscrizione) per gestire un account lab assegnando il ruolo Proprietario/Collaboratore a livello di account lab.

**A chi sarà consentito creare o gestire i lab?**

È possibile scegliere di fare in modo che gli amministratori e i membri dell'istituto di istruzione creino e gestiscano i lab. questi utenti (in genere dal tenant AAD associato alla sottoscrizione) vengono assegnati al ruolo Lab Creator all'interno dell'account lab.

**Vuoi offrire ai creatori di laboratori la possibilità di salvare immagini che possono essere condivise tra laboratori?**

Una Raccolta immagini condivise è un repository che è possibile utilizzare per salvare e condividere immagini.  Il vantaggio di questo è che se si dispone di più classi che hanno bisogno delle stesse immagini, i creatori di laboratorio possono creare l'immagine una sola volta e condividerla tra i laboratori.  Tuttavia, per iniziare, è perfettamente ragionevole iniziare senza una Galleria di immagini condivise; e, si può sempre scegliere di aggiungere uno in un secondo momento.

Se hai risposto "Sì" a questa domanda, dovrai creare e, o allegare una raccolta immagini condivise al tuo account lab.  Se hai risposto: "Non lo so", puoi rinviare la decisione a dopo.

Quando all'account lab è associata una raccolta immagini condivisa

**Quali immagini in Azure Marketplace utilizzeranno i laboratori della classe?**

Azure Marketplace offre centinaia di immagini che è possibile abilitare in modo che i creatori di lab possano usare l'immagine per la creazione del lab.  Alcune immagini possono includere tutto ciò di cui un laboratorio ha già bisogno.  In altri casi, è possibile utilizzare un'immagine come punto di partenza e quindi l'autore del lab può personalizzarla installando applicazioni aggiuntive, strumenti e così via.

Se non sai quali immagini dovrai usare, puoi sempre tornare su questo in un secondo momento per abilitarle.  Inoltre, il modo migliore per vedere quali immagini sono disponibili è quello di creare prima un account lab - questo vi darà accesso in modo da poter rivedere l'elenco delle immagini disponibili e il loro contenuto.  Di seguito vengono fornite altre informazioni.
  
**Le macchine virtuali del lab devono avere accesso ad altre risorse di Azure o locali?**

Quando si configura un account lab, è anche possibile eseguire il peer network con una rete virtuale (VNet).  Per decidere se è necessario eseguire il peercone con una rete virtuale, considerare le domande seguenti:To decide whether you need to peer with a VNet, consider the following questions:

- **È necessario fornire l'accesso a un server licenze?**
  
   Se si prevede di usare le immagini di Azure Marketplace, il costo della licenza del sistema operativo viene incluso nei prezzi per Lab Services, pertanto *non* è necessario fornire licenze per il sistema operativo stesso.  Tuttavia, per software aggiuntivo, applicazioni installate, è necessario fornire una licenza in base alle esigenze.

- **Le macchine virtuali del laboratorio devono accedere ad altre risorse locali, ad esempio una condivisione file, un database e così via?**

   È necessario creare una rete virtuale per fornire l'accesso alle risorse locale, in genere utilizzando un gateway di rete virtuale da sito a sito.  Se non si dispone di una rete virtuale configurata, sarà necessario investire ulteriore tempo per questo.  Ulteriori informazioni su come impostare questo sono fornite di seguito.

- **Le macchine virtuali lab devono accedere ad altre risorse di Azure che si trovano all'interno di una rete virtuale?**

    Se è necessario accedere alle risorse di Azure *non* protette all'interno di una rete virtuale, è possibile accedere a queste risorse tramite Internet pubblico senza eseguire alcun peering.

    Se hai risposto "Sì" a una o più domande, dovrai creare un peer dell'account lab su una rete virtuale.  Se hai risposto, 'Non lo so', puoi posticipare questa decisione fino a un momento successivo poiché puoi sempre scegliere di peerizzare una rete virtuale dopo aver creato l'account lab.

## <a name="set-up-your-lab-account"></a>Configurare l'account lab

Una volta soddisfatti i requisiti per l'account lab, è possibile configurarlo.  Seguire i collegamenti in questa sezione per informazioni su come configurare l'account lab:

1. **Creare l'account lab**

   Per istruzioni, fare riferimento all'esercitazione sulla [creazione di un account lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)

   Quando si crea un account lab, può essere utile acquisire familiarità con le risorse di Azure coinvolte. Fare riferimento al seguente elenco per ulteriori informazioni e indicazioni sulla creazione di queste risorse:

   - [Sottoscrizione](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Gruppo di risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Lab Account](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratorio aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selezione di una regione - Posizione](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations)
   - [Linee guida per la denominazione delle risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Aggiungere utenti al ruolo Lab Creator**

   Per istruzioni, fare riferimento all'esercitazione [sull'aggiunta](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) di utenti al ruolo Lab Creator.

   Inoltre, per ulteriori informazioni sui diversi ruoli che possono essere assegnati agli utenti che gestiranno gli account lab e i lab, vedere la [guida sulla gestione dell'identità](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Connettersi a una rete virtuale peerConnect to a peer VNet**

   Per istruzioni, fare riferimento alla guida alle procedure per la [connessione della rete del lab a una rete virtuale peer.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)

   Potrebbe anche essere necessario fare riferimento alle istruzioni sulla [configurazione dell'intervallo](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)di indirizzi delle macchine virtuali lab.

4. **Abilitare e rivedere le immagini**

    Per istruzioni, consulta la guida alle procedure relative [all'abilitazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images) delle immagini di Marketplace per i creatori di laboratorio.

    Per esaminare il contenuto di ogni immagine del Marketplace, fai clic sul nome dell'immagine.  Ad esempio, fare riferimento alla schermata seguente che mostra i dettagli per l'immagine della macchina virtuale Ubuntu Data Science:For example, refer to the following screenshot which shows the details for the Ubuntu Data Science VM image:

    ![Esaminare le immagini del Marketplace](../media/setup-guide/review-marketplace-images.png)

    Se all'account lab è associata una raccolta immagini condivise e si desidera abilitare la condivisione di immagini personalizzate da parte dei creatori del lab, sarà necessario completare passaggi simili, come illustrato nella schermata seguente:

    ![Abilitazione delle immagini personalizzate in Shared Image Gallery](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Gestire gli account Lab](how-to-manage-lab-accounts.md)

- [Guida alla configurazione del laboratorio di Classroom](setup-guide.md)
