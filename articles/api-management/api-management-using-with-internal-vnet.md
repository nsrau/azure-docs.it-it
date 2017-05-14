---
title: Come usare Gestione API di Azure con le reti virtuali interne | Microsoft Docs
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna.
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b9df2c3e7f49a47bfd714f28c5ab53590ca9a719
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>Uso del servizio Gestione API di Azure con una rete virtuale interna
Grazie alle reti virtuali di Azure, Gestione API è in grado di gestire API che non hanno accesso a Internet. È disponibile una serie di tecnologie VPN per effettuare tale connessione; Gestione API può essere installato in due modi principali all'interno della rete virtuale:
* Esterno
* Interno

## <a name="overview"> </a>Panoramica
Quando Gestione API viene installato in modalità di rete virtuale interna, tutti gli endpoint di servizio, ovvero gateway, portale per sviluppatori, portale di pubblicazione, gestione diretta e GIT, sono visibili solo all'interno di una rete virtuale i cui accessi sono gestiti dall'utente. Nessuno degli endpoint di servizio è registrato nel server DNS pubblico.

Usando Gestione API in modalità interna è possibile riscontrare gli scenari seguenti
* Consentire un accesso esterno sicuro da parte di terzi alle API ospitate in un data center privato, tramite connessioni VPN da sito a sito o ExpressRoute.
* Abilitare scenari cloud ibrid esponendo le API basate su cloud e locali tramite un gateway comune.
* Gestire le API ospitate in più aree geografiche usando un singolo endpoint del gateway. 

## <a name="enable-vpn"> </a>Creazione di un servizio Gestione API in una rete virtuale interna
Il servizio Gestione API in una rete virtuale interna viene ospitato dietro un servizio di bilanciamento del carico interno (ILB). L'indirizzo IP del servizio di bilanciamento del carico interno si trova nell'intervallo [RFC1918](http://www.faqs.org/rfcs/rfc1918.html).  

### <a name="enable-vnet-connection-using-azure-portal"></a>Abilitare la connessione della rete virtuale usando il portale di Azure
Creare innanzitutto il servizio Gestione API, attenendosi alla procedura [Creare un servizio Gestione API][Create API Management service]. Quindi configurare Gestione API per la distribuzione all'interno di una rete virtuale.

![Menu per la configurazione di Gestione API nella rete virtuale interna][api-management-using-internal-vnet-menu]

Una volta completata la distribuzione, nel dashboard dovrebbe essere visualizzato l'indirizzo IP virtuale interno del servizio.

![Dashboard di gestione API con rete virtuale interna configurata][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>Abilitare la connessione della rete virtuale usando i cmdlet di PowerShell
È inoltre possibile abilitare la connettività della rete virtuale utilizzando i cmdlet di PowerShell.

* **Creare un servizio Gestione API all'interno di una rete virtuale**: usare il cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) per creare e un servizio Gestione API di Azure all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

* **Distribuire un servizio Gestione API esistente all'interno di una rete virtuale**: usare il cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) per spostare un servizio Gestione API di Azure esistente all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

## <a name="apim-dns-configuration"></a>Configurazione del DNS
Quando si usa Gestione API in modalità di rete virtuale esterna, il DNS è gestito da Azure. Per la modalità di rete virtuale interna, è necessario gestire un proprio DNS.

> [!NOTE]
> Il servizio Gestione API non ascolta le richieste in arrivo agli indirizzi IP. Risponde solo alle richieste inviate al nome host configurato sui relativi endpoint di servizio, che includono gateway, portale per sviluppatori, portale di pubblicazione, endpoint di gestione diretta e GIT.

### <a name="access-on-default-host-names"></a>Accesso ai nomi host predefiniti:
Quando si crea un servizio Gestione API nel Cloud Azure pubblico, denominato ad esempio "contoso", vengono configurati i seguenti endpoint di servizio per impostazione predefinita.

>    Gateway o Proxy: contoso.azure-api.net

> Portale di pubblicazione portale per sviluppatori: contoso.portal.azure-api.net

> Endpoint di gestione diretta: contoso.management.azure-api.net

>    GIT: contoso.scm.azure-api.net

Per accedere a questi endpoint del servizio Gestione API è possibile creare una macchina virtuale in una subnet connessa alla rete virtuale in cui viene distribuito Gestione API. Supponendo che l'indirizzo IP virtuale interno per il servizio sia 10.0.0.5, è possibile eseguire il mapping del file host (%SystemDrive%\drivers\etc\hosts) come segue:

> 10.0.0.5      contoso.azure-api.net

> 10.0.0.5      contoso.portal.azure-api.net

> 10.0.0.5      contoso.management.azure-api.net

> 10.0.0.5      contoso.scm.azure-api.net

È quindi possibile accedere tutti gli endpoint di servizio dalla macchina virtuale creata. Se si usa un server DNS personalizzato in una rete virtuale, è anche possibile creare record DNS A e accedere agli endpoint da qualsiasi punto nella rete virtuale. 

### <a name="access-on-custom-domain-names"></a>Accedere a nomi di dominio personalizzati:
Se non si desidera accedere al servizio Gestione API con i nomi host predefiniti, è possibile configurare nomi di dominio personalizzati per tutti gli endpoint di servizio come mostrato di seguito

![Configurazione di un dominio personalizzato per Gestione API][api-management-custom-domain-name]

È quindi possibile creare record A nel server DNS per accedere a questi endpoint. Si noti che gli endpoint sono accessibili solo dall'interno della rete virtuale.

## <a name="related-content"> </a>Contenuti correlati
* [Problemi di configurazione di rete comuni durante la configurazione di Gestione API in una rete virtuale][Common Network Configuration Issues]
* [Domande frequenti sulle reti virtuali](../virtual-network/virtual-networks-faq.md)
* [Creazione di un record A in DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues

