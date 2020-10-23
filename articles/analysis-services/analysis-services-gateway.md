---
title: Gateway dati locale per Azure Analysis Services | Microsoft Docs
description: Un gateway locale è necessario se il server Analysis Services di Azure si connette a origini dati locali.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0d8960ddd8f617c59d6ac025fafe413256bc5b94
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107607"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Connessione a origini dati locali con gateway dati locale

Il gateway dati locale garantisce il trasferimento sicuro dei dati tra le origini dati locali e i server Azure Analysis Services nel cloud. Oltre a usare più server Azure Analysis Services nella stessa area, la versione più recente del gateway funziona anche con app per la logica di Azure, Power BI, Power Apps e Power automatizzate. Sebbene il gateway installato sia lo stesso in tutti i servizi, Azure Analysis Services e le app per la logica hanno alcuni passaggi aggiuntivi.

Le informazioni fornite di seguito sono specifiche del funzionamento di Azure Analysis Services con il gateway dati locale. Per altre informazioni sul gateway in generale e sul relativo funzionamento con altri servizi, vedere [che cos'è un gateway dati locale?](/data-integration/gateway/service-gateway-onprem).

Per Azure Analysis Services, il primo processo di configurazione con il gateway è un processo in quattro parti:

- **Scaricare ed eseguire il programma di installazione**: questo passaggio consente di installare un servizio gateway in un computer dell'organizzazione. È anche possibile accedere ad Azure usando un account in Azure AD del [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Gli account Azure B2B (guest) non sono supportati.

- **Registrare il gateway**: in questo passaggio vengono specificati un nome e una chiave di ripristino per il gateway, si seleziona un'area e si registra il gateway con il servizio cloud Gateway. La risorsa del gateway può essere registrata in qualsiasi area, ma è consigliabile che si trovi nella stessa area dei server Analysis Services. 

- **Creare una risorsa del gateway in Azure** : in questo passaggio si crea una risorsa del gateway in Azure.

- **Connettere la risorsa gateway ai server** : dopo avere creato una risorsa del gateway, è possibile iniziare a connettervi server. È possibile connettere più server e altre risorse purché si trovino nella stessa area.

## <a name="installing"></a>Installazione

Quando si installa per un ambiente di Azure Analysis Services, è importante attenersi alla procedura descritta in [installare e configurare il gateway dati locale per Azure Analysis Services](analysis-services-gateway-install.md). Questo articolo è specifico per Azure Analysis Services. Include passaggi aggiuntivi necessari per configurare una risorsa gateway dati locale in Azure e connettere il server Azure Analysis Services alla risorsa.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Connessione a una risorsa del gateway in una sottoscrizione diversa

Si consiglia di creare la risorsa del gateway di Azure nella stessa sottoscrizione del server. Tuttavia, è possibile configurare i server per la connessione a una risorsa del gateway in un'altra sottoscrizione. La connessione a una risorsa del gateway in un'altra sottoscrizione non è supportata quando si configurano le impostazioni del server esistenti o si crea un nuovo server nel portale, ma è possibile configurarlo usando PowerShell. Per altre informazioni, vedere [connettere la risorsa gateway al server](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Porte e impostazioni di comunicazione

Il gateway crea una connessione in uscita al bus di servizio di Azure. Comunica sulle porte in uscita seguenti: TCP 443 (impostazione predefinita), 5671, 5672 e da 9350 a 9354.  Non sono richieste porte in ingresso.

Potrebbe essere necessario includere indirizzi IP per l'area dati nel firewall. È possibile scaricare l'[elenco degli indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=56519). L'elenco viene aggiornato ogni settimana. Gli indirizzi IP inclusi nell'elenco degli indirizzi IP dei data center di Azure sono nella notazione CIDR. Per altre informazioni, vedere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Di seguito sono riportati i nomi di dominio completi usati dal gateway.

| Nomi di dominio | Porte in uscita | Descrizione |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usato per scaricare il programma di installazione. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |
| *.microsoftonline-p.com |443 |Usato per l'autenticazione, a seconda della configurazione. |
| dc.services.visualstudio.com    |443 |Usato da AppInsights per raccogliere i dati di telemetria. |

## <a name="next-steps"></a>Passaggi successivi 

Gli articoli seguenti sono inclusi nel contenuto generale del gateway dati locale applicabile a tutti i servizi supportati dal gateway:

* [Domande frequenti sul gateway dati locale](/data-integration/gateway/service-gateway-onprem-faq)   
* [Usare l'app del gateway dati locale](/data-integration/gateway/service-gateway-app)   
* [Amministrazione a livello di tenant](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Configurare le impostazioni proxy](/data-integration/gateway/service-gateway-proxy)   
* [Modificare impostazioni di comunicazione](/data-integration/gateway/service-gateway-communication)   
* [Configurare i file di log](/data-integration/gateway/service-gateway-log-files)   
* [Risolvere i problemi](/data-integration/gateway/service-gateway-tshoot)
* [Monitorare e ottimizzare le prestazioni del gateway](/data-integration/gateway/service-gateway-performance)