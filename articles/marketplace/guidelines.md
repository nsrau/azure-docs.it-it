---
title: Indicazioni per il server di pubblicazione di AppSource e Azure Marketplace | Azure
description: Indicazioni per editori di app e servizi per Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 4da6f5c4513477d1adbf4d7645a66de112eeab23
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307486"
---
# <a name="guidelines"></a>Indicazioni  

<!--
## Guidelines for AppSource  
-->
---  

## <a name="guidelines-for-azure-marketplace"></a>Indicazioni per Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Indicazioni per la creazione di un ID Microsoft per gestire un account Marketplace  
Se più di una persona richiede l'accesso allo stesso ID Microsoft usato per creare l'account Marketplace, è consigliabile seguire queste indicazioni per la creazione di un account aziendale. 

>[!IMPORTANT]
>Per autorizzare più utenti ad accedere all'account Microsoft Developer Center (Dev Center), Microsoft consiglia di usare Azure Active Directory (Azure AD) per assegnare ruoli ai singoli utenti. Ogni utente può accedere all'account usando le credenziali di Azure AD. Creare l'ID Microsoft tramite un indirizzo di posta elettronica in un dominio registrato per l'azienda, Microsoft suggerisce di non assegnare l'indirizzo di posta elettronica a un singolo utente. Un esempio è `windowsapps@fabrikam.com`.  
>*   Per altre informazioni, vedere la sezione [Problema: ID Microsoft in un dominio federato di Azure AD](#issue-microsoft-id-in-an-azure-ad-federated-domain).  

*   Limitare l'accesso a questo ID Microsoft al minor numero possibile di sviluppatori. 
*   Impostare una lista di distribuzione (DL) di posta elettronica aziendale che include tutti gli utenti che devono accedere all'account Dev Center. Aggiungere l'indirizzo di posta elettronica della lista di distribuzione alle informazioni di protezione. La lista di distribuzione consente a tutti i dipendenti nella lista di ricevere codici di sicurezza quando richiesto e di gestire le informazioni di protezione per l'ID Microsoft. Se non è possibile impostare una lista di distribuzione, il proprietario del singolo account di posta elettronica deve accedere e condividere il codice di sicurezza quando richiesto.  
    *   Ad esempio, quando viene aggiunta una nuova informazione di protezione all'ID Microsoft o quando si esegue l'accesso all'ID Microsoft da un nuovo dispositivo.  
*   Aggiungere un numero di telefono aziendale che non richieda un'estensione e che sia accessibile ai membri principali del team.  
*   In generale, è consigliabile richiedere agli sviluppatori di usare dispositivi attendibili per accedere al proprio account Dev Center. Tutti i principali membri del team devono avere accesso a questi dispositivi affidabili. L'utilizzo di dispositivi attendibili per l'accesso riduce la necessità di inviare codici di sicurezza quando un utente accede all'account Dev Center.  
*   Se è necessario concedere l'accesso all'account Dev Center da un computer non attendibile, è consigliabile limitare l'accesso a non più di cinque sviluppatori. In teoria, gli sviluppatori devono accedere all'account da computer che condividono la stessa area geografica e lo stesso percorso di rete.  
*   Esaminare e verificare di frequente le informazioni di sicurezza.  
    *   Per visualizzare le informazioni di protezione, visitare la pagina Impostazioni di protezione disponibile all'indirizzo [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

L'accesso all'account Dev Center deve avvenire principalmente da computer attendibili. È fondamentale che l'accesso venga eseguito da computer attendibili, in quanto è previsto un limite al numero di codici generati per ogni account Dev Center a settimana. L'uso di computer attendibili consente inoltre un'esperienza di accesso più sicura e coerente. 
*   Per altre informazioni su ulteriori indicazioni relativa all'account Dev Center e alla protezione, visitare la pagina Apertura di un account per sviluppatore disponibile all'indirizzo [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---  

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problema: ID Microsoft in un dominio federato di Azure AD  
L'account aziendale può essere federato tramite Azure Active Directory (Azure AD). Se si prova a creare un ID Microsoft tramite un indirizzo di posta elettronica aziendale federato con Azure AD, viene visualizzato un errore. Se si visualizza un errore, è consigliabile rivolgersi al team IT per verificare che l'account è federato con Azure AD. L'indirizzo di posta elettronica federato con Azure AD è un problema noto e Microsoft si sta occupando di risolverlo.  
*   Per altre informazioni su Azure AD, visitare la pagina Documentazione di Azure Active Directory disponibile all'indirizzo [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft consiglia una soluzione alternativa. Attenersi alla procedura seguente per creare un nuovo indirizzo di posta elettronica nel dominio `outlook.com` e creare una regola per inoltrare le comunicazioni.  
1.  Passare alla pagina Crea account e fare clic sul collegamento Crea un nuovo indirizzo di posta elettronica. 
    *   Per iscriversi all'ID Microsoft, visitare la pagina Crea account disponibile all'indirizzo [signup.live.com/signup](https://signup.live.com/signup).  
2.  Creare il nuovo indirizzo di posta elettronica e immettere una password. Vengono creati un nuovo ID Microsoft e una cassetta postale nel dominio `outlook.com`. Continuare il processo di registrazione finché non viene creato l'account.  

    >[!IMPORTANT]
    >È necessario usare un indirizzo di posta elettronica o una lista di distribuzione registrata come ID Microsoft per registrarsi in Dev Center. Microsoft consiglia di usare una lista di distribuzione per eliminare la dipendenza da un singolo utente. Se l'indirizzo di posta elettronica o la lista di distribuzione non è registrata, registrarsi adesso.    

    >[!Important]
    >Se l'indirizzo di posta elettronica si trova nel dominio aziendale `Microsoft`, è impossibile usarlo per la registrazione in Dev Center.  

3.  Dopo aver creato l'ID Microsoft con l'indirizzo di posta elettronica Outlook, accedere alla cassetta postale di Outlook. Creare una regola per inoltrare i messaggi di posta elettronica. La regola per inoltrare i messaggi di posta elettronica consente di inoltrare tutti i messaggi ricevuti nella cassetta postale di Outlook all'indirizzo di posta elettronica creato nel dominio per gestire l'account Marketplace.  
    *   Per accedere alla cassetta postale di Outlook, visitare la pagina Outlook disponibile all'indirizzo [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Per altre informazioni sulle regole di inoltro, visitare la pagina Usare le regole in Outlook Web App per inoltrare automaticamente i messaggi a un altro account disponibile all'indirizzo [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  La regola di inoltro invia tutti i messaggi di posta elettronica e le comunicazioni ricevute nell'account di posta elettronica di Outlook all'indirizzo di posta elettronica in un dominio registrato per la propria azienda. L'indirizzo di posta elettronica `outlook.com` deve essere usato per l'autenticazione sia in Dev Center che nel portale Cloud Partner.  

## <a name="next-steps"></a>Passaggi successivi
*   Visitare la pagina [Guida alla pubblicazione per Azure Marketplace e AppSource](./marketplace-publishers-guide.md).  
 
---  
