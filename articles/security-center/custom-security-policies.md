---
title: Creare criteri di sicurezza personalizzati nel Centro sicurezza di Azure Documenti Microsoft
description: Azure custom policy definitions monitored by Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258261"
---
# <a name="using-custom-security-policies"></a>Utilizzo di criteri di sicurezza personalizzatiUsing custom security policies

Per proteggere i sistemi e l'ambiente, il Centro sicurezza di Azure genera consigli sulla sicurezza. Queste raccomandazioni si basano sulle procedure consigliate del settore, incorporate nei criteri di sicurezza generici e predefiniti forniti a tutti i clienti. Possono anche provenire dalla conoscenza del Centro sicurezza degli standard di settore e normativi.

Con questa funzione, è possibile aggiungere le proprie iniziative *personalizzate.* Si riceveranno quindi suggerimenti se l'ambiente non segue i criteri creati. Tutte le iniziative personalizzate create verranno visualizzate accanto alle iniziative incorporate nel dashboard di conformità alle normative descritto nell'esercitazione [Migliorare la conformità normativa.](security-center-compliance-dashboard.md)

Come illustrato di [seguito](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) nella documentazione di Criteri di Azure, quando si specifica un percorso per l'iniziativa personalizzata, deve essere un gruppo di gestione o una sottoscrizione. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Per aggiungere un'iniziativa personalizzata alla sottoscrizioneTo add a custom initiative to your subscription 

1. Dalla barra laterale del Centro sicurezza, apri la pagina Criteri di **sicurezza.**

1. Selezionare una sottoscrizione o un gruppo di gestione a cui si vuole aggiungere un'iniziativa personalizzata.

    [![Selezione di una sottoscrizione per la quale si creeranno i criteri personalizzati](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > È necessario aggiungere standard personalizzati a livello di sottoscrizione (o superiore) affinché vengano valutati e visualizzati nel Centro sicurezza. 
    >
    > Quando si aggiunge uno standard personalizzato, viene assegnata *un'iniziativa* a tale ambito. È pertanto consigliabile selezionare l'ambito più ampio richiesto per tale assegnazione.

1. Nella pagina Criteri di sicurezza, in Iniziative personalizzate, fare clic su **Aggiungi un'iniziativa personalizzata.**

    [![Fai clic su "Aggiungi un'iniziativa personalizzata"](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Viene visualizzata la pagina seguente:

    ![Creare o aggiungere un criterio](media/custom-security-policies/create-or-add-custom-policy.png)

1. Nella pagina Aggiungi iniziative personalizzate esaminare l'elenco dei criteri personalizzati già creati nell'organizzazione. Se viene visualizzato uno che si desidera assegnare alla sottoscrizione, fare clic su **Aggiungi**. Se nell'elenco non è presente un'iniziativa che soddisfi le proprie esigenze, ignorare questo passaggio.

1. Per creare una nuova iniziativa personalizzata:

    1. Fare clic su **Crea nuovo**.
    1. Immettere la posizione e il nome della definizione.
    1. Selezionare i criteri da includere e fare clic su **Aggiungi**.
    1. Immettere i parametri desiderati.
    1. Fare clic su **Salva**.
    1. Nella pagina Aggiungi iniziative personalizzate fare clic su Aggiorna e la nuova iniziativa verrà visualizzata come disponibile.
    1. Fare clic su **Aggiungi** e assegnarlo alla sottoscrizione.

    > [!NOTE]
    > La creazione di nuove iniziative richiede credenziali del proprietario della sottoscrizione. Per altre informazioni sui ruoli di Azure, vedere Autorizzazioni nel Centro sicurezza di Azure.For more information about Azure roles, see [Permissions in Azure Security Center.](security-center-permissions.md)

    La tua nuova iniziativa ha effetto e puoi vedere l'impatto nei due modi seguenti:

    * Nella barra laterale del Centro sicurezza selezionare **Conformità alle normative**in Criteri & conformità. Il dashboard di conformità si apre per mostrare la nuova iniziativa personalizzata insieme alle iniziative integrate.
    
    * Si inizierà a ricevere suggerimenti se l'ambiente non segue i criteri definiti.

1. Per visualizzare i consigli risultanti per la norma, fai clic su **Consigli** nella barra laterale per aprire la pagina dei consigli. Le raccomandazioni verranno visualizzate con un'etichetta "Personalizzata" e saranno disponibili entro circa un'ora.

    [![Raccomandazioni personalizzate](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare criteri di sicurezza personalizzati. 

Per altri materiali correlati, vedere i seguenti articoli: 

- [Panoramica dei criteri di sicurezza](tutorial-security-policy.md)
- [Elenco dei criteri di sicurezza predefiniti](security-center-policy-definitions.md)