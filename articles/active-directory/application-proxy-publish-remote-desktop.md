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
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fd0ecc62fd3cfc860423acd02108648e99f44753
ms.lasthandoff: 04/03/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Pubblicare Desktop remoto con il proxy applicazione Azure AD

Questo articolo illustra come rendere accessibili le distribuzioni di Desktop remoto Windows per gli utenti remoti. Le distribuzioni di Desktop remoto possono risiedere in locale o in reti private, ad esempio distribuzioni IaaS.

> [!NOTE]
> Il proxy applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory (Azure AD). Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Il traffico del protocollo di RDP (Remote Desktop Protocol) può essere pubblicato tramite il proxy applicazione di Azure AD come applicazione proxy pass-through. Questa soluzione consente di risolvere il problema di connettività e offre la protezione di base per la sicurezza, ad esempio il buffer di rete, un front-end Internet più robusto e protezione DDoS (Denial-of-Service) distribuita.

## <a name="remote-desktop-deployment"></a>Distribuzione di Desktop remoto

All'interno della distribuzione di Desktop remoto viene pubblicato il Gateway Desktop remoto, per consentire di convertire RPC (Remote Procedure Call) sul traffico HTTPS in RDP sul traffico UDP (User Datagram Protocol).

È possibile configurare i client in modo che usino client di Desktop remoto, ad esempio MSTSC.exe, per accedere al proxy applicazione di Azure AD. In questo modo è possibile creare una nuova connessione HTTPS al Gateway Desktop remoto usando i suoi connettori. Il gateway non viene quindi esposto direttamente a Internet e tutte le richieste HTTPS verranno prima di tutto terminate nel cloud.

Questa topologia viene illustrata nel diagramma seguente:

 ![Diagramma dei servizi locali di Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configurare l'URL del gateway di Desktop remoto

Quando gli utenti configurano l'URL del Gateway Desktop remoto e attivano il traffico RDP, come di consueto, possono accedere ai file e agli altri metodi.

È possibile eseguire la pubblicazione usando il nome di dominio fornito dal proxy applicazione (msappproxy.net) oppure usando un nome di dominio personalizzato configurato in Azure AD (ad esempio, rdg.contoso.com).

Se i dispositivi client e il file RDP sono già configurati con l'URL del Gateway Desktop remoto, è possibile usare lo stesso nome di dominio e pertanto evitare la modifica. In questo caso il certificato che riguarda il dominio deve essere fornito al proxy applicazione e il rispettivo elenco di revoche di certificati deve essere accessibile tramite Internet.

Se non è configurato alcun URL del Gateway Desktop remoto, gli utenti o gli amministratori possono specificarlo nei client di Desktop remoto (MSTSC) usando la finestra di dialogo Connessione Desktop remoto, come illustrato qui.

 ![Finestra di dialogo Connessione Desktop remoto](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

La finestra di dialogo **Impostazioni connessione** viene visualizzata quando si fa clic su **Impostazioni** nella scheda **Avanzate**.

 ![Finestra Impostazioni connessione nella finestra di dialogo Connessione Desktop remoto](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Accesso Web Desktop remoto

Se l'organizzazione usa il portale Accesso Web Desktop remoto (RDWA) è possibile pubblicare anche tramite il proxy applicazione di Azure Active Directory. È possibile pubblicare in questo portale con la preautenticazione e l'accesso Single Sign-On (SSO).

La topologia dello scenario RDWA viene illustrata nel diagramma seguente:

 ![Diagramma dello scenario RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

Nel caso precedente gli utenti vengono autenticati in Azure AD prima di accedere a RDWA. Se sono già stati autenticati in Azure AD, ad esempio se usano Office 365, non devono eseguire nuovamente l'autenticazione per RDWA.

Quando gli utenti avviano la sessione RDP, devono eseguire nuovamente l'autenticazione sul canale RDP. Questo avviene perché l'accesso SSO da RDWA al Gateway Desktop remoto si basa sull'archiviazione delle credenziali dell'utente finale nel client tramite ActiveX. Questo processo viene attivato dall'autenticazione RDWA basata su form. Quando l'autenticazione RDWA usa Kerbros, non viene presentata alcuna autenticazione basata su form e quindi l'accesso SSO da RDWA a RDP non funziona.

Se RDWA ha bisogno dell'accesso SSO al traffico RDP oppure l'autenticazione RDWA basata su form è stata ampiamente personalizzata, è possibile pubblicare RDWA senza la preautenticazione.

La topologia di questo scenario viene illustrata nel diagramma seguente:

 ![Diagramma dello scenario RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

Nel caso precedente gli utenti devono eseguire l'autenticazione su RDWA usando l'autenticazione basata su form ma non hanno bisogno di eseguire l'autenticazione sul protocollo RDP.

>[!NOTE]
>In entrambi i casi precedenti, non è necessaria alcuna preautenticazione sul traffico RDP. Gli utenti possono quindi accedervi senza prima passare attraverso RDWA.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md)

