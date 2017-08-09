---
title: Aggiungere un nome di dominio personalizzato ad Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere i nomi di dominio dell'azienda ad Azure Active Directory e come verificare il nome di dominio.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d97e57c6-578a-4929-8fb8-42e858a711c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: ad72f768add7edc1d34a85c27dc2aa1b4e4b3a50
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Aggiungere un nome di dominio personalizzato ad Azure Active Directory
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-domains-add-azure-portal.md)
> * [portale di Azure classico](active-directory-add-domain.md)
> 

Tramite Azure Active Directory (Azure AD) è possibile anche aggiungere il nome di dominio aziendale ad Azure AD. Può essere presente un nome di dominio aziendale usato dall'organizzazione per le attività aziendali e dagli utenti per l'accesso. Aggiungendo il nome di dominio ad Azure AD, nella directory si potranno assegnare nomi utente familiari, ad esempio "alice@contoso.com". Il processo è semplice:

1. Aggiungere il nome di dominio personalizzato alla directory
2. Aggiungere una voce DNS per il nome di dominio nel registrar
3. Verificare il nome di dominio personalizzato in Azure AD

## <a name="how-do-i-add-a-domain-name"></a>Come è possibile aggiungere un nome di dominio?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.
   
   ![Apertura di Gestione utenti](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Nel pannello ***nome directory*** selezionare **Nomi di dominio**.
4. Nel pannello ***Nome directory* - Nomi di dominio** selezionare il comando **Aggiungi**.
   
   ![Selezione del comando Aggiungi](./media/active-directory-domains-add-azure-portal/add-command.png)
5. Nel pannello **Nome di dominio** immettere il nome del dominio personalizzato nella casella, ad esempio 'contoso.com' e quindi selezionare **Aggiungi dominio**. Assicurarsi di includere .com, .net o altre estensioni di primo livello.
6. Nel pannello ***nomedominio***, vale a dire il pannello visualizzato con il nuovo nome di dominio nel titolo, ottenere le informazioni relative alla voce DNS che Azure AD userà per verificare che l'azienda disponga di un nome di dominio personalizzato.
   
   ![ottenere informazioni relative alla voce DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Dopo aver aggiunto il nome di dominio, Azure AD deve verificare che sia di proprietà dell'organizzazione. Per consentire ad Azure AD di eseguire questa verifica, è necessario aggiungere una voce DNS nel file di zona DNS per il nome di dominio. Questa attività viene eseguita nel sito Web del registrar per il nome di dominio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Aggiungere la voce DNS al registrar per il dominio
Il passaggio successivo per potere usare il nome di dominio personalizzato con Azure AD consiste nell'aggiornare il file di zona DNS per il dominio. Ciò consente ad Azure AD di verificare che l'organizzazione sia proprietaria del nome di dominio personalizzato.

1. Accedere al registrar per il dominio. Se le autorizzazioni di accesso non sono sufficienti per aggiornare la voce DNS, chiedere a un altro utente o team autorizzato ad accedere di completare il passaggio 2 e di segnalarne il completamento.
2. Per aggiornare il file di zona DNS per il dominio, aggiungere la voce DNS fornita da Azure AD. Questa voce DNS consente ad Azure AD di verificare la proprietà del dominio. La voce DNS non modifica alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web.

Per informazioni sull'aggiunta della voce DNS, vedere [Creare record DNS per Office 365 quando si gestiscono i record DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificare il nome di dominio con Azure AD
Dopo aver aggiunto la voce DNS, è possibile verificare il nome di dominio con Azure AD.

Un nome di dominio può essere verificato solo dopo la propagazione dei record DNS. La propagazione richiede spesso solo qualche secondo, ma a volte può richiedere più di un'ora. Se il primo tentativo di verifica non funziona, riprovare più tardi.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Sfoglia**, immettere Gestione utenti nella casella di testo e quindi selezionare **Invio**.
   
   ![Apertura di Gestione utenti](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Nel pannello **Gestione utenti - Nomi di dominio** selezionare il nome di dominio non verificato che si vuole verificare.
4. Nel pannello ***nomedominio***, vale a dire il pannello visualizzato con il nuovo nome di dominio nel titolo, selezionare **Verifica** per completare la verifica.

A questo punto è possibile [assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se non è possibile verificare un nome di dominio personalizzato, provare a eseguire queste operazioni. Si inizierà dalla più comune fino a quella meno comune.

1. **Attendere un'ora**. I record DNS devono essere propagati prima che Azure AD possa verificare il dominio. Questa operazione potrebbe richiedere più di un'ora.
2. **Assicurarsi che il record DNS sia stato immesso e che sia corretto**. Completare questo passaggio nel sito Web del registrar per il dominio. Azure AD non può verificare il nome di dominio se la voce DNS non è presente nel file di zona DNS o se non corrisponde esattamente alla voce DNS fornita da Azure AD. Se le autorizzazioni di accesso non sono sufficienti per aggiornare i record DNS per il dominio nel registrar, condividere la voce DNS con l'utente o il team dell'organizzazione autorizzato ad accedere, in modo che possa aggiungere la voce DNS.
3. **Eliminare il nome di dominio dall'altra directory in Azure AD**. Un nome di dominio può essere verificato solo in una directory. Se un nome di dominio è già stato verificato in un'altra directory, è necessario eliminarlo prima di poterlo verificare nella nuova directory. Per informazioni sull'eliminazione dei nomi di dominio, vedere [Gestione dei nomi di dominio personalizzati in Azure Active Directory](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Aggiungere altri nomi di dominio personalizzati
Se l'organizzazione usa più nomi di dominio personalizzati, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerne fino a un massimo di 900. Usare la stessa procedura riportata in questo articolo per aggiungere ogni nome di dominio.

## <a name="next-steps"></a>Passaggi successivi
[Gestire i nomi di dominio personalizzati](active-directory-domains-manage-azure-portal.md)


