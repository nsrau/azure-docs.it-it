---
title: Eseguire il debug dei connettori del proxy di applicazione - Azure Active Directory Documenti Microsoft
description: Problemi di debug con i connettori proxy di applicazione di Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311843"
---
# <a name="debug-application-proxy-connector-issues"></a>Debug dei problemi con il connettore Application Proxy 

Questo articolo consente di risolvere i problemi relativi ai connettori proxy di applicazione di Azure Active Directory (Azure AD). Se si utilizza il servizio proxy di applicazione per l'accesso remoto a un'applicazione Web locale, ma si verificano problemi di connessione all'applicazione, utilizzare questo diagramma di flusso per eseguire il debug dei problemi del connettore. 

## <a name="before-you-begin"></a>Prima di iniziare

In questo articolo si presuppone che sia stato installato il connettore proxy di applicazione e che si stia riscontrando un problema. Durante la risoluzione dei problemi relativi al proxy di applicazione, è consigliabile iniziare con questo flusso di risoluzione dei problemi per determinare se i connettori del proxy di applicazione sono configurati correttamente. Se i problemi di connessione all'applicazione persistono, seguire il flusso di risoluzione dei problemi in [Debug dei problemi dell'applicazione proxy](application-proxy-debug-apps.md)di applicazione .  


Per ulteriori informazioni sul proxy di applicazione e sull'utilizzo dei relativi connettori, vedere:

- [Accesso remoto alle applicazioni locali tramite il proxy di applicazione](application-proxy.md)
- [Connettori proxy di applicazione](application-proxy-connectors.md)
- [Installare e registrare un connettore](application-proxy-add-on-premises-application.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Diagramma di flusso per problemi relativi ai connettori

Questo diagramma di flusso illustra i passaggi per il debug di alcuni dei problemi più comuni relativi ai connettori. Per informazioni dettagliate su ogni passaggio, vedere la tabella che segue il diagramma di flusso.

![Diagramma di flusso che illustra i passaggi per il debug di un connettore](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Azione | Descrizione | 
|---------|---------|---------|
|1 | Trovare il gruppo di connettori assegnato all'app | Probabilmente si dispone di un connettore installato su più server, nel qual caso i connettori devono essere [assegnati ai gruppi](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)di connettori . Per altre informazioni sui gruppi di connettori, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md). |
|2 | Installare il connettore e assegnare un gruppo | Se non è installato un connettore, vedere [Installare e registrare un connettore](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Se si verificano problemi durante l'installazione del connettore, vedere Problema durante [l'installazione del connettore](application-proxy-connector-installation-problem.md).<br></br> Se il connettore non è assegnato a un gruppo, vedere [Assegnare il connettore a un gruppo.](application-proxy-connector-groups.md#create-connector-groups)<br></br>Se l'applicazione non è assegnata a un gruppo di connettori, vedere [Assegnare l'applicazione a un gruppo di connettori](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Eseguire un test della porta sul server del connettore | Sul server del connettore, eseguire un test della porta utilizzando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) o un altro strumento di test delle porte per verificare se le porte 443 e 80 sono aperte.|
|4 | Configurare i domini e le porte | [Assicurarsi che i domini e le porte siano configurati correttamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Per il corretto funzionamento del connettore, è necessario che alcune porte siano aperte e URL a cui il server deve essere in grado di accedere. |
|5 | Verificare se è in uso un proxy back-end | Verificare se i connettori utilizzano server proxy back-end o ignorarli. Per informazioni dettagliate, vedere Risoluzione dei problemi relativi al [proxy del connettore e ai problemi](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)di connettività del servizio . |
|6 | Aggiornare il connettore e il programma di aggiornamento per l'utilizzo del proxy back-end | Se è in uso un proxy back-end, è consigliabile assicurarsi che il connettore utilizzi lo stesso proxy. Per informazioni dettagliate sulla risoluzione dei problemi e sulla configurazione dei connettori per l'utilizzo con i server proxy, vedere [Utilizzo dei server proxy locali esistenti.](application-proxy-configure-connectors-with-proxy-servers.md) |
|7 | Caricare l'URL interno dell'app sul server del connettore | Nel server del connettore, caricare l'URL interno dell'app. |
|8 | Controllare la connettività di rete interna | Si è verificato un problema di connettività nella rete interna che questo flusso di debug non è in grado di diagnosticare. L'applicazione deve essere accessibile internamente affinché i connettori funzionino. È possibile abilitare e visualizzare i registri eventi del connettore come descritto in [Connettori proxy applicazioni](application-proxy-connectors.md#under-the-hood). |
|9 | Allungare il valore di timeout nel back-end | In **Impostazioni aggiuntive** per l'applicazione modificare l'impostazione **Timeout applicazione back-end** **su Lungo**. Vedere [Aggiungere un'app locale ad Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) |
|10 | Se i problemi persistono, indirizzare problemi di flusso specifici, esaminare i flussi di debug di app e SSO | Utilizzare il [Debug applicazione proxy di applicazione problemi di](application-proxy-debug-apps.md) risoluzione dei problemi di flusso. |

## <a name="next-steps"></a>Passaggi successivi


* [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
* [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
* [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
* [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
