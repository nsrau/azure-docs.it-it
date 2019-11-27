---
title: Creare criteri di sicurezza personalizzati nel centro sicurezza di Azure | Microsoft Docs
description: Definizioni dei criteri personalizzati di Azure monitorati dal centro sicurezza di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 3e60c0e165b8f9ec79aac2048011d9e315b0ce18
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483956"
---
# <a name="using-custom-security-policies-preview"></a>Uso di criteri di sicurezza personalizzati (anteprima)

Per proteggere i sistemi e l'ambiente, il Centro sicurezza di Azure genera raccomandazioni sulla sicurezza. Questi consigli si basano sulle procedure consigliate del settore, che vengono incorporate nei criteri di sicurezza generici predefiniti forniti a tutti i clienti. Possono inoltre derivare dalla conoscenza di standard di settore e normativi del Centro sicurezza.

Con questa funzionalità di anteprima, è possibile aggiungere iniziative *personalizzate* . Si riceveranno quindi raccomandazioni se l'ambiente non segue i criteri creati.

Come illustrato nella documentazione di [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) , quando si specifica un percorso per l'iniziativa personalizzata, deve trattarsi di un gruppo di gestione o di una sottoscrizione. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Per aggiungere un'iniziativa personalizzata alla sottoscrizione 

1. Dalla barra laterale del Centro sicurezza aprire la pagina dei **criteri di sicurezza** .

1. Selezionare una sottoscrizione o un gruppo di gestione a cui si desidera aggiungere un'iniziativa personalizzata.

    [![selezionare una sottoscrizione per la quale si creeranno i criteri personalizzati](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > È necessario aggiungere standard personalizzati a livello di sottoscrizione (o superiore) per la valutazione e la visualizzazione nel centro sicurezza. 
    >
    > Quando si aggiunge uno standard personalizzato, viene assegnata un' *iniziativa* a tale ambito. Si consiglia pertanto di selezionare l'ambito più ampio necessario per l'assegnazione.

1. Nella pagina Criteri di sicurezza, sotto le iniziative personalizzate (anteprima), fare clic su **Aggiungi un'iniziativa personalizzata**.

    [![fare clic su * * Aggiungi un'iniziativa personalizzata * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Viene visualizzata la pagina seguente:

    ![Creare o aggiungere un criterio](media/custom-security-policies/create-or-add-custom-policy.png)

1. Nella pagina Aggiungi iniziative personalizzate esaminare l'elenco dei criteri personalizzati già creati nell'organizzazione. Se ne viene visualizzato uno che si vuole assegnare alla sottoscrizione, fare clic su **Aggiungi**. Se nell'elenco non è presente un'iniziativa che soddisfi le proprie esigenze, ignorare questo passaggio.

1. Per creare una nuova iniziativa personalizzata:

    1. Fare clic su **Crea nuovo**.
    1. Immettere il percorso e il nome della definizione.
    1. Selezionare i criteri da includere e fare clic su **Aggiungi**.
    1. Immettere i parametri desiderati.
    1. Fare clic su **Save**.
    1. Nella pagina Aggiungi iniziative personalizzate fare clic su Aggiorna e la nuova iniziativa verrà visualizzata come disponibile.
    1. Fare clic su **Aggiungi** e assegnarlo alla sottoscrizione.

    > [!NOTE]
    > La creazione di nuove iniziative richiede le credenziali del proprietario della sottoscrizione. Per altre informazioni sui ruoli di Azure, vedere [autorizzazioni nel centro sicurezza di Azure](security-center-permissions.md).

1. Per visualizzare le raccomandazioni risultanti per il criterio, fare clic su **raccomandazioni** nella barra laterale per aprire la pagina raccomandazioni. Le indicazioni verranno visualizzate con un'etichetta "personalizzata" e saranno disponibili entro circa un'ora.

    [![consigli personalizzati](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare criteri di sicurezza personalizzati. 

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Panoramica dei criteri di sicurezza](tutorial-security-policy.md)
- [Elenco dei criteri di sicurezza predefiniti](security-center-policy-definitions.md)