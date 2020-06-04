---
title: Creare criteri di sicurezza personalizzati nel Centro sicurezza di Azure | Microsoft Docs
description: Definizioni di criteri personalizzati di Azure monitorate nel Centro sicurezza di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: db310cb7ea5c3a89eb727262a7a7ed63ce55b5db
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650882"
---
# <a name="using-custom-security-policies"></a>Usare criteri di sicurezza personalizzati

Per semplificare la protezione dei sistemi e dell'ambiente in uso, il Centro sicurezza di Azure genera raccomandazioni sulla sicurezza. Questi consigli si basano sulle procedure consigliate del settore, che vengono integrate nei più generici criteri di sicurezza predefiniti forniti a tutti i clienti. Possono inoltre essere generati a partire dalla conoscenza del Centro sicurezza degli standard di settore e normativi.

Con questa funzionalità è possibile aggiungere iniziative *personalizzate* . Si riceverà quindi una serie di raccomandazioni nel caso in cui l'ambiente non segua i criteri creati. Eventuali iniziative personalizzate create verranno visualizzate insieme alle iniziative predefinite nel dashboard della conformità normativa, come descritto nell'esercitazione [Migliorare la conformità alle normative](security-center-compliance-dashboard.md).

Come illustrato nella [documentazione sui criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location), quando si specifica una posizione per l'iniziativa personalizzata, è necessario indicare un gruppo di gestione o una sottoscrizione. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Per aggiungere un'iniziativa personalizzata alla sottoscrizione 

1. Dalla barra laterale del Centro sicurezza aprire la pagina **Criteri di sicurezza**.

1. Selezionare la sottoscrizione o il gruppo di gestione a cui si intende aggiungere un'iniziativa personalizzata.

    [![Selezione di una sottoscrizione per la quale si creeranno i criteri personalizzati](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > È necessario aggiungere standard personalizzati a livello di sottoscrizione (o superiore) per consentirne la valutazione e la visualizzazione nel Centro sicurezza. 
    >
    > Quando si aggiunge uno standard personalizzato, all'ambito selezionato viene assegnata un'*iniziativa*. Si consiglia pertanto di selezionare l'ambito più ampio possibile per l'assegnazione.

1. Nella pagina Criteri di sicurezza, sotto Iniziative personalizzate, fare clic su **Aggiungi un'iniziativa personalizzata**.

    [![Fare clic su **Aggiungi un'iniziativa personalizzata**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Verrà visualizzata la pagina seguente:

    ![Creare o aggiungere criteri](media/custom-security-policies/create-or-add-custom-policy.png)

1. Nella pagina Aggiungi iniziative personalizzate controllare l'elenco dei criteri personalizzati già creati nell'organizzazione. Se si vuole assegnarne uno alla propria sottoscrizione, fare clic su **Aggiungi**. Se nessuna delle iniziative presenti nell'elenco soddisfa le proprie esigenze, ignorare questo passaggio.

1. Per creare una nuova iniziativa personalizzata:

    1. Fare clic su **Crea nuovo**.
    1. Immettere il percorso e il nome della definizione.
    1. Selezionare i criteri da includere e fare clic su **Aggiungi**.
    1. Immettere i parametri desiderati.
    1. Fare clic su **Salva**.
    1. Nella pagina Aggiungi iniziative personalizzate fare clic su Aggiorna. La nuova iniziativa verrà visualizzata non appena sarà disponibile.
    1. Fare clic su **Aggiungi** per assegnarla alla propria sottoscrizione.

    > [!NOTE]
    > Per creare una nuova iniziativa, è necessario specificare le credenziali del proprietario della sottoscrizione. Per altre informazioni sui ruoli di Azure, vedere [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md).

    La nuova iniziativa diventa effettiva ed è possibile osservarne l'impatto nei due modi seguenti:

    * Nella barra laterale del Centro sicurezza, selezionare **Conformità alle normative** in Criteri e conformità. Viene visualizzato il dashboard di conformità in cui la nuova iniziativa personalizzata appare insieme alle iniziative predefinite.
    
    * Si inizierà a ricevere raccomandazioni nel momento in cui l'ambiente non segue più i criteri definiti.

1. Per visualizzare le raccomandazioni generate per i criteri specificati, fare clic su **Raccomandazioni** nella barra laterale per aprire la pagina delle raccomandazioni. Le raccomandazioni verranno contrassegnate con l'etichetta "Personalizzata" e saranno disponibili entro un'ora circa.

    [![Raccomandazioni personalizzate](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhancing-your-custom-recommendations-with-detailed-information"></a>Ottimizzazione delle raccomandazioni personalizzate con informazioni dettagliate

Le raccomandazioni predefinite integrate nel Centro sicurezza di Azure includono dettagli come i livelli di gravità e istruzioni per la correzione. Per aggiungere questo tipo di informazioni alle raccomandazioni personalizzate in modo che vengano visualizzate nel portale di Azure o in qualsiasi altra posizione da cui si accede alle raccomandazioni, è necessario usare l'API REST. 

I due tipi di informazioni che è possibile aggiungere sono:

- **RemediationDescription** - Stringa
- **Severity** - Enumerazione [Low, Medium, High]

Per i criteri associati a un'iniziativa personalizzata è necessario aggiungere anche i metadati, specificandoli nella proprietà 'securityCenter', come illustrato di seguito:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

Di seguito è riportato un esempio di criteri personalizzati con la proprietà metadata/securityCenter inclusa:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Per un altro esempio di uso della proprietà securityCenter, vedere [questa sezione della documentazione dell'API REST](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare criteri di sicurezza personalizzati. 

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Panoramica dei criteri di sicurezza](tutorial-security-policy.md)
- [Elenco dei criteri di sicurezza predefiniti](security-center-policy-definitions.md)