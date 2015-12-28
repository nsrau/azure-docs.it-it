
<properties 
    pageTitle="Come usare Azure RemoteApp con gli account utente di Office 365 | Microsoft Azure"
	description="Informazioni su come usare Azure RemoteApp con gli account utente di Office 365"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/16/2015" 
    ms.author="elizapo" />



# Come usare Azure RemoteApp con gli account utente di Office 365
Se si dispone di una sottoscrizione Office 365, è presente una directory Azure Active Directory in cui sono archiviati i nomi utente e le password utilizzati per accedere ai servizi di Office 365. Ad esempio, quando gli utenti attivano Office 365 ProPlus, eseguono l’autenticazione con Azure AD per cercare le licenze. La maggior parte dei clienti desidera utilizzare la stessa directory con Azure RemoteApp.

Se si distribuisce Azure RemoteApp, è molto probabile che venga utilizzata una sottoscrizione di Azure associata a un Azure AD diverso. Per utilizzare la directory di Office 365, è necessario spostare la sottoscrizione di Azure in tale directory.

Per informazioni su come distribuire le applicazioni client di Office 365, vedere [Come utilizzare la sottoscrizione a Office 365 con Azure RemoteApp](remoteapp-officesubscription.md).
 
## Fase 1: Registrare la sottoscrizione gratuita di Office 365 Azure Active Directory
Seguire le istruzioni in [Modificare il tenant di Azure Active Directory in Azure RemoteApp](remoteapp-changetenant.md) per ottenere l’accesso amministrativo ad Azure AD tramite il portale di gestione di Azure. Come risultato di questo processo dovrebbe essere possibile accedere al portale di Azure e visualizzare la directory lì: a questo punto non verrà visualizzato molto di più perché la sottoscrizione di Azure completa in uso con Azure RemoteApp è in una directory diversa.

Ricordare il nome e la password dell'account amministratore creati in questo passaggio, perché saranno necessari nella fase 2.

## Fase 2: Modificare l’Azure AD associato alla sottoscrizione di Azure.
La sottoscrizione di Azure verrà modificata dalla directory corrente alla directory di Office 365 utilizzata nella fase 1.

Seguire le istruzioni descritte in [Modificare il tenant di Azure Active Directory in RemoteApp di Azure](remoteapp-changetenant.md). Prestare particolare attenzione ai seguenti passaggi:

- Passaggio 1: Se è stato distribuito Azure RemoteApp (ARA) nella sottoscrizione, assicurarsi di rimuovere innanzitutto tutti gli account utente di Azure AD da qualsiasi raccolta ARA, prima di tentare qualsiasi altra operazione. In alternativa, è possibile valutare l’eliminazione delle raccolte esistenti.
- Passaggio 2: Questo è un passaggio critico. È necessario utilizzare un account Microsoft (ad esempio @outlook.com) come amministratore del servizio nella sottoscrizione; questo perché non è possibile avere account utente dall’Azure AD esistente collegati alla sottoscrizione, altrimenti non sarà possibile spostarla in un Azure AD diverso.
- Passaggio 4: Quando si aggiunge una directory esistente, il sistema richiederà di accedere con l'account amministratore per tale directory. Assicurarsi di utilizzare l'account amministratore dalla fase 1.
- Passaggio 5: Modificare la directory padre della sottoscrizione con la directory di Office 365. Il risultato finale deve essere che, in Impostazioni -> Sottoscrizioni, nella sottoscrizione è elencata la directory di Office 365. ![Modificare la directory padre della sottoscrizione](./media/remoteapp-o365user/settings.png)
 

A questo punto la sottoscrizione di Azure RemoteApp è associata a Office 365 Azure AD; è possibile utilizzare gli account utente di Office 365 esistenti con Azure RemoteApp!

<!---HONumber=AcomDC_1217_2015-->