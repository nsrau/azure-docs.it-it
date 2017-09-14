---
title: Aggiungere un dominio personalizzato ad Azure AD | Microsoft Docs
description: Illustra come aggiungere un dominio personalizzato in Azure Active Directory.
services: active-directory
author: curtand
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: curtand
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 07aaefc0b2f806178651c2192f6abcbae9a31f83
ms.contentlocale: it-it
ms.lasthandoff: 08/31/2017

---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Guida introduttiva: Aggiungere un nome di dominio personalizzato ad Azure Active Directory

Ogni directory di Azure AD include un nome di dominio iniziale in formato *nomedominio*.onmicrosoft.com. Il nome di dominio iniziale non può essere modificato o eliminato, ma è possibile aggiungere ad Azure AD anche il nome di dominio aziendale. Ad esempio, è probabile che l'organizzazione abbia altri nomi di dominio usati per le attività aziendali e utenti che eseguono l'accesso con il nome di dominio aziendale. L'aggiunta di nomi di dominio personalizzati ad Azure AD consente di assegnare nella directory nomi utente familiari agli utenti, ad esempio "alice@contoso.com" invece di "alice@*<domain name>*.onmicrosoft.com". Il processo è semplice:

1. Aggiungere il nome di dominio personalizzato alla directory
2. Aggiungere una voce DNS per il nome di dominio nel registrar
3. Verificare il nome di dominio personalizzato in Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Aggiungere il nome di dominio personalizzato alla directory
1. Accedere al [portale di Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account di amministratore globale per la directory.
2. A sinistra selezionare **Nomi di dominio**.
3. In ***nome-directory* - Nomi di dominio** selezionare **Aggiungi**.
   
   ![Selezione del comando Aggiungi](./media/active-directory-domains-add-azure-portal/add-command.png)
5. In **Nome di dominio** immettere il nome del dominio personalizzato nella casella, ad esempio "contoso.com", e quindi selezionare **Aggiungi dominio**. Assicurarsi di includere .com, .net o altre estensioni di primo livello.
6. In ***nomedominio***, dove il nuovo nome di dominio è il titolo, raccogliere le informazioni relative alla voce DNS da usare in seguito per verificare il nome di dominio personalizzato in Azure AD.
   
   ![ottenere informazioni relative alla voce DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Se si intende eseguire la federazione dell'istanza locale di Active Directory di Windows Server con Azure AD, è necessario selezionare la casella di controllo **Desidero configurare questo dominio per l'accesso Single Sign-On con l'istanza di Active Directory locale** quando si esegue lo strumento Azure AD Connect per la sincronizzazione delle directory. È anche necessario registrare lo stesso nome di dominio selezionato per la federazione con la directory locale nel passaggio **Dominio di Azure AD** della procedura guidata. Per verificare come si presenta il passaggio nella procedura guidata, vedere [queste istruzioni](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se non si ha lo strumento Azure AD Connect, è possibile [scaricarlo da questa pagina](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Aggiungere una voce DNS per il nome di dominio nel registrar
Il passaggio successivo per potere usare il nome di dominio personalizzato con Azure AD consiste nell'aggiornare il file di zona DNS per il dominio. Azure AD può quindi verificare che l'organizzazione sia proprietaria del nome di dominio personalizzato. È possibile usare [DNS Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) per i record DNS di Azure/Office 365/esterni in Azure oppure aggiungere la voce DNS in un [registrar DNS diverso](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Accedere al registrar per il dominio. Se le autorizzazioni di accesso non sono sufficienti per aggiornare la voce DNS, chiedere a un altro utente o team autorizzato ad accedere di completare il passaggio 2 e di segnalarne il completamento.
2. Per aggiornare il file di zona DNS per il dominio, aggiungere la voce DNS fornita da Azure AD. La voce DNS non modifica alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Verificare il nome di dominio personalizzato in Azure AD
Dopo aver aggiunto la voce DNS, è possibile verificare il nome di dominio con Azure AD. Un nome di dominio può essere verificato solo dopo la propagazione dei record DNS. La propagazione richiede spesso solo qualche secondo, ma a volte può richiedere più di un'ora. Se il primo tentativo di verifica non funziona, riprovare più tardi.

1. Accedere ad [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account di amministratore globale per la directory.
2. A sinistra selezionare **Nomi di dominio**.
3. In ***Nome directory* - Nomi di dominio** selezionare il comando **Aggiungi nome di dominio**. 
  ![Selezione del comando Aggiungi](./media/active-directory-domains-add-azure-portal/add-command.png)
3. In ***Nome directory* - Nomi di dominio** selezionare il nome di dominio non verificato che si vuole verificare.
4. In ***nomedominio***, dove il nuovo nome di dominio è il titolo, selezionare **Verifica** per completare la verifica.

A questo punto è possibile [assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-users-create-azure-portal.md). Usando il nome di dominio personalizzato, è possibile creare account utente basati sul cloud oppure aggiornare le informazioni sugli account utente locali sincronizzate in precedenza. È anche possibile modificare le informazioni relative al suffisso di dominio degli account utente sincronizzate in precedenza usando [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o l'[API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> È possibile aggiungere fino a 900 nomi di dominio personalizzati, ma solo uno può essere [impostato come nome di dominio primario per la directory di Azure AD](active-directory-domains-manage-azure-portal.md#set-the-primary-domain-name-for-your-azure-ad-directory), che verrà usato per impostazione predefinita quando si creano nuovi account.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se non è possibile verificare un nome di dominio personalizzato, provare a eseguire questi passaggi per la risoluzione dei problemi:

1. **Attendere un'ora**. I record DNS devono essere propagati prima che Azure AD possa verificare il dominio. Questo processo può richiedere più di un'ora.
2. **Assicurarsi che il record DNS sia stato immesso e che sia corretto**. Completare questo passaggio nel sito Web del registrar per il dominio. Azure AD non può verificare il nome di dominio se: 
  * La voce DNS non è presente nel file di zona DNS
  * Non è una corrispondenza esatta con la voce DNS fornita da Azure AD. 
  
  Se le autorizzazioni di accesso non sono sufficienti per aggiornare i record DNS per il dominio nel registrar, condividere la voce DNS con l'utente o il team dell'organizzazione autorizzato ad accedere, in modo che possa aggiungere la voce DNS.
3. **Eliminare il nome di dominio dall'altra directory in Azure AD**. Un nome di dominio può essere verificato solo in una directory. Se un nome di dominio è attualmente in fase di verifica in un'altra directory, è necessario eliminarlo prima di poterlo verificare nella nuova directory. Per informazioni sull'eliminazione dei nomi di dominio, vedere [Gestione dei nomi di dominio personalizzati in Azure Active Directory](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Aggiungere altri nomi di dominio personalizzati
Se l'organizzazione usa più nomi di dominio personalizzati, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerne fino a un massimo di 900. I passaggi illustrati in questo articolo possono aiutare ad aggiungere ogni nome di dominio.

### <a name="learn-more"></a>Altre informazioni
[Panoramica concettuale dei nomi di dominio personalizzati in Azure AD](active-directory-add-domain-concepts.md)

[Gestire i nomi di dominio personalizzati](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come aggiungere un dominio personalizzato ad Azure AD. 

È possibile usare il collegamento seguente per aggiungere un nuovo dominio personalizzato in Azure AD dal portale di Azure.

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
