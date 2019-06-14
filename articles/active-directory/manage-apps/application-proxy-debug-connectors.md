---
title: Eseguire il debug di connettori Proxy di applicazione - Azure Active Directory | Microsoft Docs
description: Eseguire il debug di problemi con i connettori del Proxy applicazione Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172236"
---
# <a name="debug-application-proxy-connector-issues"></a>Eseguire il debug di problemi del connettore Proxy di applicazione 

Questo articolo consente di risolvere i problemi con i connettori del Proxy applicazione Azure Active Directory (Azure AD). Se si usa il servizio Proxy di applicazione per l'accesso remoto a un'applicazione web in locale, ma si verificano problemi durante la connessione all'applicazione, usare questo diagramma di flusso per il debug di problemi del connettore. 

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo si presuppone che sia stato installato il connettore del Proxy di applicazione e si è verificato un problema. Risolvere i problemi di Proxy dell'applicazione, è consigliabile che iniziare con questo flusso di risoluzione dei problemi per determinare se i connettori del Proxy di applicazione siano configurati correttamente. Se si verificano ancora problemi di connessione per l'applicazione, seguire il flusso di risoluzione dei problemi nelle [problemi di applicazioni Proxy di applicazione di eseguire il Debug](application-proxy-debug-apps.md).  


Per altre informazioni sul Proxy dell'applicazione e usando i suoi connettori, vedere:

- [Accesso remoto alle applicazioni locali tramite il Proxy di applicazione](application-proxy.md)
- [Connettori Proxy di applicazione](application-proxy-connectors.md)
- [Installare e registrare un connettore](application-proxy-add-on-premises-application.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Diagramma di flusso per problemi del connettore

Questo diagramma di flusso illustra i passaggi per il debug di alcuni dei più comuni problemi di connettore. Per informazioni dettagliate su ogni passaggio, vedere la tabella che segue il diagramma di flusso.

![Diagramma di flusso che illustra la procedura per il debug di un connettore](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Azione | Descrizione | 
|---------|---------|---------|
|1 | Trovare il gruppo di connettori assegnato all'app | Probabilmente si dispone di un connettore installato su più server, nel qual caso devono essere i connettori [assegnate a gruppi di connettori](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Per altre informazioni sui gruppi di connettori, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md). |
|2 | Installare il connettore e assegnare un gruppo | Se non si dispone di un connettore installato, vedere [installare e registrare un connettore](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Se il connettore non è assegnato a un gruppo, vedere [assegnare il connettore a un gruppo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se l'applicazione non è assegnata a un gruppo di connettori, vedere [assegnare l'applicazione a un gruppo di connettori](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Eseguire un test di porta nel server del connettore | Nel server del connettore, eseguire un test di porta usando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) o altri tipi di porta test dello strumento per verificare se le porte 80 e 443 siano aperte.|
|4 | Configurare le porte e domini | [Assicurarsi che le porte e domini siano configurate correttamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) per il connettore possa funzionare correttamente, esistono alcune porte che devono essere aperte e gli URL che il server deve essere in grado di accedere. |
|5 | Controllare se un proxy di back-end è in uso | Verificare se i connettori sono Usa server proxy di back-end o ignorando li. Per informazioni dettagliate, vedere [risolvere i problemi del connettore proxy e i problemi di connettività del servizio](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aggiornare il connettore e l'aggiornamento da utilizzare il proxy di back-end | Se un proxy di back-end è in uso, è opportuno assicurarsi che il connettore usi lo stesso proxy. Per informazioni dettagliate sulla risoluzione dei problemi e configurazione dei connettori per lavorare con i server proxy, vedere [lavoro esistenti in locale i server proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Caricare l'URL dell'app interno nel server del connettore | Nel server del connettore, caricare l'URL interno dell'app. |
|8 | Verificare la connettività di rete interna | Si verifica un problema di connettività della rete interna che è in grado di diagnosticare questo flusso di debug. L'applicazione deve essere accessibile internamente per i connettori lavorare. È possibile abilitare e visualizzare registri eventi del connettore, come descritto nella [connettori Proxy di applicazione](application-proxy-connectors.md#under-the-hood). |
|9 | Aumentare il valore di timeout nel back-end | Nel **impostazioni aggiuntive** per l'applicazione, modificare il **Timeout applicazione back-end** impostazione su **Long**. Visualizzare [aggiungere un'app da sito locale ad Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se i problemi persistono, destinazione problemi specifici del flusso, l'app di verifica e SSO il debug di flussi | Usare la [problemi di applicazioni Proxy di applicazione di eseguire il Debug](application-proxy-debug-apps.md) risoluzione dei problemi di flusso. |

## <a name="next-steps"></a>Passaggi successivi


* [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
* [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
* [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
* [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
