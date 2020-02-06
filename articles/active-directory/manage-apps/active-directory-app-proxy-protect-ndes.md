---
title: Integrazione con AD Application Proxy in un server registrazione dispositivi
titleSuffix: Azure Active Directory
description: Linee guida per la distribuzione di un Azure Active Directory Application Proxy per proteggere il server registrazione dispositivi.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032257"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Eseguire l'integrazione con Azure AD proxy di applicazione in un server del servizio Registrazione dispositivi di rete (registrazione dispositivi)

Il proxy dell'applicazione Azure Active Directory (AD) consente di pubblicare applicazioni all'interno della rete. Queste applicazioni sono quelle come siti SharePoint, Microsoft Outlook Web App e altre applicazioni Web. Fornisce anche l'accesso sicuro agli utenti esterni alla rete tramite Azure.

Se non si ha familiarità con Azure AD proxy di applicazione e si desidera ottenere altre informazioni, vedere [accesso remoto alle applicazioni locali tramite Azure ad proxy di applicazione](application-proxy.md).

Azure AD proxy di applicazione si basa su Azure. Offre una notevole quantità di larghezza di banda di rete e di infrastruttura server per una migliore protezione contro gli attacchi di tipo Denial of Service (DDOS) distribuiti e una disponibilità eccezionale. Non è inoltre necessario aprire porte del firewall esterne alla rete locale e non è necessario alcun server DMZ. Tutto il traffico è stato originato in ingresso. Per un elenco completo delle porte in uscita, vedere [esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

> Azure AD proxy di applicazione è una funzionalità disponibile solo se si usano le edizioni Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Se si dispone di licenze Enterprise Mobility Suite (EMS), è possibile usare questa soluzione.
> Il connettore del proxy di applicazione Azure AD viene installato solo in Windows Server 2012 R2 o versione successiva. Questo è anche un requisito del server registrazione dispositivi.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Installare e registrare il connettore nel server registrazione dispositivi

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'applicazione della directory che usa il proxy di applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias amministratore di tale dominio.
1. Selezionare il nome utente nell'angolo superiore destro. Verificare di aver effettuato l'accesso a una directory che usa il proxy di applicazione. Se è necessario cambiare directory, selezionare **Cambia directory** e scegliere una directory che usa il proxy di applicazione.
1. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Proxy di applicazione**.
1. Selezionare **Scarica servizio connettore**.

    ![Scaricare il servizio connettore per visualizzare le condizioni d'uso](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Leggere le condizioni d'uso. Al termine, selezionare **Accetta le condizioni e scarica**.
1. Copiare il file di installazione del connettore del proxy di applicazione Azure AD nel server registrazione dispositivi. 
   > È possibile installare il connettore in qualsiasi server all'interno della rete aziendale con accesso a registrazione dispositivi. Non è necessario installarlo nel server registrazione dispositivi.
1. Eseguire il file di installazione, ad esempio *AADApplicationProxyConnectorInstaller. exe*. Accettare le condizioni di licenza software.
1. Durante l'installazione, viene richiesto di registrare il connettore con il proxy dell'applicazione nella directory Azure AD.
   * Fornire le credenziali per un amministratore globale o dell'applicazione nella directory Azure AD. Le credenziali di amministratore globale o dell'applicazione Azure AD possono essere diverse dalle credenziali di Azure nel portale.

        > [!NOTE]
        > L'account amministratore globale o dell'applicazione usato per registrare il connettore deve appartenere alla stessa directory in cui si Abilita il servizio proxy di applicazione.
        >
        > Se, ad esempio, il dominio Azure AD è *contoso.com*, l'amministratore globale/dell'applicazione deve essere `admin@contoso.com` o un altro alias valido in tale dominio.

   * Se è attivata la configurazione sicurezza avanzata di Internet Explorer per il server in cui si installa il connettore, la schermata di registrazione potrebbe essere bloccata. Per consentire l'accesso, seguire le istruzioni nel messaggio di errore o disattivare sicurezza avanzata di Internet Explorer durante il processo di installazione.
   * Se la registrazione del connettore non riesce, vedere [risolvere i problemi del proxy dell'applicazione](application-proxy-troubleshoot.md).
1. Al termine dell'installazione, viene visualizzata una nota per gli ambienti con un proxy in uscita. Per configurare il connettore del proxy di applicazione Azure AD per il funzionamento tramite il proxy in uscita, eseguire lo script specificato, ad esempio `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`.
1. Nella pagina proxy di applicazione del portale di Azure il nuovo connettore è elencato con lo stato *attivo*, come illustrato nell'esempio seguente:

    ![Il nuovo connettore del proxy di applicazione Azure AD visualizzato come attivo nella portale di Azure](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Per garantire un'elevata disponibilità per le applicazioni che eseguono l'autenticazione tramite il proxy di applicazione Azure AD, è possibile installare i connettori in più macchine virtuali. Ripetere gli stessi passaggi elencati nella sezione precedente per installare il connettore in altri server aggiunti al dominio gestito di Azure AD DS.

1. Al termine dell'installazione, tornare alla portale di Azure.

1. Selezionare **Applicazioni aziendali**.

   ![Assicurarsi di coinvolgere gli stakeholder più appropriati](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Selezionare **+ nuova applicazione**, quindi selezionare **applicazione locale**. 

1. In **aggiungere un'applicazione locale**, configurare i campi seguenti:

   * **Nome**: immettere un nome per l'applicazione.
   * **URL interno**: immettere l'URL interno/FQDN del server registrazione dispositivi in cui è installato il connettore.
   * **Pre-autenticazione**: selezionare **PassThrough**. Non è possibile usare qualsiasi tipo di pre-autenticazione. Il protocollo utilizzato per le richieste di certificato (SCEP) non fornisce tale opzione.
   * Copiare l' **URL esterno** fornito negli Appunti.

1. Selezionare **+ Aggiungi** per salvare l'applicazione.

1. Verificare se è possibile accedere al server registrazione dispositivi tramite il proxy di applicazione Azure AD incollando il collegamento copiato al passaggio 10 in un browser. Verrà visualizzata una pagina iniziale predefinita di IIS.

1. Come test finale, aggiungere il percorso *MSCEP. dll* all'URL esistente incollato nel passaggio precedente:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Dovrebbe essere visualizzato un **errore HTTP 403 – accesso negato** .

1. Modificare l'URL di registrazione dispositivi fornito (tramite Microsoft Intune) ai dispositivi. questa modifica può essere in Microsoft endpoint Configuration Center o nel cloud di Intune.

   * Per Centro configurazione, passare al punto di registrazione certificati (CRP) e modificare l'URL. Questo URL è quello che i dispositivi chiamano e presentano la loro richiesta.
   * Solo per il cloud Intune, noto anche come Intune autonomo, è possibile modificare o creare un nuovo criterio SCEP e aggiungere il nuovo URL.

## <a name="next-steps"></a>Passaggi successivi

Con il Azure AD proxy di applicazione integrato con registrazione dispositivi, pubblicare le applicazioni per consentire agli utenti di accedere a. Per altre informazioni, vedere [pubblicare applicazioni con Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).
