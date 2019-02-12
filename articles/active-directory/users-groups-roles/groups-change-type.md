---
title: Modificare il tipo di appartenenza a gruppi statica in dinamica - Azure Active Directory | Microsoft Docs
description: Come creare regole di appartenenza per popolare automaticamente i gruppi e informazioni di riferimento sulle regole.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 7f8c9298c1e74e87411386a1d8ae36a34d986065
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508488"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Modificare l'appartenenza a gruppi statica in dinamica in Azure Active Directory

In Azure Active Directory (Azure AD) è possibile modificare l'appartenenza a un gruppo da statica a dinamica (o viceversa). Azure AD mantiene invariati il nome e l'ID del gruppo nel sistema, in modo che tutti i riferimenti esistenti al gruppo rimangano validi. Se invece si crea un nuovo gruppo, è necessario aggiornare tali riferimenti. L'appartenenza a gruppi dinamica elimina il sovraccarico di gestione per l'aggiunta e la rimozione di utenti. Questo articolo descrive come convertire i gruppi esistenti dall'appartenenza statica a quella dinamica usando l'interfaccia di amministrazione di Azure AD o i cmdlet di PowerShell.

> [!WARNING]
> Quando si modifica un gruppo statico esistente in gruppo dinamico, tutti i membri esistenti vengono rimossi dal gruppo e quindi la regola di appartenenza viene elaborata per aggiungere nuovi membri. Se il gruppo viene usato per controllare l'accesso alle app o alle risorse, i membri originali potrebbero perdere l'accesso finché non viene completata l'elaborazione della regola di appartenenza.
>
> È consigliabile testare prima la nuova regola di appartenenza per verificare che la nuova appartenenza nel gruppo sia quella prevista.

## <a name="change-the-membership-type-for-a-group"></a>Modificare il tipo di appartenenza per un gruppo

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore globale o di amministratore di account utente nel tenant.
2. Selezionare **Gruppi**.
3. Dall'elenco **Tutti i gruppi** aprire il gruppo che si vuole modificare.
4. Selezionare **Proprietà**.
5. Nella pagina **Proprietà** del gruppo selezionare Assegnato (statico), Utente dinamico o Dispositivo dinamico come **Tipo di appartenenza**, a seconda del tipo di appartenenza desiderato. Per l'appartenenza dinamica è possibile usare il generatore di regole per selezionare le opzioni per una regola semplice oppure scrivere manualmente una regola di appartenenza. 

I passaggi seguenti offrono un esempio della procedura da seguire per modificare da statica a dinamica l'appartenenza per un gruppo di utenti.

1. Nella pagina **Proprietà** del gruppo selezionato selezionare **Utente dinamico** come **Tipo di appartenenza** e quindi selezionare Sì nella finestra di dialogo che descrive le modifiche all'appartenenza del gruppo per continuare. 
  
   ![selezionare il tipo di appartenenza Utente dinamico](./media/groups-change-type/select-group-to-convert.png)
  
2. Selezionare **Aggiungi query dinamica** e quindi specificare la regola.
  
   ![immettere la regola](./media/groups-change-type/enter-rule.png)
  
3. Dopo aver creato la regola, selezionare **Aggiungi query** nella parte inferiore della pagina.
4. Selezionare **Salva** nella pagina **Proprietà** del gruppo per salvare le modifiche. Il valore di **Tipo di appartenenza** del gruppo viene aggiornato immediatamente nell'elenco dei gruppi.

> [!TIP]
> La conversione del gruppo potrebbe non riuscire se la regola di appartenenza immessa non è corretta. Nell'angolo superiore destro del portale viene visualizzata una notifica che contiene una spiegazione dei motivi per cui la regola non può essere accettata dal sistema. Leggerla attentamente per capire come modificare la regola per renderla valida. Per esempi di sintassi delle regole e un elenco completo di proprietà, operatori e valori supportati per una regola di appartenenza, vedere [Regole di appartenenza dinamica per i gruppi in Azure Active Directory](groups-dynamic-membership.md).


## <a name="change-membership-type-for-a-group-powershell"></a>Modificare il tipo di appartenenza per un gruppo (PowerShell)

> [!NOTE]
> Per modificare le proprietà dei gruppi dinamici, sarà necessario usare i cmdlet della **versione di anteprima di** [Azure AD PowerShell versione 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). È possibile installare l'anteprima da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview).

Di seguito è riportato un esempio delle funzioni che cambiano la gestione delle appartenenze in un gruppo esistente. In questo esempio è necessario prestare attenzione a modificare correttamente la proprietà GroupTypes e a mantenere i valori eventualmente non correlati all'appartenenza dinamica.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Per rendere statico un gruppo:

```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Per rendere dinamico un gruppo:

```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
