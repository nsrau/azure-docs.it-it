---
title: "Proxy app di Azure AD - Attività iniziali e installazione del connettore | Microsoft Docs"
description: Attivare il proxy di applicazione nel portale di Azure e installare i connettori per il proxy inverso.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: kgremban
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 3abbc7efb5fe4029de14b31473407086e582681e
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017

---

# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Attività iniziali del proxy di applicazione e installazione del connettore
Questo articolo illustra la procedura per abilitare il proxy di applicazione di Microsoft Azure AD per la directory cloud in Azure AD.

Se non si ha familiarità con le funzionalità offerte dal proxy di applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Prerequisiti del proxy dell'applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

* Una [sottoscrizione di Microsoft Azure AD Basic o Premium](active-directory-editions.md) e una directory di Azure AD di cui si è un amministratore globale.
* Un server che esegue Windows Server 2012 R2 o 2016 in cui poter installare il connettore del proxy di applicazione. Il server invia richieste ai servizi proxy di applicazione nel cloud e necessita di una connessione HTTP o HTTPS alle applicazioni che vengono pubblicate.
  * Per l'accesso Single Sign-On alle applicazioni pubblicate, è necessario che il computer sia aggiunto allo stesso dominio di AD delle applicazioni che vengono pubblicate. Per altre informazioni, vedere [Accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

Se l'organizzazione usa server proxy per la connessione a Internet, vedere [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md) per informazioni dettagliate sulla relativa configurazione.

## <a name="open-your-ports"></a>Aprire le porte

Per preparare l'ambiente per il proxy di applicazione di Azure AD, è necessario abilitare prima la comunicazione ai data center di Azure. Se nel percorso è presente un firewall, verificare che sia aperto in modo che il connettore possa inviare richieste HTTPS (TCP) al proxy di applicazione.

1. Aprire le porte seguenti al traffico **in uscita**:

   | Numero della porta | Uso |
   | --- | --- |
   | 80 | Download degli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
   | 443 | Tutte le comunicazioni in uscita con il servizio proxy di applicazione |

   Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete.

   > [!IMPORTANT]
   > La tabella contiene i requisiti relativi alle porte per le versioni del connettore 1.5.132.0 e successive. Se la versione del connettore è precedente, è anche necessario abilitare le porte seguenti: 5671, 8080, 9090, 9091, 9350, 9352 e 10100–10120.
   >
   >Per informazioni sull'aggiornamento dei connettori alla versione più recente, vedere [Comprendere i connettori del proxy di applicazione di Azure AD](application-proxy-understand-connectors.md#automatic-updates).

2. Se il firewall o il proxy consente di inserire DNS nell'elenco elementi consentiti, è possibile aggiungere connessioni a msappproxy.net e servicebus.windows.net. Altrimenti è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) che vengono aggiornati ogni settimana.

3. Usare lo [strumento per il test delle porte del connettore Proxy di applicazione Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) per verificare che il connettore possa raggiungere il servizio Proxy di applicazione. Assicurarsi almeno che l'area Stati Uniti centrali e l'area più vicina all'utente abbiano segni di spunta verdi. La presenza di più segni di spunta verdi indica una maggiore resilienza.

## <a name="install-and-register-a-connector"></a>Installare e registrare un connettore
1. Accedere come amministratore al [portale di Azure](https://portal.azure.com/).
2. La directory corrente viene visualizzata sotto il nome utente nell'angolo superiore destro. Se è necessario cambiare directory, selezionare questa icona.
3. Passare a **Azure Active Directory** > **Proxy dell'applicazione**.

   ![Accedere a Proxy di applicazione](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Selezionare **Scarica connettore**.

   ![Scaricare il connettore](./media/active-directory-application-proxy-enable/download_connector.png)

4. Eseguire **AADApplicationProxyConnectorInstaller.exe** nel server preparato in base ai prerequisiti.
5. Seguire le istruzioni della procedura guidata da installare.
6. Durante l'installazione viene richiesto di registrare il connettore con il proxy di applicazione del tenant di Azure AD.

   * Fornire le credenziali di amministratore globale di Azure AD. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
   * Verificare che l'amministratore che registra il connettore si trovi nella stessa directory in cui è stato abilitato il servizio proxy dell'applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias di tale dominio.
   * Se la **sicurezza avanzata di Internet Explorer** è impostata su **Attiva** nel server in cui si vuole installare il connettore, la schermata di registrazione potrebbe essere bloccata. Seguire le istruzioni nel messaggio di errore per consentire l'accesso. Verificare che Internet Explorer Enhanced Security Context sia disabilitato.
   * Se la registrazione del connettore non riesce, vedere [Risolvere i problemi del Proxy applicazione](active-directory-application-proxy-troubleshoot.md).  
7. Al termine dell'installazione, due nuovi servizi vengono aggiunti al server:

   * **Microsoft AAD Application Proxy Connector** abilita la connettività.

   * **Microsoft AAD Application Proxy Connector Updater** è un servizio di aggiornamento automatico che verifica periodicamente la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze.

   ![Servizi del connettore proxy di applicazione - Screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Per informazioni sui connettori, vedere [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md) (Informazioni sui connettori proxy di applicazione di Azure AD).

Per ottenere una disponibilità elevata, è consigliabile distribuire almeno due connettori. Per distribuire altri connettori, ripetere i passaggi 2 e 3. Ogni connettore deve essere registrato separatamente.

Per disinstallare il connettore, disinstallare sia il servizio connettore che il servizio di aggiornamento. Riavviare il computer per rimuovere completamente il servizio.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile [pubblicare applicazioni con il proxy di applicazione](application-proxy-publish-azure-portal.md).

Se sono presenti applicazioni in reti separate o posizioni diverse, è possibile usare gruppi di connettori per organizzare i diversi connettori in unità logiche. Per altre informazioni, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](active-directory-application-proxy-connectors-azure-portal.md).

