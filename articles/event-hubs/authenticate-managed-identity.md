---
title: Autenticazione di un'identità gestita con Azure Active Directory
description: Questo articolo fornisce informazioni sull'autenticazione di un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi di Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992835"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi
Hub eventi di Azure supporta l'autenticazione Azure Active Directory (Azure AD) con [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso alle risorse di hub eventi usando Azure AD credenziali di applicazioni in esecuzione in macchine virtuali (VM) di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.

Questo articolo illustra come autorizzare l'accesso a un hub eventi usando un'identità gestita da una macchina virtuale di Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale
Prima di poter usare le identità gestite per le risorse di Azure per autorizzare le risorse di hub eventi dalla macchina virtuale, è prima necessario abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Portale di Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concedere le autorizzazioni a un'identità gestita in Azure AD
Per autorizzare una richiesta al servizio Hub eventi da un'identità gestita nell'applicazione, configurare prima le impostazioni di controllo degli accessi in base al ruolo (RBAC) per tale identità gestita. Hub eventi di Azure definisce i ruoli RBAC che includono le autorizzazioni per l'invio e la lettura da Hub eventi. Quando il ruolo RBAC viene assegnato a un'identità gestita, all'identità gestita viene concesso l'accesso ai dati di hub eventi nell'ambito appropriato.

Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [eseguire l'autenticazione con Azure Active Directory per l'accesso alle risorse di hub eventi](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Usare hub eventi con identità gestite
Per usare gli hub eventi con le identità gestite, è necessario assegnare all'identità il ruolo e l'ambito appropriato. La procedura descritta in questa sezione usa un'applicazione semplice che viene eseguita con un'identità gestita e accede alle risorse di hub eventi.

Qui viene usata un'applicazione Web di esempio ospitata nel [servizio app Azure](https://azure.microsoft.com/services/app-service/). Per istruzioni dettagliate per la creazione di un'applicazione Web, vedere [creare un'app web ASP.NET Core in Azure](../app-service/app-service-web-get-started-dotnet.md)

Una volta creata l'applicazione, attenersi alla procedura seguente: 

1. Passare a **Impostazioni** e selezionare **Identity (identità**). 
1. Selezionare lo **stato** **su on**. 
1. Selezionare **Salva** per salvare l'impostazione. 

    ![Identità gestita per un'app Web](./media/authenticate-managed-identity/identity-web-app.png)

Una volta abilitata questa impostazione, viene creata una nuova identità del servizio nel Azure Active Directory (Azure AD) e configurata nell'host del servizio app.

A questo punto, assegnare l'identità del servizio a un ruolo nell'ambito necessario nelle risorse di hub eventi.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Per assegnare i ruoli RBAC usando il portale di Azure
Per assegnare un ruolo alle risorse di hub eventi, passare a tale risorsa nel portale di Azure. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> La procedura seguente assegna un ruolo di identità del servizio agli spazi dei nomi di hub eventi. È possibile seguire la stessa procedura per assegnare un ruolo con ambito a qualsiasi risorsa di hub eventi. 

1. Nella portale di Azure passare allo spazio dei nomi di hub eventi e visualizzare la **Panoramica** per lo spazio dei nomi. 
1. Selezionare **controllo di accesso (IAM)** nel menu a sinistra per visualizzare le impostazioni di controllo di accesso per l'hub eventi.
1.  Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
3.  Selezionare **Aggiungi** per aggiungere un nuovo ruolo.
4.  Nella pagina **Aggiungi assegnazione ruolo** selezionare i ruoli di hub eventi che si desidera assegnare. Quindi cercare per individuare l'identità del servizio registrata per l'assegnazione del ruolo.
    
    ![Pagina Aggiungi assegnazione ruolo](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selezionare **Salva**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che l'identità del servizio ha il proprietario dei dati di hub eventi.
    
    ![Identità assegnata a un ruolo](./media/authenticate-managed-identity/role-assigned.png)

Dopo aver assegnato il ruolo, l'applicazione Web avrà accesso alle risorse di hub eventi nell'ambito definito. 

### <a name="test-the-web-application"></a>Testare l'applicazione Web
È ora possibile avviare l'applicazione Web e puntare il browser alla pagina aspx di esempio. È possibile trovare l'applicazione Web di esempio che invia e riceve dati dalle risorse di hub eventi nel [repository GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installare il pacchetto più recente da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e iniziare a inviare e ricevere dati da Hub eventi usando EventHubClient, come illustrato nel codice seguente: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Passaggi successivi
- Scaricare l' [esempio](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) da GitHub.
- Per informazioni sulle identità gestite per le risorse di Azure, vedere l'articolo seguente: [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
- Vedere gli articoli correlati seguenti:
    - [Autenticare le richieste a hub eventi di Azure da un'applicazione usando Azure Active Directory](authenticate-application.md)
    - [Autenticare le richieste a hub eventi di Azure usando le firme di accesso condiviso](authenticate-shared-access-signature.md)
    - [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizzare l'accesso alle risorse di hub eventi usando le firme di accesso condiviso](authorize-access-shared-access-signature.md)