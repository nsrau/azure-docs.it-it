---
title: Aggiungere un nome di dominio personalizzato e configurare l'accesso Single Sign-On federato ad Azure Active Directory | Microsoft Docs
description: Come aggiungere i nomi di dominio dell'azienda ad Azure Active Directory e come configurare l'accesso Single Sign-On federato tra Azure Active Directory e la soluzione federativa locale.
services: active-directory
documentationcenter: ''
author: jeffsta
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2016
ms.author: curtand;jeffsta

---
# Aggiungere un nome di dominio personalizzato ad Azure Active Directory
È possibile configurare un nome di dominio personalizzato, ad esempio 'contoso.com', in modo che gli utenti in contoso.com possano avere un'esperienza di accesso Single Sign-On federato dalla rete aziendale. Se è già disponibile Active Directory Federation Services (AD FS) o un server federativo diverso in esecuzione nella rete aziendale, è possibile configurare Azure AD per l'uso del nome di dominio personalizzato tramite lo strumento Azure AD Connect. È anche possibile usare Azure AD Connect per distribuire un nuovo ambiente AD FS e per configurarlo per l'accesso Single Sign-On federato ad Azure AD.

Se AD FS o un altro server federativo non è disponibile e non se ne prevede la distribuzione, seguire le istruzioni in [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](active-directory-add-domain.md).

## Aggiungere un nome di dominio personalizzato alla directory
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account utente amministratore globale per la directory di Azure AD.
2. In **Active Directory** aprire la directory e selezionare la scheda **Domini**.
3. Sulla barra dei comandi selezionare **Aggiungi** e quindi immettere il nome del dominio personalizzato, ad esempio "contoso.com". Assicurarsi di includere .com, .net o altre estensioni di primo livello.
4. Selezionare la casella di controllo **Desidero configurare questo dominio per l'accesso Single Sign-On con il mio servizio Active Directory locale**.
5. Selezionare **Aggiungi**.

Eseguire lo strumento Azure AD Connect per ottenere la voce DNS che verrà usata da Azure AD per verificare il dominio. La voce DNS verrà visualizzata nel passaggio **Dominio di Azure AD** della procedura guidata. Per verificare l'aspetto del passaggio nella procedura guidata, vedere [queste istruzioni](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se non si ha lo strumento Azure AD Connect, è possibile [scaricarlo da questa pagina](http://go.microsoft.com/fwlink/?LinkId=615771).

## Aggiungere la voce DNS al registrar per il dominio
Il passaggio successivo per potere usare il nome di dominio personalizzato con Azure AD consiste nell'aggiornare il file di zona DNS per il dominio. Ciò consente ad Azure AD di verificare che l'organizzazione sia proprietaria del nome di dominio personalizzato.

1. Accedere al sito Web del registrar per il nome di dominio. Se le autorizzazioni di accesso non sono sufficienti per eseguire questa operazione, chiedere a un altro utente o team dell'organizzazione autorizzato ad accedere di completare il passaggio 2 e di segnalarne il completamento.
2. Per aggiornare il file di zona DNS per il dominio, aggiungere la voce DNS fornita da Azure AD. Questa voce DNS consente ad Azure AD di verificare la proprietà del dominio. La voce DNS non modifica alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web.

Per informazioni su questo passaggio, vedere [Creare record DNS per Office 365 quando si gestiscono i record DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificare il nome di dominio con Azure AD
Dopo aver aggiunto la voce DNS, è possibile verificare il nome di dominio con Azure AD.

Per verificare il dominio, selezionare **Avanti** nel passaggio **Dominio di Azure AD** della procedura guidata di Azure AD Connect. Azure AD cercherà la voce DNS nel file di zona DNS per il dominio. Azure AD verifica il nome di dominio solo dopo la propagazione dei record DNS. La propagazione richiede spesso solo qualche secondo, ma a volte può richiedere più di un'ora. Se il primo tentativo di verifica non funziona, riprovare più tardi.

Continuare quindi a eseguire i passaggi rimanenti della procedura guidata di Azure AD Connect. Gli utenti verranno sincronizzati da Windows Server AD ad Azure AD. Gli utenti sincronizzati nel dominio configurato per la federazione potranno ottenere un'esperienza di accesso Single Sign-On federato dalla rete aziendale ad Azure AD.

## Risoluzione dei problemi
Se non è possibile verificare un nome di dominio personalizzato, provare a eseguire queste operazioni. Si inizierà dalla più comune fino a quella meno comune.

1. **Attendere un'ora**. I record DNS devono essere propagati prima che Azure AD possa verificare il dominio. Questa operazione potrebbe richiedere più di un'ora.
2. **Assicurarsi che il record DNS sia stato immesso e che sia corretto**. Completare questo passaggio nel sito Web del registrar per il dominio. Azure AD non può verificare il nome di dominio se la voce DNS non è presente nel file di zona DNS o se non corrisponde esattamente alla voce DNS fornita da Azure AD. Se le autorizzazioni di accesso non sono sufficienti per aggiornare i record DNS per il dominio nel registrar, condividere la voce DNS con l'utente o il team dell'organizzazione autorizzato ad accedere, in modo che possa aggiungere la voce DNS.
3. **Eliminare il nome di dominio dall'altra directory in Azure AD**. Un nome di dominio può essere verificato solo in una directory. Se un nome di dominio è già stato verificato in un'altra directory, è necessario eliminarlo prima di poterlo verificare nella nuova directory. Per informazioni sull'eliminazione dei nomi di dominio, vedere [Gestione dei nomi di dominio personalizzati in Azure Active Directory](active-directory-add-manage-domain-names.md).

## Aggiungere altri nomi di dominio personalizzati
Se l'organizzazione usa più nomi di dominio personalizzati, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerne fino a un massimo di 900. Usare la stessa procedura riportata in questo articolo per aggiungere ogni nome di dominio.

## Passaggi successivi
* [Gestire i nomi di dominio personalizzati](active-directory-add-manage-domain-names.md)
* [Informazioni sui concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md)
* [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)
* [Usare PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_1005_2016-->