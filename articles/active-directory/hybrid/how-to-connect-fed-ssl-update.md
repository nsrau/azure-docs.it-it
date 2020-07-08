---
title: Azure AD Connect aggiornare il certificato TLS/SSL per una farm di AD FS | Microsoft Docs
description: Questo documento illustra i passaggi per aggiornare il certificato TLS/SSL di una farm di AD FS usando Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1983b5090604516265ea8e041ac68200ca2dc7b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359586"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aggiornare il certificato TLS/SSL per una farm di Active Directory Federation Services (AD FS)

## <a name="overview"></a>Panoramica
Questo articolo descrive come è possibile usare Azure AD Connect per aggiornare il certificato TLS/SSL per una farm di Active Directory Federation Services (AD FS). È possibile utilizzare lo strumento Azure AD Connect per aggiornare facilmente il certificato TLS/SSL per la farm di AD FS anche se il metodo di accesso utente selezionato non è AD FS.

È possibile eseguire l'intera operazione di aggiornamento del certificato TLS/SSL per la farm AD FS in tutti i server federativi e proxy applicazione Web (WAP) in tre semplici passaggi:

![Tre passaggi](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Per altre informazioni sui certificati usati da AD FS, vedere [Informazioni sui certificati utilizzati da AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Prerequisiti

* **Farm AD FS**: assicurarsi che la farm AD FS sia basata su Windows Server 2012 R2 o versioni successive.
* **Azure AD Connect**: assicurarsi che la versione di Azure AD Connect sia 1.1.553.0 o successiva. Si userà l'attività **Aggiorna il certificato SSL di AD FS**.

![Aggiorna attività TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Passaggio 1: Specificare informazioni sulla farm AD FS

Azure AD Connect tenta di ottenere automaticamente informazioni sulla farm AD FS nei modi seguenti:
1. Chiedendo informazioni sulla farm ad AD FS (Windows Server 2016 o versione successiva).
2. Facendo riferimento alle informazioni ottenute da esecuzioni precedenti, archiviate localmente con Azure AD Connect.

L'elenco dei server visualizzati può essere modificato aggiungendo o rimuovendo i server in modo da riflettere la configurazione corrente della farm AD FS. Non appena vengono fornite le informazioni sul server, Azure AD Connect Visualizza la connettività e lo stato corrente del certificato TLS/SSL.

![Informazioni sui server AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Se l'elenco contiene un server che non fa più parte della farm AD FS, fare clic su **Rimuovi** per eliminare il server dall'elenco dei server della farm AD FS.

![Server offline nell'elenco](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> La rimozione di un server dall'elenco dei server della farm AD FS in Azure AD Connect è un'operazione locale e aggiorna le informazioni sulla farm AD FS mantenute localmente da Azure AD Connect. A seguito di questa operazione Azure AD Connect non modifica la configurazione in AD FS.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Passaggio 2: specificare un nuovo certificato TLS/SSL

Dopo aver confermato le informazioni sui server della farm AD FS, Azure AD Connect chiede il nuovo certificato TLS/SSL. Fornire un certificato PFX protetto da password per continuare l'installazione.

![Certificato TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Dopo aver specificato il certificato, Azure AD Connect esegue una serie di verifiche dei prerequisiti per assicurarsi che il certificato sia corretto per la farm AD FS:

-   Il nome soggetto o il nome soggetto alternativo del certificato deve corrispondere al nome del servizio federativo oppure il certificato deve contenere caratteri jolly.
-   Il certificato deve essere valido per più di 30 giorni.
-   La catena di certificati deve essere valida.
-   Il certificato deve essere protetto da password.

## <a name="step-3-select-servers-for-the-update"></a>Passaggio 3: Selezionare i server per l'aggiornamento

Nel passaggio successivo selezionare i server per i quali è necessario aggiornare il certificato TLS/SSL. I server offline non possono essere selezionati per l'aggiornamento.

![Selezionare i server da aggiornare](./media/how-to-connect-fed-ssl-update/selectservers.png)

Dopo aver completato la configurazione, Azure AD Connect visualizza un messaggio che indica lo stato dell'aggiornamento e rende disponibile un'opzione per verificare l'accesso di AD FS.

![Configurazione completata](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Domande frequenti

* **Specificare il nome del soggetto del certificato per la nuova AD FS certificato TLS/SSL.**

    Azure AD Connect verifica che il nome soggetto o il nome soggetto alternativo del certificato includa il nome del servizio federativo. Ad esempio, se il nome del servizio federativo è fs.contoso.com, il nome soggetto o il nome soggetto alternativo deve essere fs.contoso.com.  Sono accettati anche certificati con caratteri jolly.

* **Perché vengono nuovamente richieste le credenziali nella pagina del server WAP?**

    Se alle credenziali specificate per la connessione ai server AD FS non sono associati anche i privilegi necessari per gestire i server WAP, Azure AD Connect richiede credenziali con privilegi amministrativi per i server WAP.

* **Il server viene visualizzato come offline. Cosa dovrei fare?**

    Se il server è offline, Azure AD Connect non può eseguire alcuna operazione. Se il server fa parte della farm AD FS, controllare la connettività al server. Dopo avere risolto il problema, premere l'icona di aggiornamento per aggiornare lo stato nella procedura guidata. Se il server faceva parte della farm in precedenza, ma ora non esiste più, fare clic su **Rimuovi** per eliminarlo dall'elenco dei server gestiti da Azure AD Connect. La rimozione di un server dall'elenco in Azure AD Connect non modifica la configurazione di AD FS. Se si usa AD FS in Windows Server 2016 o versioni successive, il server rimane nelle impostazioni di configurazione e verrà visualizzato nuovamente in occasione dell'esecuzione successiva dell'attività.

* **È possibile aggiornare un subset di server della farm con il nuovo certificato TLS/SSL?**

    Sì. In qualsiasi momento, è possibile eseguire nuovamente l'attività **Aggiorna certificato SSL** per aggiornare i server rimanenti. Nella pagina **Selezionare i server per l'aggiornamento del certificato SSL** è possibile ordinare l'elenco dei server in base a **Data di scadenza SSL** per accedere con facilità ai server non ancora aggiornati.

* **Il server è stato rimosso nell'esecuzione precedente, ma è ancora visualizzato come offline ed elencato nella pagina dei server AD FS. Perché il server offline è ancora presente anche dopo la rimozione?**

    La rimozione di un server dall'elenco di Azure AD Connect non lo rimuove dalla configurazione di AD FS. Azure AD Connect fa riferimento ad AD FS (Windows Server 2016 o versioni successive) per qualsiasi informazione sulla farm. Se il server è ancora presente nella configurazione di AD FS, verrà nuovamente incluso nell'elenco.  

## <a name="next-steps"></a>Passaggi successivi

- [Azure AD Connect e federazione](how-to-connect-fed-whatis.md)
- [Gestione e personalizzazione di Active Directory Federation Services con Azure AD Connect](how-to-connect-fed-management.md)

