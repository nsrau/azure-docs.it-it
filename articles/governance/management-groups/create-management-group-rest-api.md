---
title: "Avvio rapido: Creare un gruppo di gestione con l'API REST"
description: In questo argomento di avvio rapido si usa l'API REST per creare un gruppo di gestione con cui organizzare le risorse in una gerarchia.
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: b19fddf8215a1b133254c2a31bbea568a315f721
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237001"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Avvio rapido: Creare un gruppo di gestione con l'API REST

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API REST basate su Azure Resource Manager. In alternativa è possibile usare la funzionalità "Prova" nella documentazione di REST o strumenti come [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) di PowerShell o [Postman](https://www.postman.com).

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Creazione nell'API REST

Per l'API REST, usare l'endpoint [Gruppi di gestione - Crea o aggiorna](/rest/api/resources/managementgroups/createorupdate) per creare un nuovo gruppo di gestione. In questo esempio il valore di **groupId** del gruppo di gestione è _Contoso_.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Nessun corpo della richiesta

Il valore di **groupId** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere cambiato in seguito.

Se si vuole visualizzare un nome diverso per il gruppo di gestione all'interno del portale di Azure, aggiungere la proprietà **properties.displayName** nel corpo della richiesta. Ad esempio, per creare un gruppo di gestione con **groupId** _Contoso_ e nome visualizzato _Contoso Group_, usare l'endpoint e il corpo della richiesta seguenti:

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Request Body

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

Negli esempi precedenti il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come elemento padre, usare la proprietà **properties.parent.id**.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Request Body

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di gestione creato in precedenza, usare l'endpoint [Gruppi di gestione - Elimina](/rest/api/resources/managementgroups/delete):

- URI DELL'API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Nessun corpo della richiesta

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> [Gestire le risorse con i gruppi di gestione](./manage.md)