---
title: Aggiungere un nome di dominio personalizzato ad Azure Active Directory | Documentazione Microsoft
description: Informazioni su come aggiungere i nomi di dominio dell&quot;azienda ad Azure Active Directory e come verificare il nome di dominio.
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7bd889847f07ebbadc5e427f2b1ff3f9f869e69a


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Aggiungere un nome di dominio personalizzato ad Azure Active Directory
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-domains-add-azure-portal.md)
> * [portale di Azure classico](active-directory-add-domain.md)
> 
> 

L'organizzazione usa uno o più nomi di dominio per svolgere attività commerciali e gli utenti accedono alla rete aziendale con il nome di dominio aziendale. Poiché ora si usa Azure Active Directory (Azure AD), è possibile aggiungere il nome di dominio aziendale anche ad Azure AD. Nella directory si potranno così assegnare nomi utente familiari, ad esempio ‘alice@contoso.com.’. Il processo è semplice:

1. Aggiungere il nome di dominio personalizzato alla directory
2. Aggiungere una voce DNS per il nome di dominio nel registrar
3. Verificare il nome di dominio personalizzato in Azure AD

> [!NOTE]
> Se si prevede di configurare il nome di dominio personalizzato da usare con Active Directory Federation Services (AD FS) o un servizio token di sicurezza diverso nella rete aziendale, seguire le istruzioni disponibili in [Add and configure a domain for federation with Azure Active Directory](active-directory-add-domain-federated.md)(Aggiungere e configurare un dominio per la federazione con Azure Active Directory). Ciò risulta utile se si prevede di sincronizzare gli utenti della directory aziendale con Azure AD e la [configurazione della sincronizzazione degli hash delle password](active-directory-aadconnectsync-implement-password-synchronization.md) non rispetta i requisiti.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Aggiungere un nome di dominio personalizzato alla directory
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account utente amministratore globale per la directory di Azure AD.
2. In **Active Directory** aprire la directory e selezionare la scheda **Domini**.
3. Sulla barra dei comandi selezionare **Aggiungi**. Immettere il nome del dominio personalizzato, ad esempio "contoso.com". Assicurarsi di includere l'estensione .com, .net o un'altra estensione di primo livello e lasciare deselezionata la casella di controllo relativa all'accesso Single Sign-On (federazione).
4. Selezionare **Aggiungi**.
5. Nella seconda pagina della procedura guidata Aggiungi dominio ottenere la voce DNS che Azure AD userà per verificare che l'azienda è proprietaria del nome di dominio personalizzato.

Dopo aver aggiunto il nome di dominio, Azure AD deve verificare che sia di proprietà dell'organizzazione. Per consentire ad Azure AD di eseguire questa verifica, è necessario aggiungere una voce DNS nel file di zona DNS per il nome di dominio. Questa attività viene eseguita nel sito Web del registrar per il nome di dominio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Aggiungere la voce DNS al registrar per il dominio
Il passaggio successivo per potere usare il nome di dominio personalizzato con Azure AD consiste nell'aggiornare il file di zona DNS per il dominio. Ciò consente ad Azure AD di verificare che l'organizzazione sia proprietaria del nome di dominio personalizzato.

1. Accedere al registrar per il dominio. Se le autorizzazioni di accesso non sono sufficienti per aggiornare la voce DNS, chiedere a un altro utente o team autorizzato ad accedere di completare il passaggio 2 e di segnalarne il completamento.
2. Per aggiornare il file di zona DNS per il dominio, aggiungere la voce DNS fornita da Azure AD. Questa voce DNS consente ad Azure AD di verificare la proprietà del dominio. La voce DNS non modifica alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web.

Per informazioni sull'aggiunta della voce DNS, vedere [Creare record DNS per Office 365 quando si gestiscono i record DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificare il nome di dominio con Azure AD
Dopo aver aggiunto la voce DNS, è possibile verificare il nome di dominio con Azure AD.

Se la procedura guidata **Aggiungi dominio** è ancora aperta, selezionare **Verifica** nella terza pagina. Quando si seleziona **Verifica**, Azure AD cercherà la voce DNS nel file di zona DNS per il dominio. Azure AD può verificare il nome di dominio solo dopo la propagazione dei record DNS. La propagazione richiede spesso solo qualche secondo, ma a volte può richiedere più di un'ora. Se il primo tentativo di verifica non funziona, riprovare più tardi.

Se la procedura guidata **Aggiungi dominio** è stata chiusa, è possibile verificare il dominio nel [portale di Azure classico](https://manage.windowsazure.com/):

1. Accedere con un account utente amministratore globale per la directory di Azure AD.
2. Aprire la directory e selezionare la scheda **Domini** .
3. Selezionare il nome di dominio da verificare, quindi selezionare **Verifica** sulla barra dei comandi.
4. Selezionare **Verifica** nella finestra di dialogo per completare la verifica.

A questo punto è possibile [assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se non è possibile verificare un nome di dominio personalizzato, provare a eseguire queste operazioni. Si inizierà dalla più comune fino a quella meno comune.

1. **Attendere un'ora**. I record DNS devono essere propagati prima che Azure AD possa verificare il dominio. Questa operazione potrebbe richiedere più di un'ora.
2. **Assicurarsi che il record DNS sia stato immesso e che sia corretto**. Completare questo passaggio nel sito Web del registrar per il dominio. Azure AD non può verificare il nome di dominio se la voce DNS non è presente nel file di zona DNS o se non corrisponde esattamente alla voce DNS fornita da Azure AD. Se le autorizzazioni di accesso non sono sufficienti per aggiornare i record DNS per il dominio nel registrar, condividere la voce DNS con l'utente o il team dell'organizzazione autorizzato ad accedere, in modo che possa aggiungere la voce DNS.
3. **Eliminare il nome di dominio dall'altra directory in Azure AD**. Un nome di dominio può essere verificato solo in una directory. Se un nome di dominio è già stato verificato in un'altra directory, è necessario eliminarlo prima di poterlo verificare nella nuova directory. Per informazioni sull'eliminazione dei nomi di dominio, vedere [Gestione dei nomi di dominio personalizzati in Azure Active Directory](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Aggiungere altri nomi di dominio personalizzati
Se l'organizzazione usa più nomi di dominio personalizzati, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerne fino a un massimo di 900. Usare la stessa procedura riportata in questo articolo per aggiungere ogni nome di dominio.

## <a name="next-steps"></a>Passaggi successivi
* [Assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-add-domain-add-users.md)
* [Gestire i nomi di dominio personalizzati](active-directory-add-manage-domain-names.md)
* [Informazioni sui concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md)
* [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)
* [Usare PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Dec16_HO2-->


