---
title: Collegare account Azure all'ID partner | Microsoft Docs
description: Tenere traccia degli accordi con i clienti Azure tramite il collegamento dell'ID partner all'account utente usato per gestire le risorse del cliente.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a48298668e2297cb95f2a2f16eac6387ff509781
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608713"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Collegare l'ID partner agli account Azure

Il partner può rilevare l'impatto sugli accordi del cliente mediante il collegamento dell'ID partner agli account usati per la gestione delle risorse del cliente.

Questa funzionalità è disponibile come anteprima pubblica.

## <a name="get-access-from-your-customer"></a>Ottenere l'accesso dal cliente

Prima di collegare l'ID partner, il cliente deve consentire l'accesso alle risorse di Azure tramite una delle opzioni seguenti:

- **Utente guest:** il cliente può consentire l'accesso come utente guest e assegnare uno dei ruoli RBAC. Per altre informazioni, vedere [Aggiungere utenti guest da un'altra directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Account di directory:** il cliente può creare un nuovo utente dell'organizzazione nella propria directory e assegnare uno dei ruoli RBAC.

- **Entità servizio:** il cliente può aggiungere un'app o uno script dell'organizzazione nella propria directory e assegnare uno dei ruoli RBAC. L'identità dell'app o dello script viene definita entità servizio.

## <a name="link-partner-id"></a>Collegare l'ID partner

Quando si ha accesso alle risorse del cliente, usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per collegare l'ID Microsoft Partner Network (ID MPN) all'ID utente o all'entità servizio. È necessario collegare l'ID partner in ogni tenant del cliente.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Usare il portale di Azure per collegare un nuovo ID partner

1. Passare al [collegamento a un ID partner](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) nel portale di Azure.

2. Accedere al portale di Azure.

3. Immettere l'ID partner Microsoft. L'ID partner è l'ID [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) dell'organizzazione.

  ![Schermata che mostra l'ID partner del collegamento](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Per collegare l'ID partner per un altro cliente, usare la selezione directory. In Cambia directory, scegliere la directory.

  ![Schermata che mostra l'ID partner del collegamento](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Usare PowerShell per collegare un nuovo ID partner

1. Installare il modulo [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) di PowerShell.

2. Accedere al tenant del cliente con l'account utente o con l'entità servizio. Per altre informazioni, vedere [Accedere ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Collegare il nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) dell'organizzazione.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Ottenere l'ID partner collegato
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Aggiornare l'ID partner collegato
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Eliminare l'ID partner collegato
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Usare l'interfaccia della riga di comando per collegare un nuovo ID partner
1.  Installare l'estensione dell'interfaccia della riga di comando.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Accedere con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Collegare il nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) dell'organizzazione.

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

Tutti gli utenti dell'organizzazione partner che gestiscono le risorse del cliente possono collegare l'ID partner all'account.

**Dopo aver collegato un ID partner è possibile modificarlo?**

Sì, l'ID partner collegato può essere modificato, aggiunto o rimosso.

**Cosa accade se un utente dispone di un account in più tenant del cliente?**

Il collegamento tra l'ID partner e l'account viene eseguito per ogni tenant del cliente.  È necessario collegare l'ID partner in ogni tenant del cliente.

**Altri partner o clienti possono modificare o rimuovere il collegamento all'ID partner?**

Il collegamento è associato a livello di account. Solo il titolare dell'account può modificare o rimuovere il collegamento all'ID partner. Il cliente e altri partner non possono modificare il collegamento all'ID partner. 
