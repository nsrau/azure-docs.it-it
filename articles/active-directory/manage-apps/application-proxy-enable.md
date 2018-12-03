---
title: Proxy app di Azure AD - Attività iniziali e installazione del connettore | Microsoft Docs
description: Attivare il proxy di applicazione nel portale di Azure e installare i connettori per il proxy inverso.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 683b5b24fe8e7da086e000ff38411d3eb1c2f781
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495737"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Attività iniziali del proxy di applicazione e installazione del connettore
Questo articolo illustra la procedura per abilitare il proxy di applicazione nella directory di Azure Active (Azure AD).

Se non si conoscono ancora i vantaggi in termini di produttività e sicurezza offerti all'organizzazione dal proxy di applicazione, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](application-proxy.md).

## <a name="prerequisites"></a>Prerequisiti
Per abilitare il proxy di applicazione è necessario:

* Una [sottoscrizione di Microsoft Azure AD Basic o Premium](https://azure.microsoft.com/pricing/details/active-directory). 
* Un account amministratore dell'applicazione.

### <a name="windows-server"></a>Windows Server
È necessario un server che esegue Windows Server 2012 R2 o versione successiva in cui poter installare il connettore del proxy applicazione. Questo server deve connettersi ai servizi proxy di applicazione in Azure e alle applicazioni locali che vengono pubblicate.

È necessario abilitare TLS 1.2 per Windows Server prima di installare il connettore del proxy di applicazione. I connettori esistenti con versioni precedenti a 1.5.612.0 continueranno a funzionare nelle versioni precedenti di TLS fino a ulteriore comunicazione. Per abilitare TLS 1.2:

1. Impostare le chiavi del Registro di sistema seguenti:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Riavviare il server

Per l'accesso single sign-on alle applicazioni che usano Kerberos Contrained Delegation (KCD), il server di Windows e le applicazioni che vengono pubblicate devono trovarsi nello stesso dominio Active Directory. Per altre informazioni, vedere [KCD for single sign-on with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) (KDC per l'Accesso Single Sign-On con il proxy di applicazione).
  
### <a name="proxy-servers"></a>Server proxy

Se l'organizzazione usa server proxy per connettersi a internet, è necessario configurarli per il proxy di applicazione.  Per altre informazioni, vedere [Uso di server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md). 



## <a name="open-your-ports"></a>Aprire le porte

Per preparare l'ambiente per il proxy di applicazione di Azure AD, è necessario abilitare prima la comunicazione ai data center di Azure. Se nel percorso è presente un firewall, verificare che sia aperto in modo che il connettore possa inviare richieste HTTPS (TCP) al proxy di applicazione.

1. Aprire le porte seguenti al traffico **in uscita**:

   | Numero della porta | Uso |
   | --- | --- |
   | 80 | Download degli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
   | 443 | Tutte le comunicazioni in uscita con il servizio proxy di applicazione |

   Se il firewall regola il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete.

   > [!IMPORTANT]
   > La tabella contiene i requisiti relativi alle porte per le versioni del connettore 1.5.132.0 e successive. Se la versione del connettore è precedente, è anche necessario abilitare le porte seguenti oltre alla 80 e alla 443: 5671, 8080, 9090-9091, 9350, 9352, 10100-10120.
   >
   >Per informazioni sull'aggiornamento dei connettori alla versione più recente, vedere [Comprendere i connettori del proxy di applicazione di Azure AD](application-proxy-connectors.md#automatic-updates).

2. Se il firewall o il proxy consente di inserire DNS nell'elenco elementi consentiti, è possibile aggiungere connessioni a msappproxy.net e servicebus.windows.net. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) che vengono aggiornati ogni settimana.

3. Microsoft usa quattro indirizzi per verificare i certificati. Se non è già stato fatto per altri prodotti, consentire l'accesso agli URL seguenti:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Il connettore deve poter accedere a login.windows.net e login.microsoftonline.com per il processo di registrazione.


## <a name="install-and-register-a-connector"></a>Installare e registrare un connettore
1. Accedere come amministratore al [portale di Azure](https://portal.azure.com/).
2. La directory corrente viene visualizzata sotto il nome utente nell'angolo superiore destro. Se è necessario cambiare directory, selezionare questa icona.
3. Passare a **Azure Active Directory** > **Proxy dell'applicazione**.

   ![Accedere a Proxy di applicazione](./media/application-proxy-enable/app_proxy_navigate.png)

4. Selezionare **Scarica connettore**.

   ![Scaricare il connettore](./media/application-proxy-enable/download_connector.png)

5. Eseguire **AADApplicationProxyConnectorInstaller.exe** nel server preparato in base ai prerequisiti.
6. Seguire le istruzioni della procedura guidata da installare. Durante l'installazione viene richiesto di registrare il connettore con il proxy di applicazione del tenant di Azure AD.

   * Fornire le credenziali di amministratore globale di Azure AD. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
   * Verificare che l'amministratore che registra il connettore si trovi nella stessa directory in cui è stato abilitato il servizio proxy dell'applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias di tale dominio.
   * Se l'opzione **Configurazione sicurezza avanzata IE** è **attivata** nel server in cui si sta installando il connettore, potrebbe non venire visualizzata la schermata di registrazione. Per ottenere l'accesso, seguire le istruzioni contenute nel messaggio di errore. Verificare che Internet Explorer Enhanced Security Context sia disabilitato.

Per ottenere una disponibilità elevata, è consigliabile distribuire almeno due connettori. Ogni connettore deve essere registrato separatamente.

## <a name="test-that-the-connector-installed-correctly"></a>Testare che il connettore sia installato correttamente

È possibile verificare che un nuovo connettore sia installato correttamente controllando nel portale di Azure o nel server. 

Nel portale di Azure accedere al tenant e passare a **Azure Active Directory** > **Proxy dell'applicazione**. Tutti i connettori e i gruppi di connettori vengono visualizzati in questa pagina. Selezionare un connettore per visualizzarne i dettagli o spostarlo in un gruppo di connettori diverso. 

Nel server controllare l'elenco dei servizi attivi per il connettore e lo strumento di aggiornamento del connettore. I due servizi dovrebbero avviarsi immediatamente. In caso contrario, attivarli: 

   * **Microsoft AAD Application Proxy Connector** abilita la connettività.

   * **Microsoft AAD Application Proxy Connector Updater** è un servizio di aggiornamento automatico. Lo strumento di aggiornamento controlla la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze.

   ![Servizi del connettore proxy di applicazione - Screenshot](./media/application-proxy-enable/app_proxy_services.png)

Per informazioni sui connettori e su come vengono mantenuti aggiornati, vedere [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-connectors.md).


## <a name="next-steps"></a>Passaggi successivi
È ora possibile [pubblicare applicazioni con il proxy di applicazione](application-proxy-publish-azure-portal.md).

Se ci sono applicazioni in reti separate o posizioni diverse, usare gruppi di connettori per organizzare i diversi connettori in unità logiche. Per altre informazioni, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md).
