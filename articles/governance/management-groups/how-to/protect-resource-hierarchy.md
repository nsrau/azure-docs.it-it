---
title: Come proteggere la gerarchia di risorse - Governance di Azure
description: Informazioni su come proteggere la gerarchia di risorse con le impostazioni di gerarchia che includono l'impostazione del gruppo di gestione predefinito.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: cfaf7a732b3233536006009613cecd5aa1649dee
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951894"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Come proteggere la gerarchia di risorse

Le risorse, i gruppi di risorse, le sottoscrizioni, i gruppi di gestione e il tenant costituiscono collettivamente la gerarchia di risorse. Le impostazioni del gruppo di gestione radice, ad esempio i ruoli personalizzati di Azure o le assegnazioni dei criteri di criteri di Azure, possono avere un effetto su tutte le risorse nella gerarchia delle risorse. È importante proteggere la gerarchia di risorse da modifiche che potrebbero influire negativamente su tutte le risorse.

I gruppi di gestione dispongono ora di impostazioni di gerarchia che consentono all'amministratore tenant di controllare questi comportamenti. Questo articolo illustra tutte le impostazioni di gerarchia disponibili e spiega come impostarle.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Autorizzazioni RBAC di Azure per le impostazioni della gerarchia

Per configurare le impostazioni della gerarchia, è necessario che nel gruppo di gestione radice siano presenti due operazioni del provider di risorse seguenti:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Queste operazioni consentono a un solo utente di leggere e aggiornare le impostazioni di gerarchia. Le operazioni non forniscono nessun altro accesso alla gerarchia del gruppo di gestione o alle risorse nella gerarchia. Entrambe queste operazioni sono disponibili nell' **amministratore delle impostazioni della gerarchia**dei ruoli predefiniti di Azure.

## <a name="setting---default-management-group"></a>Impostazione - Gruppo di gestione predefinito

Per impostazione predefinita, viene aggiunta una nuova sottoscrizione in un tenant come membro del gruppo di gestione radice. Se le assegnazioni di criteri, il controllo degli accessi in base al ruolo di Azure e altri costrutti di governance vengono assegnati al gruppo di gestione radice, avranno immediatamente effetto sulle nuove sottoscrizioni. Per questo motivo, molte organizzazioni non applicano questi costrutti al gruppo di gestione radice, anche se si tratta della posizione desiderata per assegnarli. In altri casi, per le nuove sottoscrizioni è necessario un set di controlli più restrittivo, che non deve tuttavia essere assegnato a tutte le sottoscrizioni. Questa impostazione supporta entrambi i casi d'uso.

Consentendo di definire il gruppo di gestione predefinito per le nuove sottoscrizioni, è possibile applicare costrutti di governance a livello di organizzazione al gruppo di gestione radice e un gruppo di gestione separato con assegnazioni di criteri o assegnazioni di ruolo di Azure più adatte a una nuova sottoscrizione.

### <a name="set-default-management-group-in-portal"></a>Impostare il gruppo di gestione predefinito nel portale

Per configurare questa impostazione in portale di Azure, attenersi alla procedura seguente:

1. Utilizzare la barra di ricerca per cercare e selezionare "gruppi di gestione".

1. Nel gruppo di gestione radice selezionare **Dettagli** accanto al nome del gruppo di gestione.

1. In **Impostazioni**selezionare **Impostazioni gerarchia**.

1. Selezionare il pulsante **modifica gruppo di gestione predefinito** .

   > [!NOTE]
   > Se il pulsante **modifica gruppo di gestione predefinito** è disabilitato, il gruppo di gestione visualizzato non è il gruppo di gestione radice oppure l'entità di sicurezza non dispone delle autorizzazioni necessarie per modificare le impostazioni della gerarchia.

1. Selezionare un gruppo di gestione dalla gerarchia e usare il pulsante **Seleziona** .

### <a name="set-default-management-group-with-rest-api"></a>Impostare un gruppo di gestione predefinito con l'API REST

Per configurare questa impostazione con l'API REST, viene chiamato l'endpoint [delle impostazioni della gerarchia](/rest/api/resources/hierarchysettings) . A tale scopo, usare il formato di URI DELL'API REST e di richiesta seguente. Sostituire `{rootMgID}` con l'ID del gruppo di gestione radice e `{defaultGroupID}` con l'ID del gruppo di gestione che deve diventare il gruppo di gestione predefinito:

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Request Body

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Per impostare di nuovo il gruppo di gestione radice come gruppo di gestione predefinito, usare lo stesso endpoint e impostare **defaultManagementGroup** sul valore `/providers/Microsoft.Management/managementGroups/{rootMgID}`.

## <a name="setting---require-authorization"></a>Impostazione - Autorizzazione di richiesta

Per impostazione predefinita, qualsiasi utente può creare nuovi gruppi di gestione all'interno di un tenant. Gli amministratori di un tenant possono voler fornire queste autorizzazioni solo a utenti specifici per mantenere la coerenza e la conformità nella gerarchia del gruppo di gestione. Se abilitata, un utente richiede l'operazione `Microsoft.Management/managementGroups/write` sul gruppo di gestione radice per creare nuovi gruppi di gestione figlio.

### <a name="set-require-authorization-in-portal"></a>Impostare Richiedi autorizzazione nel portale

Per configurare questa impostazione in portale di Azure, attenersi alla procedura seguente:

1. Utilizzare la barra di ricerca per cercare e selezionare "gruppi di gestione".

1. Nel gruppo di gestione radice selezionare **Dettagli** accanto al nome del gruppo di gestione.

1. In **Impostazioni**selezionare **Impostazioni gerarchia**.

1. Abilitare o disabilitare le **autorizzazioni Richiedi per la creazione di nuovi gruppi di gestione.** opzione su on.

   > [!NOTE]
   > Se sono necessarie le **autorizzazioni per la creazione di nuovi gruppi di gestione.** l'interruttore è disabilitato, il gruppo di gestione visualizzato non è il gruppo di gestione radice oppure l'entità di sicurezza non ha le autorizzazioni necessarie per modificare le impostazioni della gerarchia.

### <a name="set-require-authorization-with-rest-api"></a>Impostare Richiedi autorizzazione con l'API REST

Per configurare questa impostazione con l'API REST, viene chiamato l'endpoint [delle impostazioni della gerarchia](/rest/api/resources/hierarchysettings) . A tale scopo, usare il formato di URI DELL'API REST e di richiesta seguente. Si tratta di un valore _booleano_, è quindi necessario specificare **true** o **false**. Il valore **true** abilita questo metodo di protezione della gerarchia del gruppo di gestione:

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Request Body

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Per disattivare l'impostazione, usare lo stesso endpoint e impostare **requireAuthorizationForGroupCreation** sul valore **false**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](../create-management-group-portal.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](../manage.md)