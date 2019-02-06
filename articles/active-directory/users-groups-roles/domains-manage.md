---
title: Gestione dei nomi di dominio personalizzati in Azure Active Directory | Microsoft Docs
description: Concetti relativi alla gestione e procedure dettagliate per gestire un nome di dominio in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/29/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: 12c8b80b76c721962ca6180e531578b037997553
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55294166"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gestione dei nomi di dominio personalizzati in Azure Active Directory

Un nome di dominio è una parte importante dell'identificatore per numerose risorse di directory: è parte del nome utente o dell'indirizzo di posta elettronica per un utente, dell'indirizzo per un gruppo e talvolta dell'URI ID app per un'applicazione. Una risorsa in Azure Active Directory (Azure AD) può includere un nome di dominio di proprietà della directory in cui è contenuta. I domini in Azure AD possono essere gestiti solo da un amministratore globale.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Impostare il nome di dominio primario per la directory di Azure AD

Quando viene creata la directory, il nome di dominio iniziale, ad esempio "contoso.onmicrosoft.com", è anche il nome di dominio primario. Il dominio primario è il nome di dominio predefinito per un nuovo utente quando si crea un nuovo utente. Impostare un nome di dominio primario semplifica il processo di creazione di nuovi utenti nel portale da parte degli amministratori. Per cambiare il nome di dominio primario:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.
2. Selezionare **Azure Active Directory**.
3. Selezionare **Nomi di dominio personalizzati**.
  
   ![Apertura di Gestione utenti](./media/domains-manage/add-custom-domain.png)
4. Selezionare il nome del dominio che si vuole impostare come dominio primario.
5. Selezionare il comando **Imposta come primario**. Confermare la scelta quando viene richiesto.
  
   ![Rendere primario un nome di dominio](./media/domains-manage/make-primary-domain.png)

È possibile modificare il nome di dominio primario per la directory impostando qualsiasi dominio personalizzato verificato e non federato. La modifica del dominio primario per la directory non modificherà i nomi utente degli utenti esistenti.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Aggiungere nomi di dominio personalizzati al tenant di Azure AD

È possibile aggiungere fino a 900 nomi di dominio gestiti. Se si configurano tutti i domini per la federazione con un'istanza locale di Active Directory, è possibile aggiungere fino a 450 nomi di dominio in ogni directory.

## <a name="add-subdomains-of-a-custom-domain"></a>Aggiungere sottodomini di un dominio personalizzato

Per aggiungere un nome di dominio di terzo livello, ad esempio 'europe.contoso.com' alla directory, è prima necessario aggiungere e verificare il dominio di secondo livello, ovvero contoso.com. Il sottodominio viene verificato automaticamente da Azure AD. Per controllare che il sottodominio aggiunto sia verificato, aggiornare l'elenco dei domini nel browser.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato

Se si modificano i registrar DNS, non sono necessarie attività di configurazione aggiuntive in Azure AD. Si può continuare a usare il nome di dominio con Azure AD senza interruzioni. Se si usa il nome di dominio personalizzato con Office 365, Intune o altri servizi che si basano su nomi di dominio personalizzati in Azure AD, vedere la documentazione per tali servizi.

## <a name="delete-a-custom-domain-name"></a>Eliminare un nome di dominio personalizzato

È possibile eliminare da Azure AD i nomi di dominio personalizzati che l'organizzazione non usa più o che vuole usare in un'altra directory di Azure AD.

Per eliminare un nome di dominio personalizzato, è prima necessario assicurarsi che nella directory non siano presenti risorse che si basano sul nome di dominio. Non è possibile eliminare un nome di dominio dalla directory nei casi seguenti:

* Sono presenti utenti con un nome utente, un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.
* Sono presenti gruppi con un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.
* In Azure AD sono presenti applicazioni con un URI di ID app che include il nome di dominio.

È necessario modificare o eliminare tali risorse dalla directory di Azure AD prima di poter eliminare il nome di dominio personalizzato.

### <a name="forcedelete-option"></a>Opzione ForceDelete

È possibile eseguire l'operazione **ForceDelete** su un nome di dominio nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) oppure usando l'[API Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/domain_forcedelete). Tali opzioni usano un'operazione asincrona e aggiornano tutti i riferimenti del nome di dominio personalizzato, come "user@contoso.com", con il nome di dominio predefinito iniziale, come "user@contoso.onmicrosoft.com". 

Per chiamare **ForceDelete** nel portale di Azure, è necessario verificare che siano presenti meno di 1000 riferimenti al nome di dominio e aggiornare o rimuovere nell'[interfaccia di amministrazione di Exchange](https://outlook.office365.com/ecp/) tutti i riferimenti in cui il servizio di provisioning è Exchange. Ciò include gli elenchi distribuiti e i gruppi di sicurezza abilitati alla posta elettronica di Exchange. Per altre informazioni, vedere l'articolo relativo alla [rimozione dei gruppi di sicurezza abilitati alla posta elettronica](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). L'operazione **ForceDelete** non riesce, inoltre, in presenza di una delle condizioni seguenti:

* Si è acquistato un dominio tramite servizi di sottoscrizione di domini di Office 365
* Si amministra come partner per conto del tenant di un altro cliente

L'operazione **ForceDelete** include le azioni seguenti:

* Rinomina il nome dell'entità utente, l'indirizzo di posta elettronica e l'indirizzo proxy degli utenti con riferimenti al nome di dominio personalizzato usando il nome di dominio predefinito iniziale.
* Rinomina l'indirizzo di posta elettronica dei gruppi con riferimenti al nome di dominio personalizzato usando il nome di dominio predefinito iniziale.
* Rinomina gli URI identificatore delle applicazioni con riferimenti al nome di dominio personalizzato usando il nome di dominio predefinito iniziale.

Viene restituito un errore nei casi seguenti:

* Il numero di oggetti da rinominare è superiore a 1000
* Una delle applicazioni da rinominare è un'app multi-tenant

### <a name="frequently-asked-questions"></a>Domande frequenti

**D: Perché l'eliminazione del dominio non riesce e genera un errore che indica la presenza di gruppi gestiti da Exchange per il nome di dominio?** <br>
**R:** Attualmente, il provisioning di determinati gruppi, come gli elenchi distribuiti e i gruppi di sicurezza abilitati alla posta elettronica, viene effettuato da Exchange ed è necessario eseguirne manualmente la pulizia nell'[interfaccia di amministrazione di Exchange](https://outlook.office365.com/ecp/). Potrebbero essere presenti indirizzi proxy residui che si basano sul nome di dominio personalizzato e che dovranno essere aggiornati manualmente con un altro nome di dominio. 

**D: Perché, dopo aver eseguito l'accesso come admin@contoso.com, non è possibile eliminare il nome di dominio "contoso.com"?**<br>
**R:** Non è possibile eliminare un nome di dominio personalizzato cui viene fatto riferimento nel nome dell'account utente in uso. Assicurarsi che l'account amministratore globale usi il nome di dominio predefinito iniziale ".onmicrosoft.com", ad esempio admin@contoso.onmicrosoft.com. Accedere con un diverso account amministratore globale, ad esempio admin@contoso.onmicrosoft.com, o con un altro nome di dominio personalizzato come "fabrikam.com", per il quale l'account è admin@fabrikam.com.

**D: Dopo aver fatto clic sul pulsante Elimina dominio, lo stato visualizzato per l'operazione di eliminazione è `In Progress`. Quanto tempo occorre? Che cosa accade se non riesce?**<br>
**R:** L'operazione di eliminazione di un dominio è un attività asincrona in background che rinomina tutti i riferimenti al nome di dominio. Il completamento dovrebbe richiedere un minuto o due. Se l'eliminazione del dominio non riesce, verificare che non sia presente quanto segue:

* App configurate per il nome di dominio con l'URI identificatore dell'app
* Gruppi abilitati alla posta elettronica che fanno riferimento al nome di dominio personalizzato
* Più di 1000 riferimenti al nome di dominio

Se si riscontra che una o più condizioni non sono state soddisfatte, eseguire la pulizia dei riferimenti manualmente e provare a eliminare di nuovo il dominio.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usare API Graph o PowerShell per gestire i nomi di dominio

La maggior parte delle attività di gestione per i nomi di dominio in Azure Active Directory può anche essere completata usando Microsoft PowerShell oppure a livello di codice usando l'API Graph di Azure AD.

* [Uso di PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Uso dell'API Graph per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere nomi di dominio personalizzati](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Rimuovere i gruppi di sicurezza abilitati alla posta elettronica di Exchange nell'interfaccia di amministrazione di Exchange per un nome di dominio personalizzato in Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Eseguire l'operazione ForceDelete su un nome di dominio personalizzato con l'API Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/domain_forcedelete)
