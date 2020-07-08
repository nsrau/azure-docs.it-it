---
title: Come proteggere la gerarchia di risorse - Governance di Azure
description: Informazioni su come proteggere la gerarchia di risorse con le impostazioni di gerarchia che includono l'impostazione del gruppo di gestione predefinito.
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: 60c184d176ae62c1af525db656c56a83422cb94a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837350"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Come proteggere la gerarchia di risorse

Le risorse, i gruppi di risorse, le sottoscrizioni, i gruppi di gestione e il tenant costituiscono collettivamente la gerarchia di risorse. Le impostazioni del gruppo di gestione radice, ad esempio i ruoli Controllo degli accessi in base al ruolo personalizzati o le assegnazioni dei criteri di Criteri di Azure, possono influire su ogni risorsa della gerarchia di risorse. È importante proteggere la gerarchia di risorse da modifiche che potrebbero influire negativamente su tutte le risorse.

I gruppi di gestione dispongono ora di impostazioni di gerarchia che consentono all'amministratore tenant di controllare questi comportamenti. Questo articolo illustra tutte le impostazioni di gerarchia disponibili e spiega come impostarle.

## <a name="rbac-permissions-for-hierarchy-settings"></a>Autorizzazioni di Controllo degli accessi in base al ruolo per le impostazioni di gerarchia

Per configurare una delle impostazioni di gerarchia è necessarie eseguire le due operazioni di Controllo degli accessi in base al ruolo seguenti sul gruppo di gestione radice:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Queste operazioni consentono a un solo utente di leggere e aggiornare le impostazioni di gerarchia. Le operazioni non forniscono nessun altro accesso alla gerarchia del gruppo di gestione o alle risorse nella gerarchia. Entrambe le operazioni sono disponibili nel ruolo Controllo degli accessi in base al ruolo di **amministratore delle impostazioni di gerarchia** predefinito.

## <a name="setting---default-management-group"></a>Impostazione - Gruppo di gestione predefinito

Per impostazione predefinita, viene aggiunta una nuova sottoscrizione in un tenant come membro del gruppo di gestione radice. Se le assegnazioni di criteri, il controllo degli accessi in base al ruolo e altri costrutti di governance vengono assegnati al gruppo di gestione radice, avranno immediatamente effetto sulle nuove sottoscrizioni. Per questo motivo, molte organizzazioni non applicano questi costrutti al gruppo di gestione radice, anche se si tratta della posizione desiderata per assegnarli. In altri casi, per le nuove sottoscrizioni è necessario un set di controlli più restrittivo, che non deve tuttavia essere assegnato a tutte le sottoscrizioni. Questa impostazione supporta entrambi i casi d'uso.

Consentendo di definire il gruppo di gestione predefinito per le nuove sottoscrizioni, i costrutti di governance a livello di organizzazione possono essere applicati al gruppo di gestione radice ed è possibile definire un gruppo di gestione separato con assegnazioni di criteri o assegnazioni di Controllo degli accessi in base al ruolo più adatte.

Per configurare questa impostazione, viene chiamato l'endpoint dell'API REST [Impostazioni gerarchia](/rest/api/resources/hierarchysettings). A tale scopo, usare il formato di URI DELL'API REST e di richiesta seguente. Sostituire `{rootMgID}` con l'ID del gruppo di gestione radice e `{defaultGroupID}` con l'ID del gruppo di gestione che deve diventare il gruppo di gestione predefinito:

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

Per configurare questa impostazione, viene chiamato l'endpoint dell'API REST [Impostazioni gerarchia](/rest/api/resources/hierarchysettings). A tale scopo, usare il formato di URI DELL'API REST e di richiesta seguente. Si tratta di un valore _booleano_, è quindi necessario specificare **true** o **false**. Il valore **true** abilita questo metodo di protezione della gerarchia del gruppo di gestione:

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

- [Creare gruppi di gestione per organizzare le risorse di Azure](../create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](../manage.md)
