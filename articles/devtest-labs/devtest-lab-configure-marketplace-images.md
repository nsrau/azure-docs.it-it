---
title: Configurare le impostazioni dell'immagine di Azure Marketplace in Azure DevTest Labs
description: Specificare le immagini di Azure Marketplace che possono essere usate per la creazione di una VM in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512436"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurare le impostazioni dell'immagine di Azure Marketplace in Azure DevTest Labs
Lab di sviluppo/test supporta la creazione di VM basate su immagini di Azure Marketplace in base alla configurazione delle immagini di Azure Marketplace da usare nel lab. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab. Garantisce che il team abbia accesso solo alle immagini del Marketplace necessarie. 

## <a name="specify-allowed-images-for-creating-vms"></a>Specificare le immagini consentite per la creazione di macchine virtuali
Seguire questa procedura per specificare quali immagini di Azure Marketplace sono consentite durante la creazione di una macchina virtuale. 

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco di lab selezionare il proprio lab. 
4. Nella pagina home page per il Lab selezionare **configurazione e criteri**.
5. Nella pagina **configurazione e criteri** del Lab in **basi macchine virtuali**selezionare **Immagini del Marketplace**.
6. Specificare se si desidera che tutte le immagini di Azure Marketplace qualificate siano disponibili per essere usate come base di una nuova macchina virtuale. Se si seleziona **Sì**, tutte le immagini di Azure Marketplace che soddisfano tutti i criteri seguenti saranno disponibili nel lab:
   
   * L'immagine crea una singola macchina virtuale **e**
   * L'immagine usa Azure Resource Manager per il provisioning delle macchine virtuali **e**
   * L'immagine non richiede l'acquisto di un piano di licenze aggiuntivo
     
     Se non si desidera autorizzare alcuna immagine o si desidera specificare le immagini che possono essere usate, selezionare **No**.
     
     ![Opzione per consentire l'uso di tutte le immagini di Marketplace come immagini di base per le macchine virtuali](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se si seleziona **No** nel passaggio precedente, la casella di controllo **Immagini consentite/Seleziona tutto** viene abilitata. 
   È possibile usare questa opzione con la casella di ricerca per selezionare o deselezionare rapidamente tutti gli elementi visualizzati nell'elenco.
   * Selezionare le singole immagini di Azure Marketplace che si desidera rendere disponibili per la creazione di macchine virtuali selezionando la casella di controllo corrispondente all'immagine.
   * Non selezionare alcuna voce nell'elenco se non si desidera rendere disponibile alcuna immagine di Azure Marketplace per l'uso nel lab.
   
     ![È possibile specificare quali immagini di Azure Marketplace possono essere usate come immagini di base per le macchine virtuali](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Risolvere problemi
Se non è possibile trovare un'immagine specifica da abilitare per il Lab, attenersi alla procedura seguente: 

- Verificare se è possibile visualizzare l'immagine durante la creazione di una macchina virtuale di calcolo.
- L'immagine potrebbe non essere disponibile nel tipo di sottoscrizione in uso. Rivolgersi all'amministratore della sottoscrizione per il tipo di sottoscrizione, ad esempio MSDN, gratuito, con pagamento in base al consumo e così via. 
- Il supporto per le immagini di generazione 2 in DevTest Labs è limitato. Se per un'immagine sono disponibili sia la versione Gen 1 che la Gen 2, DevTest Labs visualizza solo la versione Gen 1 durante la creazione di una macchina virtuale. La soluzione alternativa consiste nel creare un'immagine di generazione 2 personalizzata all'esterno del Lab e usarla per creare una macchina virtuale. Se è disponibile solo la versione di generazione 2 dell'immagine, è supportata e visualizzata nell'elenco di DevTest Labs. 
      


## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato il modo in cui le immagini di Azure Marketplace sono consentite durante la creazione di una macchina virtuale, il passaggio successivo consiste nell' [aggiungere una macchina virtuale al Lab](devtest-lab-add-vm.md).

