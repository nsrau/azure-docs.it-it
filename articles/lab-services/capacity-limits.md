---
title: Limiti di capacità in Azure Lab Services
description: Informazioni sui limiti di capacità (limiti delle macchine virtuali) in Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491019"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Limiti di capacità in Azure Lab Services
Azure Lab Services dispone di limiti di capacità predefiniti per le sottoscrizioni di Azure per rispettare le limitazioni della quota di calcolo di Azure e per mitigare le frodi. Tutte le sottoscrizioni di Azure avranno un limite di capacità iniziale, che può variare in base al tipo di sottoscrizione, al numero di core di calcolo standard e ai core GPU disponibili all'interno Azure Lab Services. Limita il numero di macchine virtuali che è possibile creare all'interno dei Lab prima di richiedere un aumento del limite.  

Se è stato raggiunto il limite di core per le macchine virtuali della sottoscrizione, verranno visualizzati messaggi di Azure Lab Services quando si tenta di eseguire azioni per la creazione di altre macchine virtuali. Ad esempio: 

- Creare un lab
- Pubblicare un Lab
- Modificare la capacità del Lab per aggiungere altre macchine virtuali a un lab esistente

Queste azioni possono anche essere disabilitate se è già stato raggiunto il limite di core. 

![Limiti di core-messaggio di avviso](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Sottoscrizioni con limite predefinito di zero Core
Alcuni tipi di sottoscrizione rari usati più di frequente per frode possono avere un limite predefinito di 0 Core standard e 0 core GPU. Se si usa uno di questi tipi di sottoscrizione, l'amministratore che crea l'account Lab dovrà richiedere un aumento del limite prima di poter usare Azure Lab Services. 

Per richiedere un aumento del limite, l'amministratore può seguire questa procedura:  

1.  Nella sottoscrizione [creare un account Lab](tutorial-setup-lab-account.md).
2.  Nella pagina **Panoramica** dell'account Lab fare clic sul pulsante **Richiedi aumento limite** nella parte superiore. 
3.  Seguire i passaggi nel modulo per inviare una richiesta di supporto per aumentare il limite.

## <a name="request-a-limit-increase"></a>Richiedi un aumento del limite
Se si raggiunge il limite di core, è possibile richiedere un aumento del limite per continuare a usare Azure Lab Services. Il processo di richiesta è un checkpoint per garantire che la sottoscrizione non sia inclusa in casi di frode o di distribuzioni su larga scala improvvise.

I messaggi relativi al limite di core della macchina virtuale nel portale di Azure Lab Services includono un collegamento per richiedere un aumento del limite. Il collegamento apre una nuova scheda del browser in cui è possibile creare una nuova richiesta di supporto. Le informazioni sul tipo di problema, sulla sottoscrizione e sul tipo di quota verranno automaticamente compilate come illustrato nell'immagine seguente: 

![Nuova richiesta di supporto](./media/capacity-limits/new-support-request.png)


Verrà quindi richiesto di fornire altre informazioni sull'aumento del limite. Nel campo **Description (Descrizione** ) specificare i dettagli seguenti:

- Cosa si sta tentando di eseguire, ad esempio creando un Lab per insegnare una classe di informatica, eseguire un hackathon e così via.
- Dimensioni della macchina virtuale in uso per questo Lab
- Numero di macchine virtuali necessarie

Una volta inviata la richiesta di supporto, si esaminerà la richiesta. Se necessario, ti contatteremo per ottenere ulteriori dettagli. 

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente:
- [Guida dell'amministratore: ridimensionamento delle macchine virtuali](administrator-guide.md#vm-sizing).
- [Domande frequenti](classroom-labs-faq.md)