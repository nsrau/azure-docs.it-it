---
title: "Proxy app di Azure AD - Attività iniziali e installazione del connettore | Microsoft Docs"
description: Attivare il proxy di applicazione nel portale di Azure e installare i connettori per il proxy inverso.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8bfdc2f4b86e38d707c026365e3ffcce471d5367
ms.lasthandoff: 04/07/2017


---

# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Attività iniziali del proxy di applicazione e installazione del connettore
Questo articolo illustra la procedura per abilitare il proxy di applicazione di Microsoft Azure AD per la directory cloud in Azure AD.

Se non si ha familiarità con le funzionalità offerte dal proxy di applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Prerequisiti del proxy dell'applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

* Una [sottoscrizione di Microsoft Azure AD Basic o Premium](active-directory-editions.md) e una directory di Azure AD di cui si è un amministratore globale.
* Un server che esegue Windows Server 2012 R2 oppure Windows 8.1 o versioni successive in cui poter installare il connettore proxy di applicazione. Il server invia richieste ai servizi proxy di applicazione nel cloud e necessita di una connessione HTTP o HTTPS alle applicazioni che vengono pubblicate.
  * Per l'accesso Single Sign-On alle applicazioni pubblicate, è necessario che il computer sia aggiunto allo stesso dominio di AD delle applicazioni che vengono pubblicate. Per altre informazioni, vedere [Accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

Se l'organizzazione usa server proxy per la connessione a Internet, leggere [Working with existing on-premises proxy servers](application-proxy-working-with-proxy-servers.md) (Utilizzo di server proxy locali esistenti) per informazioni dettagliate sulla relativa configurazione.

## <a name="open-your-ports"></a>Aprire le porte

Se nel percorso è presente un firewall, verificare che sia aperto in modo che il connettore possa inviare richieste HTTPS (TCP) al proxy di applicazione. Il connettore usa queste porte con sottodomini che fanno parte dei domini di alto livello: msappproxy.net e servicebus.windows.net. Assicurarsi di aprire le porte seguenti al traffico **in uscita** :

| Numero della porta | Descrizione |
| --- | --- |
| 80 |Abilitare il traffico HTTP in uscita per la convalida di sicurezza |
| 443 |Abilitare l'autenticazione dell'utente in Azure AD (obbligatoria solo per il processo di registrazione del connettore)<br>Abilitare la sequenza di bootstrap del connettore e gli aggiornamenti automatici<br>Abilitare la comunicazione tra il connettore e il servizio di Azure per le richieste in ingresso  |
| 10100–10120 |Abilitare risposte LOB HTTP inviate al proxy |
| 9350 |Facoltativo, per consentire prestazioni migliori per le richieste in ingresso |
| 8080 |Abilitare la sequenza di bootstrap del connettore e l'aggiornamento automatico del connettore |
| 9090 |Abilitare la registrazione del connettore (necessaria solo per il processo di registrazione del connettore) |
| 9091 |Abilitare il rinnovo automatico dei certificati di attendibilità del connettore |

> [!IMPORTANT]
> La tabella riflette i requisiti della porta per la versione più recente del connettore. Se si dispone ancora di un connettore precedente alla versione 1.5, è necessario anche abilitare 9350, 9352 e 5671. Queste porte abilitano la comunicazione tra i connettori precedenti e il servizio di Azure per le richieste in ingresso. 

Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Assicurarsi anche di abilitare la porta 8080 per NT Authority\System.

Usare lo [strumento per il test delle porte del connettore Proxy di applicazione Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) per verificare che il connettore possa raggiungere il servizio Proxy di applicazione. Assicurarsi almeno che l'area Stati Uniti centrali e l'area più vicina all'utente abbiano segni di spunta verdi. La presenza di più segni di spunta verdi indica una maggiore resilienza. 


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


