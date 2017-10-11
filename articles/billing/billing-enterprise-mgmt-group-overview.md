---
title: Organizzare le risorse con i gruppi di gestione di Azure - Azure | Microsoft Docs
description: Informazioni sui gruppi di gestione e sul loro uso.
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---


# <a name="organize-your-resources-with-azure-management-groups"></a>Organizzare le risorse con i gruppi di gestione di Azure 

Se si dispone di più sottoscrizioni, è possibile organizzarle in contenitori chiamati "gruppi di gestione" che consentono di gestire l'accesso, i criteri, i costi e la conformità tra le sottoscrizioni. Ad esempio, è possibile applicare i criteri a un gruppo di gestione che limita i tipi di risorsa che è possibile creare.

> [!Note]
> Questa funzionalità è attualmente in anteprima privata. [Iscriversi qui](https://aka.ms/MGPreviewSignup) per avere per avere l'anteprima.   
 


I gruppi di gestione possono essere organizzati in una gerarchia. La struttura mostrata è una rappresentazione di esempio di una possibile gerarchia per i gruppi di gestione:


![Albero gerarchico](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Correlazione tra gruppi di gestione e registrazione ad Azure Enterprise

L'introduzione ai gruppi di gestione è un passaggio di un processo più lungo di transizione delle funzioni da [Enterprise Portal](https://ea.azure.com) al [portale di Azure](https://portal.azure.com).

La struttura del gruppo di gestione viene creata così come descritto in Enterprise Portal. L'intera gerarchia composta da registrazione, reparti e account viene mappata nei gruppi di gestione corrispondenti. 

Ecco come la struttura EA attuale esegue il mapping nella gerarchia di un gruppo di gestione. 

![Albero gerarchico](media/billing-enterprise-mgmt-groups/tree2.png)

La tabella seguente illustra come viene eseguito il mapping degli utenti di Enterprise Portal nella gerarchia del gruppo di gestione.

|    Ruolo EA                                       |    Ruolo nel nodo del gruppo di gestione su cui è stato eseguito il mapping    |    Autorizzazioni per il nodo del gruppo di gestione                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    Amministratore EA                              |    Collaboratore per i criteri delle risorse                   |    Può visualizzare i costi, gestire i criteri e visualizzare la gerarchia a livello di nodo di registrazione e a livelli inferiori    |
|    Amministratore EA in modalità di sola lettura            |    Lettore per la fatturazione                                |    Può leggere i costi e visualizzare la gerarchia a livello di nodo di registrazione e a livelli inferiori                              |
|    Amministratore del reparto                      |    Lettore per la fatturazione                                |    Può leggere i costi e visualizzare la gerarchia a livello di nodo del reparto e a livelli inferiori                                 |
|    Amministratore del reparto in modalità di sola lettura    |    Lettore per la fatturazione                                |    Può leggere i costi e visualizzare la gerarchia a livello di nodo del reparto e a livelli inferiori                                 |
|    Proprietario dell'account                                 |    Collaboratore per i criteri delle risorse                   |    Può visualizzare i costi, gestire i criteri e visualizzare la gerarchia a livello di nodo dell'account e a livelli inferiori       |




## <a name="view-management-groups-in-the-azure-portal"></a>Visualizzare i gruppi di gestione nel portale di Azure

Per visualizzare una registrazione, un reparto o un account all'interno dell'anteprima, accedere al portale di Azure usando il collegamento nel messaggio di posta elettronica di benvenuto.   

![gerarchia](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Visualizzazione dei costi 
Nelle schermate dei dettagli relativi ai gruppi di gestione, vengono visualizzati i costi attuali da inizio mese. Questi costi sono basati sull'uso e non tengono conto di importi prepagati, eccedenze, quantità incluse, rettifiche e imposte. Per il gruppo di gestione corrispondente alla registrazione, la sezione dei costi mostra l'impegno rimanente.  

![dettagli di registrazione](media/billing-enterprise-mgmt-groups/enrollment.png)

 I "Costi fatturati separatamente" sono l'accumulo mensile delle modifiche separate ad esempio marketplace, eccedenze e altri costi che non risultano allineati al livello di impegno della registrazione.  Per altre informazioni sulla scomposizione dei costi, consultare [Enterprise Portal](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Abilitazione dell'accesso ai costi
Se i costi non vengono visualizzati, vedere il documento [Troubleshoot enterprise cost views](https://aka.ms/enableazurecosts) (Risoluzione dei problemi relativi alla visualizzazione dei costi aziendali) per avere informazioni.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Ritardi tra Enterprise Portal e il portale di Azure 
* Nell'anteprima gli importi visualizzati nel portale di Azure potrebbero subire un ritardo rispetto ai valori in Enterprise Portal. Questo problema è temporaneo e Microsoft sta lavorando per risolverlo.
* Le impostazioni aggiornate in Enterprise Portal subiscono un ritardo di alcuni minuti prima che gli aggiornamenti siano visibili nel portale di Azure. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Rapporto tra gruppi di gestione e directory   
Come le sottoscrizioni, anche i gruppi di gestione hanno una relazione di trust con Azure AD. Una gerarchia di un gruppo di gestione considera attendibile una sola directory per autenticare gli utenti. Tutti gli amministratori associati a una gerarchia del gruppo di gestione devono appartenere alla stessa directory. 

Nel momento in cui la gerarchia di registrazione viene mappata nei gruppi di gestione, si stabilisce una relazione di trust con una sola directory. Dove possibile, viene selezionata una directory esistente associata agli account utente di registrazione. In alcuni casi, viene creata una nuova directory e tutti gli utenti della registrazione esistente vengono invitati in questa directory. Le directory associate alle sottoscrizioni di registrazione non sono interessate. Pertanto, la gerarchia potrebbe essere creata in una directory diversa dalle sottoscrizioni. [Altre informazioni](billing-enterprise-mgmt-grp-find.md) sull'impatto di questo processo sull'esperienza di esplorazione tra la gerarchia e le relative sottoscrizioni.

## <a name="administering-your-management-groups"></a>Amministrare i gruppi di gestione
I gruppi di gestione nel portale di Azure sono in anteprima e in questa versione iniziale sono di sola lettura. Per eseguire gli aggiornamenti, passare a Enterprise Portal. Gli aggiornamenti si riflettono automaticamente nel portale di Azure. Vedere la documentazione all'interno del portale aziendale per informazioni su come apportare modifiche e aggiungere elementi.   

## <a name="policy-management"></a>Gestione dei criteri
Gestione risorse consente di creare criteri personalizzati per gestire le risorse. Con i gruppi di gestione, i criteri possono essere assegnati a qualsiasi livello della gerarchia e le risorse ereditano tali criteri.  [Altre informazioni](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> I criteri non vengono applicati nelle directory. 



