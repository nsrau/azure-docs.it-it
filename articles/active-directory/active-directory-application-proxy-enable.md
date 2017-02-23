---
title: Abilitare il proxy di applicazione di Azure AD | Documentazione Microsoft
description: Attivare il proxy di applicazione nel portale di Azure classico e installare i connettori per il proxy inverso.
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2d5ea8109f5fde5f2b480ceeea34c365b619fab5
ms.openlocfilehash: 5f42292fa9238dadd717d5c2e9530b22ee10d943


---

# <a name="enable-application-proxy-in-the-azure-portal"></a>Abilitare il proxy di applicazione nel portale di Azure
Questo articolo illustra la procedura per abilitare il proxy di applicazione di Microsoft Azure AD per la directory cloud in Azure AD.

Se non si ha familiarità con le funzionalità offerte dal proxy di applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Prerequisiti del proxy dell'applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

* Una [sottoscrizione di Microsoft Azure AD Basic o Premium](active-directory-editions.md) e una directory di Azure AD di cui si è un amministratore globale.
* Un server che esegue Windows Server 2012 R2 oppure Windows 8.1 o versioni successive in cui poter installare il connettore proxy di applicazione. Il server invia richieste ai servizi proxy di applicazione nel cloud e necessita di una connessione HTTP o HTTPS alle applicazioni che vengono pubblicate.
  
  * Per l'accesso Single Sign-On alle applicazioni pubblicate, è necessario che il computer sia aggiunto allo stesso dominio di AD delle applicazioni che vengono pubblicate. Per altre informazioni, vedere [Accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)
* Se nel percorso è presente un firewall, verificare che sia aperto in modo che il connettore possa inviare richieste HTTPS (TCP) al proxy di applicazione. Il connettore usa queste porte con sottodomini che fanno parte dei domini di alto livello: msappproxy.net e servicebus.windows.net. Assicurarsi di aprire le porte seguenti al traffico **in uscita** :
  
  | Numero della porta | Descrizione |
  | --- | --- |
  | 80 |Abilitare il traffico HTTP in uscita per la convalida di sicurezza |
  | 443 |Abilitare l'autenticazione dell'utente in Azure AD (obbligatoria solo per il processo di registrazione del connettore) |
  | 10100–10120 |Abilitare risposte LOB HTTP inviate al proxy |
  | 9352, 5671 |Abilitare la comunicazione tra il connettore e il servizio di Azure per le richieste in ingresso. |
  | 9350 |Facoltativo, per consentire prestazioni migliori per le richieste in ingresso |
  | 8080 |Abilitare la sequenza di bootstrap del connettore e l'aggiornamento automatico del connettore |
  | 9090 |Abilitare la registrazione del connettore (necessaria solo per il processo di registrazione del connettore) |
  | 9091 |Abilitare il rinnovo automatico dei certificati di attendibilità del connettore |
  
    Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Assicurarsi anche di abilitare la porta 8080 per NT Authority\System.
* Se l'organizzazione usa server proxy per la connessione a Internet, vedere il post di blog [Working with existing on-premises proxy servers](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) (Utilizzo di server proxy locali esistenti) per informazioni dettagliate sulla relativa configurazione.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Passaggio 1: abilitare il proxy dell’applicazione in Azure AD
1. Accedere come amministratore al [portale di Azure classico](https://manage.windowsazure.com/).
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.
   
    ![Active Directory - Icona](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Selezionare **Configura** nella pagina della directory e scorrere verso il basso fino a **Proxy dell'applicazione**.
4. Impostare **Abilita servizi proxy dell'applicazione per questa directory** su **Abilitati**.
   
    ![Abilitare il proxy dell’applicazione](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Selezionare **Scarica ora**. Verrà visualizzata la pagina **Azure AD Application Proxy Connector Download**(Download connettore proxy applicazione di Azure AD). Leggere e accettare le condizioni di licenza e fare clic su **Scarica** per salvare il file di Windows Installer (con estensione exe) per il connettore.

## <a name="step-2-install-and-register-the-connector"></a>Passaggio 2: installare e registrare il connettore
1. Eseguire **AADApplicationProxyConnectorInstaller.exe** nel server preparato in base ai prerequisiti.
2. Seguire le istruzioni della procedura guidata da installare.
3. Durante l'installazione verrà richiesto di registrare il connettore con il proxy di applicazione del tenant di Azure AD.
   
   * Fornire le credenziali di amministratore globale di Azure AD. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
   * Verificare che l'amministratore che registra il connettore si trovi nella stessa directory in cui è stato abilitato il servizio proxy dell'applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias di tale dominio.
   * Se la **sicurezza avanzata di Internet Explorer** è impostata su **Attiva** nel server in cui si vuole installare il connettore, la schermata di registrazione potrebbe essere bloccata. Seguire le istruzioni nel messaggio di errore per consentire l'accesso. Verificare che Internet Explorer Enhanced Security Context sia disabilitato.
   * Se la registrazione del connettore non riesce, vedere [Risolvere i problemi del Proxy applicazione](active-directory-application-proxy-troubleshoot.md).  
4. Al termine dell'installazione, due nuovi servizi vengono aggiunti al server:
   
   * **Microsoft AAD Application Proxy Connector** abilita la connettività.
     
     * **Microsoft AAD Application Proxy Connector Updater** è un servizio di aggiornamento automatico che verifica periodicamente la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze.
     
     ![Servizi del connettore proxy di applicazione - Screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Fare clic su **Fine** nella finestra di installazione.

Per informazioni sui connettori, vedere [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md) (Informazioni sui connettori proxy di applicazione di Azure AD). 

Per ottenere una disponibilità elevata, è consigliabile distribuire almeno due connettori. Per distribuire altri connettori, ripetere i precedenti passaggi 2 e 3. Ogni connettore deve essere registrato separatamente.

Per disinstallare il connettore, disinstallare sia il servizio connettore che il servizio di aggiornamento. Riavviare il computer per rimuovere completamente il servizio.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile [pubblicare applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md).

Se sono presenti applicazioni in reti separate o posizioni diverse, è possibile usare gruppi di connettori per organizzare i diversi connettori in unità logiche. Per altre informazioni, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](active-directory-application-proxy-connectors.md).




<!--HONumber=Feb17_HO2-->


