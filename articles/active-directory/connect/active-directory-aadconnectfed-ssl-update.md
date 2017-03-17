---
title: 'Azure AD Connect: Aggiornare il certificato SSL per una farm Active Directory Federation Services (AD FS) | Microsoft Docs'
description: Questo documento illustra la procedura necessaria per aggiornare il certificato SSL di una farm AD FS con Azure AD Connect.
services: active-directory
keywords: azure ad connect, aggiornamento ssl ad fs, aggiornamento certificato ad fs, modifica certificato ad fs, nuovo certificato ad fs, certificato ad fs, aggiornare certificato ssl ad fs, aggiornare ad fs certificato ssl, configurare certificato ssl ad fs, ad fs, ssl, certificato, certificato comunicazioni di servizio ad fs, aggiornare federazione, configurare federazione, aad connect
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aggiornare il certificato SSL per una farm Active Directory Federation Services (AD FS)

## <a name="overview"></a>Panoramica
Questo articolo descrive come usare Azure AD Connect per aggiornare il certificato SSL relativo a una farm Active Directory Federation Services (AD FS).  Se come metodo di accesso in Azure AD Connect è impostato AD FS, lo strumento Azure AD Connect consente di aggiornare facilmente il certificato SSL per la farm AD FS, nell'intera federazione e nei server WAP, in tre semplici passaggi:

![Tre passaggi](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Per altre informazioni sui certificati usati da AD FS, vedere l'articolo [Informazioni sui certificati utilizzati da AD FS](https://technet.microsoft.com/library/cc730660.aspx).

##<a name="pre-requisites"></a>Prerequisiti

* **Farm AD FS**: la versione della farm AD FS deve essere 2012R2 or successiva
* **Azure AD Connect**: assicurarsi che la versione di Azure AD Connect sia 1.1.443.0 o successiva. Si troverà l'attività "Aggiorna il certificato SSL di AD FS".

![Attività Aggiorna SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>Passaggio 1: Specificare informazioni sulla farm AD FS

AAD Connect proverà a ottenere automaticamente informazioni sulla farm AD FS nei modi seguenti:
1. Chiedendo informazioni sulla farm da AD FS (2016 o versione successiva)
2. Facendo riferimento alle informazioni ottenute da esecuzioni precedenti (archiviate localmente con Azure AD Connect). 

L'elenco dei server visualizzati può essere modificato aggiungendo o rimuovendo i server in modo da riflettere la configurazione corrente della farm AD FS. Non appena riesce a ottenere informazioni sui server, Azure AD Connect visualizza la connettività e lo stato corrente del certificato SSL.

![Informazioni sui server AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Se l'elenco contiene un server che non fa più parte della farm AD FS, fare clic sull'icona di rimozione per eliminare il server dall'elenco dei server della farm AD FS. 

![Server offline nell'elenco](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> L'eliminazione dall'elenco dei server della farm AD FS in Azure AD Connect è un'operazione locale e aggiorna le informazioni sulla farm AD FS mantenute localmente da Azure AD Connect. A seguito di questa operazione Azure AD Connect non apporta alcuna modifica di configurazione in AD FS.    

##<a name="step-2-provide-new-ssl-certificate"></a>Passaggio 2: Specificare il nuovo certificato SSL

Una volta confermate le informazioni sui server della farm AD FS, Azure AD Connect chiede il nuovo certificato SSL. Fornire un certificato PFX protetto da password per continuare l'installazione. 

![Certificato SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
Dopo questa operazione, Azure AD Connect verifica la presenza di alcuni prerequisiti sul certificato per assicurarsi che sia corretto per la farm AD FS:
1.    Il nome soggetto (SN) o il nome soggetto alternativo (SAN) del certificato deve corrispondere al nome del servizio federativo oppure il certificato deve contenere caratteri jolly.
2.    Il certificato deve essere valido per più di 30 giorni
3.    La catena di certificati deve essere valida 
4.    Il certificato deve essere protetto da password

##<a name="step-3-select-servers-for-update"></a>Passaggio 3: Selezionare i server per l'aggiornamento

Il passaggio successivo consiste nel selezionare i server in cui deve essere aggiornato il certificato SSL. I server offline non possono essere selezionati per l'aggiornamento. 

![Selezionare i server da aggiornare](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Al termine della configurazione, in Azure AD Connect verrà visualizzato un messaggio in cui è riportato lo stato dell'aggiornamento e sarà resa disponibile un'opzione per verificare l'accesso di AD FS.

![Configurazione completata](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>Domande frequenti

* **Quale deve essere il nome soggetto del certificato per il nuovo certificato SSL di AD FS?**

    Azure AD Connect verificherà che il nome soggetto del certificato o il nome soggetto alternativo includa il nome del servizio federativo. Se, ad esempio, il nome del servizio federativo è fs.contoso.com, il nome soggetto o il nome soggetto alternativo deve essere fs.contoso.com.  Sono accettati anche certificati con caratteri jolly. 

* **Perché vengono nuovamente richieste le credenziali nella pagina del server WAP?**

    Se alle credenziali specificate per la connessione ai server AD FS non sono associati i privilegi necessari per gestire anche i server WAP, Azure AD Connect chiederà credenziali con privilegi amministrativi per i server WAP.

* **Se il server appare offline, cosa è necessario fare?**

    Se il server è offline, Azure AD Connect non può eseguire alcuna operazione. Se il server fa parte della farm AD FS, verificare la connettività al server e, dopo aver risolto il problema, fare clic sull'icona di aggiornamento per aggiornare lo stato nella procedura guidata. Se il server faceva parte della farm in precedenza, ma ora non esiste più, fare clic sull'icona di rimozione per eliminarlo dall'elenco dei server gestiti da Azure AD Connect.  L'eliminazione del server dall'elenco di Azure AD Connect non modifica in alcun modo la configurazione di AD FS e, se si usa AD FS 2016 o versione successiva, il server rimarrà nelle impostazioni di configurazione e verrà nuovamente visualizzato alla successiva esecuzione dell'attività.

* **È possibile aggiornare un subset di server della farm con il nuovo certificato SSL?**

    Sì. In qualsiasi momento, è possibile eseguire nuovamente l'attività "Aggiorna certificato SSL" per aggiornare i server rimanenti. Nella pagina "Selezionare i server per l'aggiornamento del certificato SSL" è possibile ordinare l'elenco dei server in base a "Data di scadenza SSL" per accedere con facilità ai server non ancora aggiornati. 

* **Un server che era stato rimosso durante l'esecuzione precedente è ancora visualizzato come offline ed elencato nella pagina dei server AD FS. Perché il server offline è ancora presente dopo essere stato eliminato?**

    L'eliminazione di un server dall'elenco di Azure AD Connect non lo rimuove dalla configurazione di AD FS. Azure AD Connect, inoltre, fa riferimento ad AD FS (2016 o versione successiva) per qualsiasi informazioni sulla farm e, se il server è ancora presente nella configurazione di AD FS, verrà nuovamente incluso nell'elenco.  

## <a name="next-steps"></a>Passaggi successivi

[Azure AD Connect e federazione](active-directory-aadconnectfed-whatis.md)
[Gestione e personalizzazione di Active Directory Federation Services con Azure AD Connect](active-directory-aadconnect-federation-management.md)

