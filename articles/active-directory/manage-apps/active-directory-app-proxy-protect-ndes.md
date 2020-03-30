---
title: Integrazione con il proxy di applicazione di Active Directory in un server rappresenta
titleSuffix: Azure Active Directory
description: Indicazioni sulla distribuzione di un proxy di applicazione di Azure Active Directory per proteggere il server rappresenta.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032257"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrazione con il proxy di applicazione di Azure AD in un server NDES (Network Device Enrollment Service)

Il proxy di applicazione di Azure Active Directory (AD) consente di pubblicare applicazioni all'interno della rete. Queste applicazioni sono quelle quali i siti di SharePoint, Microsoft Outlook Web App e altre applicazioni Web. Fornisce inoltre un accesso sicuro agli utenti esterni alla rete tramite Azure.It also provides secure access to users outside your network via Azure.

Se non si ha familiarità con il proxy di applicazione di Azure AD e si vogliono altre informazioni, vedere [Accesso remoto alle applicazioni locali tramite](application-proxy.md)il proxy di applicazione di Azure AD.

Azure AD Application Proxy is built on Azure. Offre un'enorme quantità di larghezza di banda di rete e infrastruttura server per una migliore protezione contro gli attacchi DDOS (Distributed Denial of Service) e una disponibilità eccellente. Inoltre, non è necessario aprire porte firewall esterne alla rete locale e non è necessario alcun server d'rete. Tutto il traffico ha origine in ingresso. Per un elenco completo delle porte in uscita, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)

> Azure AD Application Proxy is a feature that is available only if you are using the Premium or Basic editions of Azure Active Directory. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Se si dispone di licenze Enterprise Mobility Suite (EMS), è possibile utilizzare questa soluzione.
> Il connettore proxy di applicazione di Azure AD viene installato solo in Windows Server 2012 R2 o versione successiva. Questo è anche un requisito del server rappresenta.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Installare e registrare il connettore sul server rappresenta

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'applicazione della directory che usa il proxy di applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias amministratore di tale dominio.
1. Selezionare il nome utente nell'angolo superiore destro. Verificare di aver effettuato l'accesso a una directory che usa il proxy di applicazione. Se è necessario cambiare directory, selezionare **Cambia directory** e scegliere una directory che usa il proxy di applicazione.
1. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Proxy di applicazione**.
1. Selezionare **Scarica servizio connettore**.

    ![Scaricare il servizio connettore per visualizzare le condizioni d'uso](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Leggere le condizioni d'uso. Al termine, selezionare **Accetta le condizioni e scarica**.
1. Copiare il file di installazione del connettore proxy di applicazione di Azure AD nel server rappresenta. 
   > È possibile installare il connettore su qualsiasi server all'interno della rete aziendale con accesso a rappresenta. Non è necessario installarlo sul server rappresenta stesso.
1. Eseguire il file di installazione, ad esempio *AADApplicationProxyConnectorInstaller.exe*. Accettare le condizioni di licenza software.
1. Durante l'installazione, viene richiesto di registrare il connettore con il proxy di applicazione nella directory di Azure AD.
   * Fornire le credenziali per un amministratore globale o dell'applicazione nella directory di Azure AD. Le credenziali di amministratore globale o dell'applicazione di Azure AD possono essere diverse dalle credenziali di Azure nel portale.

        > [!NOTE]
        > L'account globale o dell'amministratore dell'applicazione utilizzato per registrare il connettore deve appartenere alla stessa directory in cui si abilita il servizio proxy di applicazione.
        >
        > Ad esempio, se il *contoso.com*dominio di Azure AD è `admin@contoso.com` contoso.com , l'amministratore globale/applicazione deve essere o un altro alias valido in tale dominio.

   * Se La Protezione avanzata di Internet Explorer è attivata per il server in cui si installa il connettore, la schermata di registrazione potrebbe essere bloccata. Per consentire l'accesso, seguire le istruzioni nel messaggio di errore o disattivare Protezione avanzata di Internet Explorer durante il processo di installazione.
   * Se la registrazione del connettore non riesce, vedere [Risoluzione dei problemi relativi al proxy di applicazione](application-proxy-troubleshoot.md).
1. Al termine dell'installazione, viene visualizzata una nota per gli ambienti con un proxy in uscita. Per configurare il connettore del proxy di applicazione di Azure `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`AD in modo che funzioni tramite il proxy in uscita, eseguire lo script fornito, ad esempio .
1. Nella pagina Proxy applicazione nel portale di Azure il nuovo connettore è elencato con lo stato *Attivo,* come illustrato nell'esempio seguente:

    ![Il nuovo connettore proxy di applicazione di Azure AD visualizzato come attivo nel portale di AzureThe new Azure AD Application Proxy connector shown as active in the Azure portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Per fornire disponibilità elevata per le applicazioni che eseguono l'autenticazione tramite il proxy di applicazione di Azure AD, è possibile installare i connettori in più macchine virtuali. Ripetere gli stessi passaggi elencati nella sezione precedente per installare il connettore in altri server aggiunti al dominio gestito di Servizi di dominio Active Directory di Azure.

1. Dopo l'installazione, tornare al portale di Azure.After successful installation, go back to the Azure portal.

1. Selezionare **Applicazioni aziendali**.

   ![assicurarsi di coinvolgere le parti interessate giuste](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Selezionare **Nuova applicazione**, quindi **Applicazione locale**. 

1. **Nell''applicazione Aggiungi**applicazione locale configurare i campi seguenti:

   * **Nome**: Immettere un nome per l'applicazione.
   * **URL interno**: Immettere l'URL interno/FQDN del server rappresenta in cui è stato installato il connettore.
   * **Preautenticazione**: selezionare **Passthrough**. Non è possibile utilizzare alcuna forma di pre-autenticazione. Il protocollo utilizzato per le richieste di certificati (SCEP) non fornisce tale opzione.
   * Copiare **l'URL esterno** fornito negli Appunti.

1. Per salvare l'applicazione, selezionare **: Aggiungi.**

1. Verificare se è possibile accedere al server rappresenta tramite il proxy di applicazione di Azure AD incollando il collegamento copiato nel passaggio 10 in un browser. Verrà visualizzata una pagina di benvenuto predefinita di IIS.

1. Come test finale, aggiungere il percorso *mscep.dll* all'URL esistente incollato nel passaggio precedente:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Verrà visualizzato un **errore HTTP 403 – Risposta non consentita.**

1. Modificare l'URL rappresenta fornito (tramite Microsoft Intune) ai dispositivi, questa modifica potrebbe essere in Microsoft Endpoint Configuration Center o Intune Cloud.

   * Per Centro configurazione, passare al punto di registrazione certificati (CRP) e modificare l'URL. Questo URL è ciò che i dispositivi chiamano e presentano la loro sfida.
   * Per Intune Cloud Only, noto anche come Intune autonomo, modificare o creare un nuovo criterio SCEP e aggiungere il nuovo URL.

## <a name="next-steps"></a>Passaggi successivi

Con il proxy di applicazione di Azure AD integrato con rappresenta, pubblicare le applicazioni a cui gli utenti possono accedere. Per altre informazioni, vedere [pubblicare applicazioni con il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)di Azure AD.
