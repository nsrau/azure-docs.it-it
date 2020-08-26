---
title: Usare i criteri del firewall di Azure per definire una gerarchia di regole
description: Informazioni su come usare i criteri del firewall di Azure per definire una gerarchia di regole e applicare la conformità.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: c290904c9f4bc7dba70dad9351dc45b676e0c236
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893723"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Usare i criteri del firewall di Azure per definire una gerarchia di regole

Gli amministratori della sicurezza devono gestire i firewall e garantire la conformità nelle distribuzioni locali e cloud. Un componente chiave è la possibilità di fornire ai team delle applicazioni la flessibilità necessaria per implementare pipeline di integrazione continua/recapito continuo per creare regole del firewall in modo automatico.

Criteri del firewall di Azure consente di definire una gerarchia di regole e di applicare la conformità:

- Fornisce una struttura gerarchica per sovrapporre un criterio di base centrale a un criterio del team di applicazioni figlio. Il criterio di base ha una priorità più alta e viene eseguito prima del criterio figlio.
- Usare una definizione personalizzata di controllo degli accessi in base al ruolo per evitare la rimozione accidentale dei criteri di base e fornire l'accesso selettivo ai gruppi di raccolta regole all'interno di una sottoscrizione o di un gruppo di risorse 

## <a name="solution-overview"></a>Panoramica della soluzione

Di seguito sono riportati i passaggi di alto livello per questo esempio:

1. Creare un criterio del firewall di base nel gruppo di risorse del team di sicurezza. 
3. Definire regole specifiche per la sicurezza IT nei criteri di base. In questo modo viene aggiunto un set comune di regole per consentire/negare il traffico.
4. Creare criteri del team di applicazione che ereditano i criteri di base. 
5. Definire le regole specifiche del team dell'applicazione nei criteri. È inoltre possibile eseguire la migrazione di regole da firewall preesistenti.
6. Creare Azure Active Directory ruoli personalizzati per fornire accesso con granularità fine al gruppo di raccolta regole e aggiungere ruoli in un ambito di criteri firewall. Nell'esempio seguente, i membri del team di vendita possono modificare i gruppi di raccolta regole per i criteri del firewall dei team di vendita. Lo stesso vale per il database e i team di progettazione.
7. Associare i criteri al firewall corrispondente. Un firewall di Azure può avere un solo criterio assegnato. Per questo è necessario che ogni team dell'applicazione disponga di un firewall.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Team e requisiti" border="false":::

### <a name="create-the-firewall-policies"></a>Creare i criteri del firewall

- Criterio del firewall di base.

Creare criteri per ogni team di applicazioni:

- Un criterio di firewall delle vendite. I criteri del firewall di vendita ereditano i criteri del firewall di base.
- Criteri del firewall del database. Il criterio firewall del database eredita i criteri di base del firewall.
- Criteri del firewall di progettazione. Il criterio del firewall di progettazione eredita anche il criterio del firewall di base.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Gerarchia dei criteri" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Creare ruoli personalizzati per accedere ai gruppi di raccolta regole 

I ruoli personalizzati vengono definiti per ogni team di applicazioni. Il ruolo definisce le operazioni e l'ambito. I team di applicazioni possono modificare i gruppi di raccolta regole per le rispettive applicazioni.

Utilizzare la seguente procedura di alto livello per definire i ruoli personalizzati:

1. Ottenere la sottoscrizione:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Eseguire il comando seguente:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Usare il comando Get-AzRoleDefinition per restituire il ruolo Reader in formato JSON. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Aprire il ReaderSupportRole.jssu file in un editor.

   Di seguito è riportato l'output JSON. Per informazioni sulle diverse proprietà, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Modificare il file JSON per aggiungere il 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   operazione alla proprietà **Actions**   . Assicurarsi di includere una virgola dopo l'operazione di lettura. Questa azione consente all'utente di creare e aggiornare i gruppi di raccolta regole.
6. In **AssignableScopes**aggiungere l'ID sottoscrizione con il formato seguente: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   È necessario aggiungere ID sottoscrizione espliciti. In caso contrario non sarà possibile importare il ruolo nella sottoscrizione.
7. Eliminare la **Id**   riga della proprietà ID e impostare **IsCustom**la   Proprietà IsTrue su true.
8. Modificare le proprietà **nome**   e **Descrizione**   in *autore gruppo di raccolta regole AZFM* e *gli utenti in questo ruolo possono modificare i gruppi di insiemi di regole dei criteri firewall*

Il file JSON dovrebbe essere simile all'esempio seguente:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Per creare il nuovo ruolo personalizzato, usare il comando New-AzRoleDefinition e specificare il file di definizione del ruolo JSON. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare tutti i ruoli personalizzati, è possibile usare il comando Get-AzRoleDefinition:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

È anche possibile visualizzare i ruoli personalizzati nel portale di Azure. Passare alla sottoscrizione, selezionare **controllo di accesso (IAM)**, **ruoli**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Autorizzazione di lettura SalesAppPolicy":::

Per altre informazioni, vedere [esercitazione: creare un ruolo personalizzato di Azure usando Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Aggiungere utenti al ruolo personalizzato

Nel portale è possibile aggiungere utenti al ruolo autori del gruppo di raccolta regole AZFM e fornire l'accesso ai criteri del firewall.

1. Dal portale selezionare i criteri del firewall del team dell'applicazione (ad esempio, SalesAppPolicy).
2. Selezionare **controllo di accesso**.
3. Selezionare **Aggiungi un'assegnazione di ruolo**.
4. Aggiungere utenti/gruppi di utenti (ad esempio, il team di vendita) al ruolo.

Ripetere questa procedura per gli altri criteri del firewall.

### <a name="summary"></a>Riepilogo

Il criterio firewall con controllo degli accessi in base al ruolo ora fornisce l'accesso selettivo ai gruppi di raccolta regole

Gli utenti non hanno le autorizzazioni per:
- Eliminare i criteri firewall o firewall di Azure.
- Aggiornare la gerarchia dei criteri firewall o le impostazioni DNS o l'Intelligence per le minacce.
- Aggiornare i criteri del firewall in cui non sono membri del gruppo di autori del gruppo di raccolta regole AZFM.

Gli amministratori della sicurezza possono usare i criteri di base per applicare Guardrails e bloccare determinati tipi di traffico (ad esempio ICMP) richiesti dall'azienda. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [criteri del firewall di Azure](policy-overview.md).

