---
title: Identità gestite per le risorse di Azure con il bus di servizio di Azure | Microsoft Docs
description: Usare le identità gestite per le risorse di Azure con il bus di servizio di Azure
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: a671b2ddd3cfa1237b6d843369e78233960f1c14
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013138"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse del bus di servizio di Azure
Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) offrono una funzionalità per l'intera piattaforma Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client del bus di servizio in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per le identità gestite per le risorse di Azure abilitato non deve quindi gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi della messaggistica del bus di servizio. Quando l'app si connette, il bus di servizio associa il contesto dell'entità gestita al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo. Una volta eseguita l'associazione a un'identità gestita, il client del bus di servizio può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'entità gestita ai ruoli del bus di servizio. 

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a un'entità del bus di servizio, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0. 
 1. Successivamente, il token viene passato come parte di una richiesta al servizio del bus di servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2,0 in fase di esecuzione. Se un'applicazione è in esecuzione in un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app per le funzioni di Azure, può usare un'identità gestita per accedere alle risorse. Per informazioni su come autenticare le richieste effettuate da un'identità gestita al servizio del bus di servizio, vedere [autenticare l'accesso alle risorse del bus di servizio di Azure con Azure Active Directory e identità gestite per le risorse di Azure](service-bus-managed-service-identity.md). 

Il passaggio di autorizzazione richiede che uno o più ruoli RBAC siano assegnati all'entità di sicurezza. Il bus di servizio di Azure fornisce ruoli RBAC che includono i set di autorizzazioni per le risorse del bus di servizio. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. Per altre informazioni sull'assegnazione di ruoli RBAC al bus di servizio di Azure, vedere [ruoli RBAC predefiniti per il bus di servizio di Azure](#built-in-rbac-roles-for-azure-service-bus). 

Le applicazioni native e le applicazioni Web che effettuano richieste al bus di servizio possono anche autorizzare con Azure AD. Questo articolo illustra come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse del bus di servizio. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Assegnazione di ruoli RBAC per i diritti di accesso
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). Il bus di servizio di Azure definisce un set di ruoli RBAC predefiniti che comprende i set comuni di autorizzazioni usate per accedere alle entità del bus di servizio ed è anche possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo RBAC viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'ambito di accesso può essere limitato al livello di sottoscrizione, al gruppo di risorse o allo spazio dei nomi del bus di servizio. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un'identità gestita per le risorse di Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ruoli RBAC predefiniti per il bus di servizio di Azure
Per il bus di servizio di Azure, la gestione degli spazi dei nomi e di tutte le risorse correlate tramite il portale di Azure e l'API Gestione risorse di Azure è già protetto mediante il modello di *Controllo degli accessi in base al ruolo*. Azure fornisce i ruoli RBAC predefiniti seguenti per autorizzare l'accesso a uno spazio dei nomi del bus di servizio:

- [Proprietario dati del bus di servizio di Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Consente l'accesso ai dati allo spazio dei nomi del bus di servizio e alle relative entità (code, argomenti, sottoscrizioni e filtri)
- [Mittente dati del bus di servizio di Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Usare questo ruolo per concedere l'accesso di trasmissione allo spazio dei nomi del bus di servizio e alle relative entità.
- [Ricevitore di dati del bus di servizio di Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Usare questo ruolo per concedere la ricezione dell'accesso allo spazio dei nomi del bus di servizio e alle relative entità. 

## <a name="resource-scope"></a>Ambito delle risorse 
Prima di assegnare un ruolo di controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate stabiliscono che è sempre preferibile concedere solo l'ambito più ristretto possibile.

Nell'elenco seguente vengono descritti i livelli in cui è possibile definire l'ambito di accesso alle risorse del bus di servizio, a partire dall'ambito più restrittivo:

- **Coda**, **argomento**o **sottoscrizione**: L'assegnazione di ruolo si applica all'entità specifica del bus di servizio. Attualmente, il portale di Azure non supporta l'assegnazione di utenti/gruppi/identità gestite ai ruoli RBAC del bus di servizio a livello di sottoscrizione. 
- **Spazio dei nomi del bus di servizio**: L'assegnazione di ruolo si estende all'intera topologia del bus di servizio nello spazio dei nomi e al gruppo di consumer associato.
- **Gruppo di risorse**: L'assegnazione di ruolo si applica a tutte le risorse del bus di servizio nel gruppo di risorse.
- **Sottoscrizione** L'assegnazione di ruolo si applica a tutte le risorse del bus di servizio in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per la propagazione. 

Per ulteriori informazioni sulla definizione dei ruoli predefiniti, vedere informazioni sulle [definizioni](../role-based-access-control/role-definitions.md#management-and-data-operations)dei ruoli. Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere [creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale
Prima di poter usare le identità gestite per le risorse di Azure per autorizzare le risorse del bus di servizio dalla VM, è necessario innanzitutto abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Portale di Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concedere le autorizzazioni a un'identità gestita in Azure AD
Per autorizzare una richiesta al servizio Service Bus da un'identità gestita nell'applicazione, configurare prima le impostazioni di controllo degli accessi in base al ruolo (RBAC) per l'identità gestita. Il bus di servizio di Azure definisce i ruoli RBAC che includono le autorizzazioni per l'invio e la lettura dal bus di servizio. Quando il ruolo RBAC viene assegnato a un'identità gestita, all'identità gestita viene concesso l'accesso alle entità del bus di servizio nell'ambito appropriato.

Per ulteriori informazioni sull'assegnazione di ruoli RBAC, vedere [eseguire l'autenticazione e l'autorizzazione con Azure Active Directory per l'accesso alle risorse del bus di servizio](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usare il bus di servizio con le identità gestite per le risorse di Azure
Per usare il bus di servizio con le identità gestite, è necessario assegnare all'identità il ruolo e l'ambito appropriato. La procedura descritta in questa sezione usa un'applicazione semplice che viene eseguita con un'identità gestita e accede alle risorse del bus di servizio.

Qui viene usata un'applicazione Web di esempio ospitata nel [servizio app Azure](https://azure.microsoft.com/services/app-service/). Per istruzioni dettagliate per la creazione di un'applicazione Web, vedere [creare un'app web ASP.NET Core in Azure](../app-service/app-service-web-get-started-dotnet.md)

Una volta creata l'applicazione, attenersi alla procedura seguente: 

1. Passare a **Impostazioni** e selezionare **Identity (identità**). 
1. Selezionare lo **stato** **su on**. 
1. Selezionare **Salva** per salvare l'impostazione. 

    ![Identità gestita per un'app Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Una volta abilitata questa impostazione, viene creata una nuova identità del servizio nel Azure Active Directory (Azure AD) e configurata nell'host del servizio app.

A questo punto, assegnare l'identità del servizio a un ruolo nell'ambito necessario nelle risorse del bus di servizio.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Per assegnare i ruoli RBAC usando il portale di Azure
Per assegnare un ruolo a uno spazio dei nomi del bus di servizio, passare allo spazio dei nomi nel portale di Azure. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> La procedura seguente consente di assegnare un ruolo di identità del servizio agli spazi dei nomi del bus di servizio. È possibile seguire la stessa procedura per assegnare un ruolo ad altri ambiti supportati (gruppo di risorse e sottoscrizione). 
> 
> [Creare uno spazio dei nomi di messaggistica del bus di servizio](service-bus-create-namespace-portal.md) , se non è già presente. 

1. Nella portale di Azure passare allo spazio dei nomi del bus di servizio e visualizzare la **Panoramica** per lo spazio dei nomi. 
1. Selezionare **controllo di accesso (IAM)** nel menu a sinistra per visualizzare le impostazioni di controllo di accesso per lo spazio dei nomi del bus di servizio.
1.  Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
3.  Selezionare **Aggiungi** per aggiungere un nuovo ruolo.
4.  Nella pagina **Aggiungi assegnazione ruolo** selezionare i ruoli del bus di servizio di Azure che si desidera assegnare. Quindi cercare per individuare l'identità del servizio registrata per l'assegnazione del ruolo.
    
    ![Pagina Aggiungi assegnazione ruolo](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selezionare **Salva**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che l'identità del servizio ha il proprietario dei dati del bus di servizio di Azure.
    
    ![Identità assegnata a un ruolo](./media/service-bus-managed-service-identity/role-assigned.png)

Una volta assegnato il ruolo, l'applicazione Web avrà accesso alle entità del bus di servizio nell'ambito definito. 

### <a name="run-the-app"></a>Esecuzione dell'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

La pagina Default.aspx è la pagina di destinazione. Il codice è disponibile nel file Default.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione al bus di servizio per l'invio o la ricezione di messaggi.

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità gestita con la chiamata a `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();`. In questo modo, non ci sono segreti da conservare e usare. Il flusso del contesto dell'identità gestita nel bus di servizio e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token. Questo modello è più semplice rispetto all'uso della firma di accesso condiviso.

Dopo avere apportato queste modifiche, pubblicare ed eseguire l'applicazione. È possibile ottenere con facilità i dati di pubblicazione corretti scaricando e quindi importando un profilo di pubblicazione in Visual Studio:

![Recupera profilo di pubblicazione](./media/service-bus-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata. Fare quindi clic su **Invia** o **Ricevi**.


> [!NOTE]
> - L'identità gestita funziona solo all'interno dell'ambiente di Azure, nei servizi app, nelle macchine virtuali di Azure e nei set di scalabilità. Per le applicazioni .NET, la libreria Microsoft.Azure.Services.AppAuthentication, usata dal pacchetto NuGet del bus di servizio, rappresenta un'astrazione di questo protocollo e supporta un'esperienza di sviluppo locale. Questa libreria consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'interfaccia della riga di comando di Azure 2.0 o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Le identità gestite attualmente non funzionano con gli slot di distribuzione del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
