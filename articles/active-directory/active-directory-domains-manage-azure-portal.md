---
title: Gestione dei nomi di dominio personalizzati in Azure Active Directory | Microsoft Docs
description: Concetti relativi alla gestione e procedure dettagliate per gestire un nome di dominio in Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand;jeffsta
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 2f5a41907e9fa304444b369311e182d3f00ef619
ms.contentlocale: it-it
ms.lasthandoff: 09/26/2017

---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gestione dei nomi di dominio personalizzati in Azure Active Directory
Un nome di dominio è una parte importante dell'identificatore per numerose risorse directory: è parte di un nome utente o di un indirizzo di posta elettronica per un utente, parte dell'indirizzo per un gruppo e può essere parte dell'URI dell'ID app per un'applicazione. Una risorsa in Azure Active Directory (Azure AD) può includere un nome di dominio già verificato come di proprietà della directory contenente il servizio. Solo un amministratore globale può eseguire attività di gestione del dominio in Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Impostare il nome di dominio primario per la directory di Azure AD
Quando viene creata la directory, il nome di dominio iniziale, ad esempio "contoso.onmicrosoft.com", è anche il nome di dominio primario. Il dominio primario è il nome di dominio predefinito per un nuovo utente quando si crea un nuovo utente. Impostare un nome di dominio primario semplifica il processo di creazione di nuovi utenti nel portale da parte degli amministratori. Per cambiare il nome di dominio primario:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.
   
   ![Apertura di Gestione utenti](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Nel pannello ***nome directory*** selezionare **Nomi di dominio**.
4. Nel pannello ***nome directory* - Nomi di dominio** selezionare il nome di dominio che si vuole rendere il nome primario.
5. Nel pannello del ***nome di dominio***, ossia nel pannello che viene visualizzato con il nuovo nome di dominio nel titolo, selezionare il comando **Imposta come primario**. Confermare la scelta quando viene richiesto.
   
   ![Rendere primario un nome di dominio](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Per il nome di dominio primario per la directory è possibile impostare qualsiasi dominio personalizzato verificato che non sia federato. La modifica del dominio primario per la directory non comporta la modifica dei nomi utente per gli utenti esistenti.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Aggiungere nomi di dominio personalizzati alla directory di Azure AD
> È possibile aggiungere fino a un massimo di 900 nomi di dominio gestiti. Se si intende configurare tutti i domini per la federazione con l'istanza locale di Active Directory, è possibile aggiungere fino a un massimo di 450 nomi di dominio in ogni directory. Per altre informazioni, vedere [Nomi di dominio federati e gestiti](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Aggiungere sottodomini di un dominio personalizzato
Per aggiungere un nome di dominio di terzo livello, ad esempio 'europe.contoso.com' alla directory, è prima necessario aggiungere e verificare il dominio di secondo livello, ovvero contoso.com. Il sottodominio verrà automaticamente verificato da Azure AD. Per ottenere la conferma dell'avvenuta verifica del dominio appena aggiunto, aggiornare la pagina del browser in cui sono visualizzati i domini.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato
Se si modifica il registrar DNS per il nome di dominio personalizzato, è possibile continuare a usare il nome di dominio personalizzato in Azure AD senza interruzioni e senza ulteriori attività di configurazione. Se si usa il nome di dominio personalizzato con Office 365, Intune o altri servizi che si basano sui nomi di dominio personalizzati in Azure AD, vedere la documentazione per tali servizi.

## <a name="delete-a-custom-domain-name"></a>Eliminare un nome di dominio personalizzato
È possibile eliminare da Azure AD i nomi di dominio personalizzati che l'organizzazione non usa più o che vuole usare in un'altra directory di Azure AD.

Per eliminare un nome di dominio personalizzato, è prima necessario assicurarsi che nella directory non siano presenti risorse che si basano sul nome di dominio. Non è possibile eliminare un nome di dominio dalla directory nei casi seguenti:

* Sono presenti utenti con un nome utente, un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.
* Sono presenti gruppi con un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.
* In Azure AD sono presenti applicazioni con un URI di ID app che include il nome di dominio.

È necessario modificare o eliminare tali risorse dalla directory di Azure AD prima di poter eliminare il nome di dominio personalizzato.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usare API Graph o PowerShell per gestire i nomi di dominio
La maggior parte delle attività di gestione per i nomi di dominio in Azure Active Directory può anche essere completata usando Microsoft PowerShell oppure a livello di codice usando l'API Graph di Azure AD.

* [Uso di PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Uso dell'API Graph per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere nomi di dominio personalizzati](add-custom-domain.md)


