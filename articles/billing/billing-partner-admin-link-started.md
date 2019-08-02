---
title: Collegare un account Azure a un ID partner | Microsoft Docs
description: Tener traccia degli engagement con i clienti di Azure collegando un ID partner all'account utente usato per gestire le risorse del cliente.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706399"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Collegare un ID partner agli account Azure

I partner Microsoft offrono servizi che aiutano i clienti a raggiungere obiettivi aziendali e Mission usando i prodotti Microsoft. Quando agisce per conto del cliente che gestisce, configura e supporta i servizi di Azure, gli utenti partner dovranno accedere all'ambiente del cliente. Utilizzando il collegamento amministratore partner, i partner possono associare l'ID di rete del partner alle credenziali utilizzate per la distribuzione dei servizi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Ottenere l'accesso dal cliente

Prima di collegare l'ID partner, il cliente deve consentire l'accesso alle risorse di Azure tramite una delle opzioni seguenti:

- **Utente guest**: il cliente può consentire l'accesso come utente guest e assegnare uno dei ruoli Controllo degli accessi in base al ruolo. Per altre informazioni, vedere [Aggiungere utenti guest da un'altra directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Account directory**: il cliente può creare un account utente nella propria directory e assegnare ruoli RBAC.

- **Entità servizio**: il cliente può aggiungere un'app o uno script dell'organizzazione nella propria directory e assegnare uno dei ruoli RBAC. L'identità dell'app o dello script viene definita entità servizio.

## <a name="link-to-a-partner-id"></a>Collegamento a un ID partner

Quando si ha accesso alle risorse del cliente, usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per collegare l'ID Microsoft Partner Network (ID MPN) all'ID utente o all'entità servizio. Collegare l'ID partner in ogni tenant del cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Usare il portale di Azure per creare il collegamento a un nuovo ID partner

1. Passare a [Collega a un ID partner](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) nel portale di Azure.

2. Accedere al portale di Azure.

3. Immettere l'ID partner Microsoft. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione.

   ![Screenshot che mostra Collega a un ID partner](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Per collegare un ID partner per un altro cliente, cambiare la directory. In **Cambia directory** selezionare la directory.

   ![Screenshot che mostra Cambia directory](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Usare PowerShell per creare il collegamento a un nuovo ID partner

1. Installare il modulo di PowerShell [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

2. Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Sign in with PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) (Accedere con PowerShell).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Ottenere l'ID partner collegato
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aggiornare l'ID partner collegato
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Eliminare l'ID partner collegato
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Usare l'interfaccia della riga di comando di Azure per creare il collegamento a un nuovo ID partner
1. Installare l'estensione dell'interfaccia della riga di comando di Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Ottenere l'ID partner collegato
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aggiornare l'ID partner collegato
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Eliminare l'ID partner collegato
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Passaggi successivi

Partecipare alla discussione [Community Partner Microsoft](https://aka.ms/PALdiscussion) per ricevere aggiornamenti o inviare commenti.

## <a name="frequently-asked-questions"></a>Domande frequenti

**Chi può collegare l'ID partner?**

Tutti gli utenti dell'organizzazione partner che gestiscono le risorse di Azure del cliente possono collegare l'ID partner all'account.

**Un ID partner può essere modificato dopo essere stato collegato?**

Sì. Un ID partner collegato può essere modificato, aggiunto o rimosso.

**Cosa accade se un utente ha un account in più di un tenant del cliente?**

Il collegamento tra l'ID partner e l'account viene eseguito per ogni tenant del cliente. Collegare l'ID partner in ogni tenant del cliente.

**Altri partner o clienti possono modificare o rimuovere il collegamento all'ID partner?**

Il collegamento è associato a livello di account utente. Solo il titolare dell'account può modificare o rimuovere il collegamento all'ID partner. Il cliente e altri partner non possono modificare il collegamento all'ID partner.


**Quale ID MPN è necessario usare se la mia azienda ha più di?**

Per collegare l'ID partner, è necessario usare gli account location partner e gli ID MPN associati.  Altre informazioni sugli [account partner](https://docs.microsoft.com/partner-center/account-structure)

**Dove è possibile trovare il reporting dei ricavi influenzato per l'ID partner collegato?**

La creazione di report sulle prestazioni del prodotto Cloud è disponibile per [i](https://partner.microsoft.com/membership/reports/myinsights)partner nel centro per i partner del dashboard Insights. È necessario selezionare il collegamento amministratore partner come tipo di associazione partner.

**Perché non è possibile visualizzare il cliente nei report?**

Non è possibile visualizzare il cliente nei report a causa dei motivi seguenti

1. L'account utente collegato non ha [accesso basato sui ruoli](https://docs.microsoft.com/azure/role-based-access-control/overview) per nessuna sottoscrizione o risorsa di Azure del cliente.

2. La sottoscrizione di Azure in cui l'utente dispone di accesso in [base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) non ha alcun uso.

**Link ID partner funziona con Azure Stack?**

Sì, è possibile collegare l'ID partner per Azure Stack.
