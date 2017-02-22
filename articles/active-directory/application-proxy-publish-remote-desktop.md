---
title: Pubblicare Desktop remoto con il proxy applicazione Azure Active Directory | Documentazione Microsoft
description: Tratta i fondamenti dei connettori del proxy applicazione Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 40ddf7d6648561eab855b41e76a5ca509c6ca4ef
ms.openlocfilehash: 05130d728c579e0b778bdc8ec49e01f2223c2bba


---

# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>Pubblicare Desktop remoto con il proxy applicazione Azure Active Directory

Questo articolo illustra come rendere accessibili le distribuzioni di Desktop remoto per gli utenti remoti. Queste distribuzioni di Desktop remoto possono risiedere in locale o in reti private, ad esempio distribuzioni IaaS. 

> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 
 

Il traffico del protocollo di Desktop remoto può essere pubblicato tramite il proxy applicazione come applicazione proxy pass-through. Questa soluzione consente di risolvere il problema di connettività e offre la protezione di base per la sicurezza, ad esempio la bufferizzazione di rete, un front-end Internet più robusto e protezione DDoS. 

##<a name="remote-desktop-deployment"></a>Distribuzione di Desktop remoto

All'interno della distribuzione di Desktop remoto, il gateway di Desktop remoto viene pubblicato in modo che sia in grado di convertire la chiamata RPC sul traffico HTTPS in RDP sul traffico UDP.

È possibile configurare i client per l'uso di client di Desktop remoto, ad esempio MSTSC.exe, per accedere al proxy applicazione Azure AD. In questo modo è possibile creare una nuova connessione HTTPS al gateway di Desktop remoto usando i suoi connettori. Così facendo, il gateway di Desktop remoto non sarà esposto direttamente a Internet e tutte le richieste HTTPS verranno terminate prima nel cloud. 

Il diagramma seguente illustra questa topologia.

 ![Servizi Azure AD locali](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configurare l'URL del gateway di Desktop remoto

Se sono gli utenti a configurare l'URL del gateway di Desktop remoto, quando attivano il traffico RDP nel modo consueto saranno in grado di accedere ai file e ad altri metodi.

La pubblicazione può essere eseguita usando il nome di dominio fornito dal proxy applicazione (msappproxy.net) o tramite un nome di dominio personalizzato configurato in Azure AD, ad esempio rdg.contoso.com. 

Se i dispositivi client e il file RDP sono già configurati con l'URL del gateway di Desktop remoto, è possibile usare lo stesso nome di dominio e pertanto evitare la modifica. In questo caso il certificato che riguarda il dominio deve essere fornito al proxy applicazione e il sui CRL deve essere accessibile tramite Internet.

Se non è configurato un URL del gateway di Desktop remoto, gli utenti o gli amministratori possono specificarlo nei client di Desktop remoto (MSTSC) usando la casella Connessione Desktop remoto, come illustrato di seguito.

 ![Servizi Azure AD locali](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

La casella Impostazioni di connessione viene visualizzata quando si fa clic su **Impostazioni** nella scheda **Avanzate**.

 ![Servizi Azure AD locali](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Accesso Web Desktop remoto

Se l'organizzazione usa il portale Accesso Web Desktop remoto (RDWA) è possibile pubblicare anche tramite il proxy applicazione Azure Active Directory. È possibile pubblicare in questo portale con la preautenticazione e l'accesso Single Sign-On (SSO).

Il diagramma seguente illustra la topologia di questo scenario.

 ![Servizi Azure AD locali](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

Nel caso precedente gli utenti saranno autenticati in Azure AD prima di accedere a RDWA. Se sono già stati autenticati in Azure AD (ad esempio se usano Office 365) non devono autenticarsi nuovamente per RDWA.

Quando agli utenti avviano la sessione RDP, devono autenticarsi nuovamente sul canale RDP. Questo avviene perché l'accesso SSO da RDWA al gateway di Desktop remoto si basa sull'archiviazione delle credenziali dell'utente finale nel client usando ActiveX. Questo processo viene attivato dall'autenticazione RDWA basata su form. Quando l'autenticazione RDWA usa Kerbros, non viene presentata alcuna autenticazione basata su form e pertanto l'accesso SSO da RDWA a RDP non funzionerà.

Se RDWA ha bisogno dell'accesso SSO al traffico RDP oppure l'autenticazione RDWA basata su form è stata ampiamente personalizzata, è possibile pubblicare RDWA senza la preautenticazione.

Il diagramma seguente illustra la topologia di questo scenario.

 ![Servizi Azure AD locali](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

Nel caso precedente gli utenti dovranno eseguire l'autenticazione su RDWA usando l'autenticazione basata su form ma non avranno bisogno di autenticarsi sul protocollo RDP. 

È importante notare in entrambi i casi che non è richiesta alcuna preautenticazione sul traffico RDP. Di conseguenza gli utenti possono accedervi senza prima passare attraverso RDWA.

##<a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso remoto a SharePoint con il proxy applicazione Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Abilitare il proxy applicazione nel portale di Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


