---
title: "Proxy app di Azure AD - Attività iniziali e installazione del connettore | Microsoft Docs"
description: Attivare il proxy di applicazione nel portale di Azure e installare i connettori per il proxy inverso.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 09497144ef4047c1191f3c02afccb881d48aaf3c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Attività iniziali del proxy di applicazione e installazione del connettore
Questo articolo illustra la procedura per abilitare il proxy di applicazione di Microsoft Azure AD per la directory cloud in Azure AD.

Se non si conoscono ancora i vantaggi in termini di produttività e sicurezza offerti all'organizzazione dal proxy di applicazione, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Prerequisiti del proxy dell'applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

* Una [sottoscrizione di Microsoft Azure AD Basic o Premium](active-directory-editions.md) e una directory di Azure AD di cui si è un amministratore globale.
* Un server che esegue Windows Server 2012 R2 o 2016 in cui poter installare il connettore del proxy di applicazione. Il server deve potersi connettere ai servizi proxy di applicazione nel cloud e alle applicazioni locali che vengono pubblicate.
  * Per l'accesso Single Sign-On alle applicazioni pubblicate usando la delega vincolata Kerberos, è necessario che il computer sia aggiunto allo stesso dominio di AD delle applicazioni che vengono pubblicate. Per informazioni, vedere [KCD for single sign-on with Application Proxy](active-directory-application-proxy-sso-using-kcd.md) (KDC per l'Accesso Single Sign-On con il proxy di applicazione).

Se l'organizzazione usa i server proxy per la connessione a Internet, vedere [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md) per informazioni dettagliate su come configurarli prima di iniziare a usare il proxy di applicazione.

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
   >Per informazioni sull'aggiornamento dei connettori alla versione più recente, vedere [Comprendere i connettori del proxy di applicazione di Azure AD](application-proxy-understand-connectors.md#automatic-updates).

2. Se il firewall o il proxy consente di inserire DNS nell'elenco elementi consentiti, è possibile aggiungere connessioni a msappproxy.net e servicebus.windows.net. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) che vengono aggiornati ogni settimana.

3. Microsoft usa quattro indirizzi per verificare i certificati. Se non è già stato fatto per altri prodotti, consentire l'accesso agli URL seguenti:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Il connettore deve poter accedere a login.windows.net e login.microsoftonline.com per il processo di registrazione.

5. Usare lo [strumento per il test delle porte del connettore Proxy di applicazione Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) per verificare che il connettore possa raggiungere il servizio Proxy di applicazione. Assicurarsi almeno che l'area Stati Uniti centrali e l'area più vicina all'utente abbiano segni di spunta verdi. Tuttavia, la presenza di più segni di spunta verde indica una maggiore resilienza.

## <a name="install-and-register-a-connector"></a>Installare e registrare un connettore
1. Accedere come amministratore al [portale di Azure](https://portal.azure.com/).
2. La directory corrente viene visualizzata sotto il nome utente nell'angolo superiore destro. Se è necessario cambiare directory, selezionare questa icona.
3. Passare a **Azure Active Directory** > **Proxy dell'applicazione**.

   ![Accedere a Proxy di applicazione](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Selezionare **Scarica connettore**.

   ![Scaricare il connettore](./media/active-directory-application-proxy-enable/download_connector.png)

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

   ![Servizi del connettore proxy di applicazione - Screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Per informazioni sui connettori e su come vengono mantenuti aggiornati, vedere [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md).


## <a name="next-steps"></a>Passaggi successivi
È ora possibile [pubblicare applicazioni con il proxy di applicazione](application-proxy-publish-azure-portal.md).

Se ci sono applicazioni in reti separate o posizioni diverse, usare gruppi di connettori per organizzare i diversi connettori in unità logiche. Per altre informazioni, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](active-directory-application-proxy-connectors-azure-portal.md).
