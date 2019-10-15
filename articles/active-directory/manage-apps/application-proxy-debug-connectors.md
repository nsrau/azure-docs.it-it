---
title: Eseguire il debug di connettori proxy di applicazione-Azure Active Directory | Microsoft Docs
description: Problemi di debug con i connettori del proxy di applicazione Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311843"
---
# <a name="debug-application-proxy-connector-issues"></a>Problemi di debug del connettore del proxy di applicazione 

Questo articolo consente di risolvere i problemi relativi ai connettori del proxy di applicazione Azure Active Directory (Azure AD). Se si usa il servizio proxy di applicazione per l'accesso remoto a un'applicazione Web locale, ma si verificano problemi di connessione all'applicazione, usare questo diagramma di flusso per eseguire il debug dei problemi del connettore. 

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia stato installato il connettore del proxy di applicazione e si siano verificati problemi. Quando si risolvono i problemi del proxy di applicazione, è consigliabile iniziare con questo flusso di risoluzione dei problemi per determinare se i connettori del proxy di applicazione sono configurati correttamente. Se si verificano ancora problemi di connessione all'applicazione, seguire il flusso di risoluzione dei [problemi di debug](application-proxy-debug-apps.md)dell'applicazione proxy di applicazione.  


Per ulteriori informazioni sul proxy di applicazione e sull'utilizzo dei relativi connettori, vedere:

- [Accesso remoto alle applicazioni locali tramite il proxy di applicazione](application-proxy.md)
- [Connettori del proxy di applicazione](application-proxy-connectors.md)
- [Installare e registrare un connettore](application-proxy-add-on-premises-application.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Diagramma di flusso per i problemi del connettore

Questo diagramma di flusso illustra i passaggi per eseguire il debug di alcuni dei problemi più comuni del connettore. Per informazioni dettagliate su ogni passaggio, vedere la tabella che segue il diagramma di flusso.

![Diagramma di flusso con i passaggi per il debug di un connettore](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Azione | Descrizione | 
|---------|---------|---------|
|1 | Trovare il gruppo di connettori assegnato all'app | È probabile che sia installato un connettore su più server, nel qual caso i connettori devono essere [assegnati ai gruppi di connettori](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Per altre informazioni sui gruppi di connettori, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md). |
|2 | Installare il connettore e assegnare un gruppo | Se non è installato un connettore, vedere [installare e registrare un connettore](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Se si verificano problemi durante l'installazione del connettore, vedere [problemi di installazione del](application-proxy-connector-installation-problem.md)connettore.<br></br> Se il connettore non è assegnato a un gruppo, vedere [assegnare il connettore a un gruppo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se l'applicazione non è assegnata a un gruppo di connettori, vedere [assegnare l'applicazione a un gruppo di connettori](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Eseguire un test di porta sul server del connettore | Sul server del connettore, eseguire un test di porta usando [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) o un altro strumento di test della porta per verificare se sono aperte le porte 443 e 80.|
|4 | Configurare i domini e le porte | Verificare [che i domini e le porte siano configurati correttamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Per il corretto funzionamento del connettore, è necessario aprire alcune porte e gli URL a cui il server deve essere in grado di accedere. |
|5 | Verificare se è in uso un proxy back-end | Verificare se i connettori utilizzano server proxy back-end o ignorarli. Per informazioni dettagliate, vedere [risolvere i problemi del proxy del connettore e problemi di connettività del servizio](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aggiornare il connettore e l'aggiornamento per usare il proxy back-end | Se è in uso un proxy back-end, è necessario assicurarsi che il connettore usi lo stesso proxy. Per informazioni dettagliate sulla risoluzione dei problemi e sulla configurazione dei connettori per l'uso con i server proxy, vedere [usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Caricare l'URL interno dell'app nel server del connettore | Nel server connettore caricare l'URL interno dell'app. |
|8 | Verifica la connettività di rete interna | Si è verificato un problema di connettività nella rete interna che questo flusso di debug non è in grado di diagnosticare. Per il corretto funzionamento dei connettori, l'applicazione deve essere accessibile internamente. È possibile abilitare e visualizzare i registri eventi del connettore come descritto in [connettori del proxy di applicazione](application-proxy-connectors.md#under-the-hood). |
|9 | Estendere il valore di timeout nel back-end | Nelle **Impostazioni aggiuntive** per l'applicazione impostare **Timeout applicazione back-end** su **Long**. Vedere [aggiungere un'app locale a Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se i problemi permangono, individuare problemi specifici del flusso, esaminare i flussi di debug di app e SSO | Usare il flusso di applicazione [debug applicazione proxy](application-proxy-debug-apps.md) di risoluzione dei problemi. |

## <a name="next-steps"></a>Passaggi successivi


* [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
* [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
* [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
* [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
